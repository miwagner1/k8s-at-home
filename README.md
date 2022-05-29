# k8s-at-home (Work in Progress)
Kubernetes at home with K3s


Depnds on longhorn for storage, traefik for the ingress and cert-manager to manage the TLS certificates. 

I am new to Kubernetes, so I imagine there are a lot of improvements left to be done with the configurations.

## Air-Gap so we don’t need the internet when the nodes are rebooted for updates /loose power etc
(Work in progress)  
**To do:**  
Docker Registry on each node  
Convert helm carts to manafests with the template command (include CRDs)  
Airgap install k3s  
Use https://hub.docker.com/r/joxit/docker-registry-ui to help us manage the registries 


## Install k3s
read through https://rancher.com/docs/k3s/latest/en/installation/ and make sure you meet the installation requirements.

## install Helm 3
I now use https://github.com/k3s-io/helm-controller to deploy my helm dependent installs which is also apart of k3s.

I am going to use MetalLB as my service load balancer instead of the built in Klipper Load Balancer. I also disabled the built in Traefik deployment and use my own configuration.
```curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC=“--disable servicelb --disable traefik” sh -```
Follow the rest if the k3s docs for other options and how to add a node to the cluster.

Go through the files in k8s and adjust for your environment.
For example: set the ip ranges for metallb, set your email and cloudfare api key or use another provider with cert-manager etc.
```
sudo kubectl apply -k k8s/metallb
sudo kubectl apply -k k8s/cert-manager
sudo kubectl apply -k k8s/traefik
sudo kubectl apply -k k8s/longhorn
sudo kubectl apply -k k8s/authelia
```
for kustomize deployments with helm....
```
# view yaml manifest first
kubectl kustomize --enable-helm  
I am replacing this with https://github.com/k3s-io/helm-controller

# apply to kubernetes cluster
# 'apply' command does not have enable-helm flag
kubectl kustomize --enable-helm | kubectl apply -f -
```

Redis host for authelia is redis.authelia.svc.cluster.local


authelia will err out untill you eddit the config file on the longhorn storage (to do, change to a configmap)
## Start Deploying The Apps You Want
Do not forget to review and change the configs as needed.
deploy an app with ```sudo kubectl apply -k stacks/appnamehere```

### to do
Make dev deployments for local vm development.

## Edit files inside longhorn volume
I chose ssh due to better security than samba and because mounting the samba share on iOS had issues of files/folders disappearing/appearing needing to refresh the directory to show everything etc…  
Apps used on iOS:  
[‎SSH Client - Files & Terminal on the App Store](https://apps.apple.com/us/app/ssh-client-files-terminal/id1336634154)  
[‎Textastic Code Editor on the App Store](https://apps.apple.com/us/app/textastic-code-editor/id1049254261)
### Home assistant
SSH port 4005  
IP is the Traefik ingress ip from MetalLB 

### Manual
Get longhorn volume name from the longhorn dashboard and note what node its attached to
On the node the volume is attached to
```lsblk -f```
Cd to the dir that matches

## Manage your Kubernetes cluster on the go
I have been using kubenav to manage my cluster through my wireguard vpn on my phone.
https://kubenav.io
