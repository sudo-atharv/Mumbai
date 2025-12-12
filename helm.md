**Helm Chart Tutorial**
Pre-requisites **-** One running Kubernetes cluster \& Install kubectl



**Install kubectl**
#curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
#sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
#kubectl version --client

**Install Helm**
#sudo apt-get install curl gpg apt-transport-https --yes

\#curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

\#echo "deb \[signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

\#sudo apt-get update

\#sudo apt-get install helm
#helm version



Basic Helm CLI Commands
#helm install <RELEASE\_NAME> <CHART\_NAME>
#helm delete <RELEASE\_NAME>
#helm list -a
#helm create helloworld

\#tree helloworld

helloworld

├── charts

├── Chart.yaml

├── templates

│  ├── deployment.yaml

│  ├── \_helpers.tpl

│  ├── hpa.yaml

│  ├── ingress.yaml

│  ├── NOTES.txt

│  ├── serviceaccount.yaml

│  ├── service.yaml

│  └── tests

│      └── test-connection.yaml

└── values.yaml



\#vim helloworld/values.yaml
service:

  type: NodePort

  port: 80

:wq



\#helm install myhelloworld helloworld
#helm list -a

\#kubectl get service

Now browse ip:<port\_no> in browser it will show default nginx page





**add Helm Chart repository**

\#helm repo add bitnami https://charts.bitnami.com/bitnami

\#helm search repo bitnami

\#helm repo list



**update Helm Chart repository**

\#helm repo update



**index Helm Chart repository**

The index command can be used for generating the index file of given directory which contains the packaged charts.

\#helm repo index helloworld (This command should create index.yaml inside your packaged charts directory.)

\#cat helloworld/index.yaml



**remove Helm Chart repository**

\#helm repo remove bitnami



**Helm upgrade**
Upgrade helm chart after update anything into it. It will change revision no.

\#helm upgrade myhelloworld helloworld



**Helm Rollback**

Rollback the helm chart. This will change revision no.

\#helm rollback myhelloworld 1



**key benfits of using Helmfile** 

1.You can bundle several Helm Charts into a Single Helmfile to manage your kubernetes eco system

2.Helmfile helps you to keep isolation between the different environments(developemnt, staging, production)

3.It can help you to identify the differences between the new changes which you want to apply against the existing running deployment inside kubernetes cluster

4.Helmfile uses the Go Templates which lets you templatify your Helmfile and also you can use Sprig Library with functions - requiredEnv, exec, readFile, toYaml, fromYaml, setValueAtPath, get, tpl, required, fetchSecretValue, expandSecretRefs

5.With the help of HelmFile you can deploy multi-tier applications inside kubernetes cluster.



**How to Install Helmfile and running docker container of helmfile?**
#wget https://github.com/roboll/helmfile/releases/download/v0.144.0/helmfile\_linux\_amd64  (Linux base OS)

\#mv helmfile\_linux\_amd64 helmfile

\#chmod 777 helmfile 

\#sudo mv helmfile /usr/local/bin
#helmfile -version



**Create your first Helmfile and install Helmchart**

\#helm create helloworld

\#vim helmfile

---

releases:



&nbsp; - name: helloworldrelease         #(release name)

&nbsp;   chart: ./helloworld       #(Directory/Chart name)

&nbsp;   installed: true     

:wq


\#helmfile sync

\#helm list -a
Ctrl + Shift + V


**Uninstall the helmchart using Helmfile**
#vim helmfile
---

releases:



&nbsp; - name: helloworldrelease

&nbsp;   chart: ./helloworld

&nbsp;   installed: false

:wq



\#helmfile sync
#helm list -a



**Use GitHub repository for installing helm chart using Helmfile**
you can even install the helmchart from remote repository such as GitHub, Google Container Repository, AWS ECR
#> helmfile (clear helmfile)

\#vim helmfile

---

repositories:

&nbsp; - name: helloworld

&nbsp;   url: git+https://github.com/rahulwagh/helmchart@helloworld?ref=master\&sparse=0



releases:

&nbsp; - name: helloworldreleas

&nbsp;   chart: helloworld/helloworld

&nbsp;   installed: true 



:wq

#helmfile sync
#helm list -a



**Deploy multiple Helmcharts using Helmfile**
1. Create your first helm chart 
#helm create helloworld1

2\. Create your second helm chart

\#helm create helloworld2

3\. Create a Helmfile for deploying multiple helmchart .i.e. helloworld1, helloworld2

\#vim helmfile
---

releases:

&nbsp; - name: helloworld1        #release name

&nbsp;   chart: ./helloworld1     #chart name

&nbsp;   installed: true



&nbsp; - name: helloworld2        #release name

&nbsp;   chart: ./helloworld2     #chart name

&nbsp;   installed: true


:wq



\#helmfile sync

**Add 'bitnami/wordpress' to your repo list of Helm Chart**
#helm repo list

\#helm repo add bitnami https://charts.bitnami.com/bitnami

\#helm repo list








































