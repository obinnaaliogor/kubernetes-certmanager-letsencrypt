We will automate the creation of ssl/tls certificate in kubernetes using letsencrypt.
Reference <https://letsencrypt.org/docs/>

helm repo add jetstack <https://charts.jetstack.io>

helm repo add ingress-nginx <https://kubernetes.github.io/ingress-nginx>

helm repo update ingress-nginx

helm search repo ingress-nginx

nginx ingress controller github repo:
<https://github.com/kubernetes/ingress-nginx/blob/main/README.md#readme>

NGINX_CHART_VERSION="4.8.4"

helm install ingress-nginx ingress-nginx/ingress-nginx \
    --version $NGINX_CHART_VERSION \
    -f "nginx-values-v${NGINX_CHART_VERSION}.yaml" \
    --namespace ingress-nginx \
    --create-namespace

 helm upgrade ingress-nginx ingress-nginx/ingress-nginx \
     --version $NGINX_CHART_VERSION \
     -f "nginx-values-v${NGINX_CHART_VERSION}.yaml" \
     --namespace ingress-nginx \
     --create-namespace

curl -Li <http://quote.obinnaaliogor.xyz/>
curl -v -k <http://quote.obinnaaliogor.xyz/>

 kubernetes-certmanager-letsencrypt git:(main) ✗ curl -v -k <https://quote.obinnaaliogor.xyz/>

* Trying 54.159.230.234:443...
* Connected to quote.wiz-obi.com (54.159.230.234) port 443 (#0)
* ALPN: offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384
* ALPN: server accepted h2
* Server certificate:
* subject: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
* start date: Dec  9 01:51:19 2023 GMT
* expire date: Dec  8 01:51:19 2024 GMT
* issuer: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
* SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* using HTTP/2
* h2 [:method: GET]
* h2 [:scheme: https]
* h2 [:authority: quote.obinnaaliogor.xyz]
* h2 [:path: /]
* h2 [user-agent: curl/8.1.2]
* h2 [accept: */*]
* Using Stream ID: 1 (easy handle 0x12c00bc00)

> GET / HTTP/2
> Host: quote.wiz-obi.com
> User-Agent: curl/8.1.2
> Accept: */*
>
< HTTP/2 200
< date: Sat, 09 Dec 2023 02:24:10 GMT
< content-type: application/json
< content-length: 167
< strict-transport-security: max-age=15724800; includeSubDomains
<
{
    "server": "bouncy-orange-k607adm4",
    "quote": "Non-locality is the driver of truth. By summoning, we vibrate.",
    "time": "2023-12-09T02:24:10.339076835Z"

* Connection #0 to host quote.obinnaaliogor.xyz left intact
}%

...
<https://cert-manager.io/>

CERT_MANAGER_HELM_CHART_VERSION="1.13.2"

helm install cert-manager jetstack/cert-manager --version "$CERT_MANAGER_HELM_CHART_VERSION" \
  --namespace cert-manager \
  --create-namespace \
  -f cert-manager-values-v${CERT_MANAGER_HELM_CHART_VERSION}.yaml

  #validate the issuance of the cert.

kubectl get crd -l app.kubernetes.io/name=cert-manager
k get crd #deploying the cert manager created crds

TO know the ip addresses of the clients that made requests to our application.
For logging purposes and our application needs.
Then we have to enable proxy protocol on the ingress controller, this can be done on the ingress controllers value file.


➜  kubernetes-certmanager-letsencrypt git:(main) ✗ k replace --force -f ingressresource.yaml
ingress.networking.k8s.io "ingress-quote" deleted
ingress.networking.k8s.io/ingress-quote replaced
➜  kubernetes-certmanager-letsencrypt git:(main) ✗
➜  kubernetes-certmanager-letsencrypt git:(main) ✗

➜  kubernetes-certmanager-letsencrypt git:(main) ✗ k get orders.acme.cert-manager.io -n backend
NAME                             STATE     AGE
letsencrypt-nginx-1-3837422662   pending   4s
➜  kubernetes-certmanager-letsencrypt git:(main) ✗
➜  kubernetes-certmanager-letsencrypt git:(main) ✗
➜  kubernetes-certmanager-letsencrypt git:(main) ✗ k get issuers.cert-manager.io -n backend
NAME                READY   AGE
letsencrypt-nginx   True    3m5s
➜  kubernetes-certmanager-letsencrypt git:(main) ✗ k get orders.acme.cert-manager.io -n backend
NAME                             STATE   AGE
letsencrypt-nginx-1-3837422662   ready   21s
➜  kubernetes-certmanager-letsencrypt git:(main) ✗ k get certificaterequests.cert-manager.io -n backend
NAME                  APPROVED   DENIED   READY   ISSUER              REQUESTOR                                         AGE
letsencrypt-nginx-1   True                True    letsencrypt-nginx   system:serviceaccount:cert-manager:cert-manager   36s

#https://porkbun.com/account/domainsSpeedy?fo=1&oid=4866763
you can register a domain from the above site and change the authoritative ns <nameserver> with that of your cloud provider where your application or ELB was created eg. aws,gcp,digitalocean etc.
this was exactly what i did, follow this guide

Reference: https://dev.to/mubbashir10/point-your-domain-to-aws-using-route-53-42kf

Reference cert manager docs: https://cert-manager.io/docs/installation/upgrade/