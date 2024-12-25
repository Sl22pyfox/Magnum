```
service:
  type: NodePort
additionalArguments:
  - "--serversTransport.insecureSkipVerify=true"
deployment:
  replicas: 3
ports:
  web:
    nodePort: 30080
    forwardedHeaders:
      trustedIPs: ["10.90.90.48","192.168.0.0/16"]
      insecure: true
    proxyProtocol:
      trustedIPs: ["10.90.90.48","192.168.0.0/16"]
      insecure: true
  websecure:
    expose:
      default: false
ingressRoute:
  dashboard:
    enabled: true
    matchRule: Host(`test-portal-traefik.magnum.kz`)
    entryPoints: ["web"]
    middlewares:
      - name: traefik-dashboard-auth
extraObjects:
  - apiVersion: v1
    kind: Secret
    metadata:
      name: traefik-dashboard-auth-secret
    type: kubernetes.io/basic-auth
    stringData:
      username: admin
      password: xxXX1234
  - apiVersion: traefik.io/v1alpha1
    kind: Middleware
    metadata:
      name: traefik-dashboard-auth
    spec:
      basicAuth:
        secret: traefik-dashboard-auth-secret
```