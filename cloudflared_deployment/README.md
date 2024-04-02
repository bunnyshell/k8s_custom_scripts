# Basic Manifests to Install Cloudflare Tunnel in a Kubernetes cluster 

This will deploy Clouflared configured to route traffic to *the Ingress controller*.
The deployment needs the configmap and a secret.

### Create the tunnel

```bash

cloudflared tunnel create cluster-ingress-eks129

Tunnel credentials written to ~/.cloudflared/xxxxxx.json. cloudflared chose this file based on where your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel.

Created tunnel ______________________ with id _________________

``` 

### Create/Set Kubernetes namespace

### Read the credentials from the tunnel config file

Read the tunnel credentials file ` ~/.cloudflared/xxxxxx.json.`, extract variables:

```bash
export CF_ACCOUNT_TAG=missing_tag
export CF_TUNNEL_ID=missing_id
export CF_TUNNEL_SECRET=missing_secret
export CF_TUNNEL_NAME=missing_name
```

### Create the secret

```bash
jq -cn --arg accountTag $CF_ACCOUNT_TAG \
 --arg tunnelID $CF_TUNNEL_ID \
 --arg tunnelName $CF_TUNNEL_NAME \
 --arg tunnelSecret $CF_TUNNEL_SECRET \
 '{AccountTag: $accountTag, TunnelID: $tunnelID, TunnelName: $tunnelName, TunnelSecret: $tunnelSecret}' | \
kubectl create secret generic tunnel-creds --from-file=creds.json=/dev/stdin
```

### Deploy

```bash
kubectl apply -f configmap.yaml
kubectl apply -f deployment.yaml
```