# Kubernetes Network

## Port Forwarding / Reach Pod inside network

If you want to reach a service inside the pod you can simply forward the port to your local machine. This makes the need for public ingress obsolete at least for some cases.

``` bash
kubectl --namespace {{ NAMESPACE_NAME }} port-forward svc/{{ SVC_NAME }} 9090:9090
```

Sources:

- <https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/>

## Get Pods DNS name / IP address

Currently when a pod is created, its hostname is the Pod's metadata.name value. The Pod spec has an optional hostname field, which can be used to specify the Pod's hostname.

``` bash
{{ POD_NAME }}.my-namespace
```

Please note: In most cases you want to reference the service DNS record since that is the actual entrypoint to your Pod.

``` bash
service-name.my-namespace
```

Note however that using the namespace is not necessary if pods/services are in the same namespace.

Container in the same pod share a virtual network and thus simply use "127.0.0.1" if they want to reach each other.

Sources:

- <https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-hostname-and-subdomain-fields>
- <https://stackoverflow.com/a/59558862>

## NodePort and port on Hosts and how to use port outside standard range

Using the following adjusted start commands to /etc/systemd/system/multi-user.target.wants/k3s.service

``` bash
--service-node-port-range 53-54
```

will allow us to use ports 53-54. Now using a nodeport will reroute all traffic on all nodes incoming to port 53 to our service. We need to use the nodePort parameter to insure a specific port is used.

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: adguard-adguard-home-dns
spec:
  selector:
    app.kubernetes.io/name: adguard-home
    app.kubernetes.io/instance: adguard
  type: NodePort
  ports:
    - port: 53
      name: dns-tcp
      targetPort: dns-tcp
      nodePort: 53
      protocol: TCP
```

With this it is not possible to use multiple ranges. However that seems to be possible but was not tested(see sources).

Sources:

- <https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0>
- <https://alesnosek.com/blog/2017/02/14/accessing-kubernetes-pods-from-outside-of-the-cluster/>
- <https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport>
- <https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/>
- <https://stackoverflow.com/a/59679905>

## Install ingress controller nginx

altered the default command to

``` bash
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

Otherwise it would create nginx in the wrong namespace(default)

- <https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal>

Sources:

- <https://rancher.com/docs/k3s/latest/en/installation/install-options/#options-for-installation-with-script>
- <https://helm.sh/docs/intro/install/>
- <https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management>
- <https://rancher.com/docs/k3s/latest/en/cluster-access/>
- <https://stackoverflow.com/questions/46360361/invalid-x509-certificate-for-kubernetes-master>
- <https://github.com/k3s-io/k3s/issues/214#issuecomment-482388852>
- <https://stackoverflow.com/questions/51783651/how-to-create-a-namespace-if-it-doesnt-exists-from-helm-templates>

## How to install Cert-manager

Sources:

- <https://cert-manager.io/docs/installation/kubernetes/>

## Default Ingress

``` yaml
ingressclass.kubernetes.io/is-default-class
```

Sources:

- <https://kubernetes.github.io/ingress-nginx/#i-have-only-one-ingress-controller-in-my-cluster-what-should-i-do>

## Protect ingress with basic auth

Add annotations to ingress:

``` yaml
nginx.ingress.kubernetes.io/auth-type: basic
nginx.ingress.kubernetes.io/auth-secret: basic-auth
nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required '
```

basic-auth is the name of our secret which contains the basic auth. The secret needs to contain a key "auth" which contains the md5 string.

Sources:

- <https://longhorn.io/docs/1.1.2/deploy/accessing-the-ui/longhorn-ingress/>

## Protect images with oauth2

Use the predefined oauth2 service and keycloak application json. The following annotation can be used to protect services:

``` yaml
nginx.ingress.kubernetes.io/auth-url: "https://{{ SSO_URL }}/oauth2/auth"
nginx.ingress.kubernetes.io/auth-signin: "https://{{ SSO_URL }}/oauth2/start?rd=$scheme://$best_http_host$request_uri"
nginx.ingress.kubernetes.io/auth-response-headers: "x-auth-request-user, x-auth-request-email, x-auth-request-access-token"
acme.cert-manager.io/http01-edit-in-place: "true"
nginx.ingress.kubernetes.io/proxy-buffer-size: "16k"
```

Sources:

- <https://www.talkingquickly.co.uk/webapp-authentication-keycloak-OAuth2-proxy-nginx-ingress-kubernetes>
- <https://github.com/oauth2-proxy/manifests/tree/main/helm/oauth2-proxy>
- <https://www.talkingquickly.co.uk/installing-keycloak-kubernetes-helm>

## Use configuration snippets to add custom headers

``` yaml
nginx.ingress.kubernetes.io/configuration-snippet: |-
  proxy_set_header X-Remote-User $remote_user;
```

Sources:

- <https://kubernetes.github.io/ingress-nginx/examples/customization/configuration-snippets/>
