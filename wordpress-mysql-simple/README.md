## Configure a GKE Cluster on Google Cloud ##

## Create Namespace ##

```
kubectl create ns wordpress
```

### Update the Secrets in wordpress-secrets file as per the CloudSQL Configuartion ###

## Apply the Manifest from 1-* to 9-* ##
```
kubectl apply -n wordpress -f <filename>.yaml
```
