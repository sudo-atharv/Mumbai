
# Helm Chart Tutorial

## Pre-requisites
- One running Kubernetes cluster
- Install `kubectl`


## Install kubectl

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

## Install Helm

```sh
sudo apt-get install curl gpg apt-transport-https --yes
curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
helm version
```

## Basic Helm CLI Commands

```sh
helm install <RELEASE_NAME> <CHART_NAME>
helm delete <RELEASE_NAME>
helm list -a
helm create helloworld
```

## Helm Chart Directory Structure
```sh
tree helloworld
```

```
helloworld
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```



## Edit `values.yaml`

```yaml
# helloworld/values.yaml
service:
  type: NodePort
  port: 80
```



## Install and List Helm Release

```sh
helm install myhelloworld helloworld
helm list -a
kubectl get service
```

Now browse `ip:<port_no>` in your browser to see the default nginx page.



## Add Helm Chart Repository

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo bitnami
helm repo list
```



## Update Helm Chart Repository

```sh
helm repo update
```



## Index Helm Chart Repository

The `index` command generates the index file for a directory containing packaged charts.

```sh
helm repo index helloworld
cat helloworld/index.yaml
```


## Remove Helm Chart Repository

```sh
helm repo remove bitnami
```


## Helm Upgrade

Upgrade a Helm chart after making changes. This will change the revision number.

```sh
helm upgrade myhelloworld helloworld
```

## Helm Rollback

Rollback the Helm chart to a previous revision.

```sh
helm rollback myhelloworld 1
```


## Key Benefits of Using Helmfile

1. Bundle several Helm Charts into a single Helmfile to manage your Kubernetes ecosystem.
2. Maintain isolation between different environments (development, staging, production).
3. Identify differences between new changes and existing deployments in your Kubernetes cluster.
4. Use Go Templates in Helmfile, with Sprig library functions like `requiredEnv`, `exec`, `readFile`, `toYaml`, `fromYaml`, `setValueAtPath`, `get`, `tpl`, `required`, `fetchSecretValue`, `expandSecretRefs`.
5. Deploy multi-tier applications inside your Kubernetes cluster with Helmfile.



# Helmfile Installation and Usage Guide

## Install Helmfile (Linux)

```sh
wget https://github.com/roboll/helmfile/releases/download/v0.144.0/helmfile_linux_amd64
mv helmfile_linux_amd64 helmfile
chmod 777 helmfile
sudo mv helmfile /usr/local/bin
helmfile -version
```


## Create Your First Helmfile and Install a Helm Chart

1. Create a Helm chart:
    ```sh
    helm create helloworld
    ```

2. Create a `helmfile.yaml`:
    ```yaml
    releases:
      - name: helloworldrelease   # release name
        chart: ./helloworld       # directory/chart name
        installed: true
    ```

3. Sync with Helmfile:
    ```sh
    helmfile sync
    helm list -a
    ```


## Uninstall the Helm Chart Using Helmfile

1. Edit `helmfile.yaml` to set `installed: false`:
    ```yaml
    releases:
      - name: helloworldrelease
        chart: ./helloworld
        installed: false
    ```

2. Sync again:
    ```sh
    helmfile sync
    helm list -a
    ```


## Use GitHub Repository for Installing Helm Chart with Helmfile

1. Edit `helmfile.yaml`:
    ```yaml
    repositories:
      - name: helloworld
        url: git+https://github.com/rahulwagh/helmchart@helloworld?ref=master&sparse=0

    releases:
      - name: helloworldrelease
        chart: helloworld/helloworld
        installed: true
    ```

2. Sync:
    ```sh
    helmfile sync
    helm list -a
    ```


## Deploy Multiple Helm Charts Using Helmfile

1. Create charts:
    ```sh
    helm create helloworld1
    helm create helloworld2
    ```

2. Create a `helmfile.yaml`:
    ```yaml
    releases:
      - name: helloworld1
        chart: ./helloworld1
        installed: true

      - name: helloworld2
        chart: ./helloworld2
        installed: true
    ```

3. Sync:
    helmfile sync
    ```


## Add 'bitnami/wordpress' to Your Helm Repo List

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list
```










































