... purpose and features ???
see example values.yaml

# Cluster setup
In both GKE/Standard case, it is assumed that you have already installed a basic cluster with generic features

## GKE
- Declare some environment variables used later\
  ```export CLUSTER_NAME=...```\
  ```export ZONE=...```\
  ```export PROJECT=...```\

- Initialize GKE environment\
  ```gcloud container clusters get-credentials $CLUSTER_NAME --zone $ZONE --project $PROJECT```\

- Associate external IP address/es
  ```export ENV=...```\
  for each environments to deploy:\
  ```gcloud compute addresses create $ENV-ip --global```\
  or cluster wide:\
  ```gcloud compute addresses create global-ip --global```\

- Check addresses FYI\
  ```gcloud compute addresses list```

### Setup plugin needed by cert-manager (if enabled)
```gcloud components install gke-gcloud-auth-plugin```

### Setup cluster-wide disk provisioning
```gcloud container clusters update $CLUSTER_NAME --update-addons=GcePersistentDiskCsiDriver=ENABLED --zone=$ZONE```

## Standard
- Create a mountpoint for local persistent volume\
- Provision ext IP ???


# Configure your DNS
It is assumed you are using an external DNS service, or know how to manage DNS resources from your k8s cluster\
...create records A


# Configure values.yaml
- Set cluster.isGKE boolean being either GKE or not (say standard)
- Set cert-manager properties\
  set cluster.cert-manager.enabled boolean\
  set cluster.cert-manager.namespace\
  set cluster.cert-manager custom options\
  set cluster.letsEncryptAccountEmail\
- Set cluster.logsStorageSize\
- Set environment/s properties; foreach environment to deploy:\
  set name\
  set ipName: $ENV-ip\
  or
  set ipName: global-ip\
  set name/hostName/timezonePath etc\
  set at least one URLs, see example values.yaml\
  set optional nginxResources / syslogResources\
  set enabled boolean

## GKE
- Set cluster.zone

## Standard
- Set cluster.pvHostPath


# Install Helm Chart
```helm install helm-url-logger ./ -f values.yaml --namespace url-logger```


# Uninstall Helm Chart
```helm uninstall helm-url-logger --namespace url-logger```


# Cluster cleanup

## GKE
- Unprovision persistent volume ???\
  ```...```\
- Delete the domain name\
  ```export ZONE=...```\
  for each environments deployed:\
  ```export DOMAIN_NAME=...```\
  ```gcloud dns record-sets delete $DOMAIN_NAME --zone $ZONE --type A```\
- Delete the static IP address/es\
  ```export $ENV-ip=...```\
  for each enviroments deployed:\
  ```gcloud compute addresses delete $ENV-ip --global```\
  or cluster wide:\
  ```gcloud compute addresses delete global-ip --global```

## Standard
- Remove your local persistent volume directory\
- Unprovision your domain name/s resources such as LoadBalancer ???\
- Unprovision your environments/global IP address/es ???
