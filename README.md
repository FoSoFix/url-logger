... purpose and features ???


# Configure values.yaml
- set cluster.isGKE
- cert-manager
  set cluster.cert-manager.enabled
  set cluster.cert-manager.namespace
  set cluster.cert-manager custom options
  set cluster.letsEncryptAccountEmail
- set cluster.logsStorageSize
- foreach environment to deploy
  set $ENV-ip or global-ip
  ...set name/hostName/timezonePath ecc
  ...set at least one URLs ???
  ...set optional nginxResources / syslogResources
  set enabled boolean

## GKE
- set cluster.zone

## Standard
- set cluster.pvHostPath


# Cluster setup
In both GKE/Standard case, it is assumed that you have already installed a basic cluster with generic features.

## GKE
- Declare some environment variables used later
  ```export CLUSTER_NAME=...```
  ```export ZONE=...```
  ```export PROJECT=...```

- Initialize GKE environment
  ```gcloud container clusters get-credentials $CLUSTER_NAME --zone $ZONE --project $PROJECT```

- for each env to deploy / cluster wide
  ```export ENV=...```
  ```gcloud compute addresses create $ENV-ip --global```
  or
  ```gcloud compute addresses create global-ip --global```

- check addresses FYI
  ```gcloud compute addresses list```

### Setup plugin needed by cert-manager (if enabled)
```gcloud components install gke-gcloud-auth-plugin```

### Setup cluster-wide disk provisioning
```gcloud container clusters update $CLUSTER_NAME --update-addons=GcePersistentDiskCsiDriver=ENABLED --zone=$ZONE```

## Standard
- create a mountpoint for local persistent volume
- provision ext IP ???


# Configure your DNS
It is assumed you are using an external DNS service, or know how to manage DNS resources from your k8s cluster.
...create records A


# Install Helm Chart
helm install helm-url-logger ./ -f values.yaml --namespace url-logger


# Uninstall Helm Chart
helm uninstall helm-url-logger --namespace url-logger


# Cluster cleanup

## GKE
- Unprovision persistent volume ???
  ```...```
- Delete the domain name.
  ```export ZONE=...```
  for each env deployed:
  ```export DOMAIN_NAME=...```
  ```gcloud dns record-sets delete $DOMAIN_NAME --zone $ZONE --type A```
- Delete the static IP address/es.
  for each env deployed / cluster wide:
  ```export ENV=...```
  ```gcloud compute addresses delete $ENV-ip --global```
  or
  ```gcloud compute addresses delete global-ip --global```

## Standard
- remove your local persistent volume directory
- unprovision your domain name/s resources such as LoadBalancer ???
- unprovision your environments/global IP address/es ???
