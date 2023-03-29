## Configure a GKE Cluster on Google Cloud ##

## Create Namespace ##

```
kubectl create ns wordpress
```

### Update the Secrets in wordpress-secrets and mysql-secrets file as per your Configuartion ###

## Apply the Manifest from 1-* to 9-* ##
```
kubectl apply -n wordpress -f <filename>.yaml
```
