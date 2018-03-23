# Whitelist ingress traffic to Kubernetes
## Example of a Jenkins deployment with ClusterIP using Traefik ingress controller to whitelist specific IP's

These instructions enable to create a Jenkins deployment with ClusterIP (Not exposed via LoadBalancer) and expose it to selected public IP's via Traefik ingress controller.

### Install Jenkins with ClusterIP service
`$ helm install jenkins-release --set Master.ServiceType=ClusterIP stable/jenkins`

### Install Traefik
`$ helm install stable/traefik --name traefik-release --namespace kube-system`

### Patch Traefik service to enable X-Forwarded public IP's
`$ kubectl patch service traefik-release-traefik  -n kube-system --patch $'spec:\n externalTrafficPolicy: Local\n'`

### Edit ingress.yaml 
Set the public ip's or range to whitelist. 

Single IP: `ingress.kubernetes.io/whitelist-source-range: "64.18.13.199/32"`

Multiple IP ranges (Cloudflare for example): 
`ingress.kubernetes.io/whitelist-source-range: "103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,104.16.0.0/12,108.162.192.0/18,131.0.72.0/22,141.101.64.0/18,162.158.0.0/15,172.64.0.0/13,173.245.48.0/20,188.114.96.0/20,190.93.240.0/20,197.234.240.0/22,198.41.128.0/17"`

### Create Ingress controller with whitelist IP's
`$ kubectl create -f ingress.yaml`
