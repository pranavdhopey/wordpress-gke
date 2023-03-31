## Configure a GKE Cluster with Workload Identity Feature Enabled ##
## Configure a CloudSQL Mysql 8 instance from Google Cloud Console ##

## Deploy Nginx Ingress Controller ##

Ref: https://kubernetes.github.io/ingress-nginx/deploy/#gce-gke

```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```


## Deploy Cert Manger CRD's on Cluster ##
Ref: https://cert-manager.io/docs/installation/kubectl/

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```


## Create Namespace and ServiceAccount ##

```
kubectl create ns wordpress
kubectl create sa wordpress-sa -n wordpress
```


## Create IAM Service Account ##

```
SA_NAME=cloudsql-proxy
gcloud iam service-accounts create $SA_NAME --display-name $SA_NAME


SA_EMAIL=$(gcloud iam service-accounts list \
    --filter=displayName:$SA_NAME \
    --format='value(email)')
```


## Assign a Cloud SQL Client Role to IAM SA ##

```
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --role roles/cloudsql.client \
    --member serviceAccount:$SA_EMAIL
```


## Do workload Identity IAM binding with K8S SA ##

```
gcloud iam service-accounts add-iam-policy-binding $SA_EMAIL \
    --role roles/iam.workloadIdentityUser \
    --member "serviceAccount:PROJECT_ID.svc.id.goog[wordpress/wordpress-sa]"
```	


## Annotate K8S SA with IAM SA ##

```    
kubectl.exe annotate sa wordpress-sa -n wordpress iam.gke.io/gcp-service-account=$SA_EMAIL
```


### Update the Secrets in wordpress-secrets file as per the CloudSQL Configuartion ###

```
kubectl create secret generic wordpress-secrets --from-env-file=wordpress-secrets --dry-run=client -o yaml > 3-Wordpress-secrets.yaml
```


## Create Static IP for Load Balancer ##

```
gcloud compute addresses create wordpress-ip --global
```

### Create a DNS entry for IP address created above  ###


## Apply the Manifest from 1-* to 5-* ##

```
kubectl apply -n wordpress -f 1-StorageClass.yaml -f 2-WordpressPVClaim.yaml -f 3-Wordpress-secrets.yaml -f 4-Wordpress-Deployment-CloudSQL.yaml -f 5-Wordpress-service.yaml
```

## Update and Apply Issuer and Certificate Manifest ##

```
kubectl apply -n wordpress -f 6-Issuer.yaml -f 7-LetsEncryptCertificate.yaml
```

## Update and Apply Frontend Config and Ingress ##

```
kubectl apply -n wordpress -f Ingress.yaml
```