## Configure a GKE Cluster on Google Cloud ##

## Create Namespace ##

```
kubectl create ns wordpress
```

### Update the Secrets in mysql-secrets and wordpress-secrets file as per your Configuartion ###
```
kubectl create secret generic mysql-secrets --from-env-file=mysql-secrets --dry-run=client -o yaml > 4-Mysql-secrets.yaml

kubectl create secret generic wordpress-secrets --from-env-file=wordpress-secrets --dry-run=client -o yaml > 7-Wordpress-secrets.yaml
```

## Create Static IP for Load Balancer ##
```
gcloud compute addresses create wordpress-ip --region <your-region>
```
### Update above created IP address in 9-Wordpress-service.yaml file ### 

## Apply the Manifest from 1-* to 9-* ##
```
kubectl apply -n wordpress -f <filename>.yaml
```
