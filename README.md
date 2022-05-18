# Kubeflow demo

## Deploying a Kubernetes cluster with kind.

Install Kind following these instructions [here.](https://www.kubeflow.org/docs/components/pipelines/installation/localcluster-deployment/#1-installing-kind)

Clone the kubeflow_demo repo TODO: Insert URL

### Create the local multinode cluster

```bash
kind create cluster --name kubeflow --config multi-node.yaml
```

Set context using 


```bash
kubectl cluster-info --context kind-kubeflow
```

## Install kubeflow


```bash
while ! kustomize build example | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done

```

### Optional: Install k9s for a great K8s experience

Simply follow the instructions provided [here.](https://github.com/derailed/k9s#installation).

Go to services and find `ml-pipeline-ui`, then type shift+f

## Tear down 

```bash
kustomize build example | kubectl delete -f -;
```

Debugging led to [this](https://stackoverflow.com/questions/66989018/kubeflow-pipeline-fail-to-create-container):

### TODO: Do this right with kustomize

#### Change the configmap to run pipelines

You don't need to use docker. In fact the problem is with workflow-controller-configmap in kubeflow name space. You can edit it with
```bash
kubectl edit configmap workflow-controller-configmap -n kubeflow
```
and change containerRuntimeExecutor: docker to containerRuntimeExecutor: pns. Also you can change some of the steps and install kubeflow 1.3 in mutlitpass 1.21 rather than 1.15. Do not use kubelfow add-on (at least didn't work for me). You need kustomize 3.2 to create manifests as they mentioned in https://github.com/kubeflow/manifests#installation.

#### Change the config-domain 

You also need to change the config-domain so that you can curl from within the Kubernetes cluster with intracluster communications 

```bash
kubectl edit configmap config-domain -n knative-serving
```

```yaml 
apiVersion: v1
data:
  svc.cluster.local: ""

```





