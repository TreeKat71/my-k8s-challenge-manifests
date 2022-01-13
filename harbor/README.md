# DigitalOcean Kubernetes Challenge - k8s manifests for ArgoCD

1. Generate your own certificate

    Ask your IT admin for the signing `.crt` stuff

2. (optional) Set DNS
    
    For testing purpose, you can simply add lines to `/etc/hosts`
    ```bash
    # Please fill your own node IP
    123.45.67.89 registry.k8s.cloud
    ```

3. Copy the `CA cert` to your machine

    This step is to make your docker-client trust that `certificate`
    ```bash
    # You may need to create the parent folder
    $ mkdir -p /etc/docker/certs.d/registry.k8s.cloud
    $ mv ca.crt /etc/docker/certs.d/registry.k8s.cloud/ca.crt
    ```
4. Create namespace for `Harbor`

    ```bash
    $ kubectl create namespace harbor
    ```

5. Create tls related `secret` for `harbor-helm`

- Fill in ca.crt, tls.crt, tls.key
    ```bash
    data:
      ca.crt:   ABC <-
      tls.crt:  ABC <-
      tls.key:  ABC <-
    ```

- Create tls secret
    ```bash
    $ kubectl apply -f tls-secret.yaml
    ```

6. Customize your own `values.yaml`

    ```bash
    expose.ingress.hosts.core: registry.k8s.cloud
    externalURL: https://registry.k8s.cloud
    harborAdminPassword: "password"
    secretKey: "16-char-key"
    persistence.enabled: true
    ```

7. Deploy `Harbor` with `Helm`
- Add helm repo and update it

    ```bash
    $ helm repo add harbor https://helm.goharbor.io
    $ helm repo update
    ```
- Deploy harbor by helm chart

    ```bash
    $ helm upgrade --install <release-name> harbor/harbor -f values-tls.yaml
    ```
