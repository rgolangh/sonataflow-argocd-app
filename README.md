This repo demonstrates maintaing multi serverless workflow definitions, using kustomize and possibly adding argocd

## Structure:
```
kustomiztion.yaml        // the base kustomization yaml
flow-x:                  // per-flow directory
  sonataflow.yaml        // sonataflow CR
  configMap.yaml         // the sonataflow's configMap (the target quarkus application.properties)
  specs/                 // specs dirs for openapi specs
    svc-openapi.yaml     // some service's open-api spec
  kustomization.yaml     // per-flow kustomization
```

## Prerequisites
A k8s cluster, I used kind with podman.
And sonata serverless operator:
```
kubectl create -f https://raw.githubusercontent.com/kiegroup/kogito-serverless-operator/v1.42.0/operator.yaml
```

## From git to deployed workflows

* **Namespace:**
Create the 'workflows' namespace:
```
kubectl create namespace workflows
```

* **Kustomize usage:**
Run kustomize to get all the manifests:
```
kustomize build .
```

Deploy using kustomize only:
```
kustomize build . | kubectl apply -f - --namespace workflows
```

* **Argocd Usage:**
Alternatively use argocd application for a more managed approach:
```
argocd app create workflows --repo https://github.com/rgolangh/sonataflow-argocd-app --path ./ --dest-server https://kubernetes.default.svc --dest-namespace workflows 

argocd app sync workflows 
```

* **Automated Sync:**
To avoid manual syncs, tweak the app sync policy:
```
argocd app set workflows --sync-policy automated
```

* **Invoke a workflow:**
Expose the flows to interact with them incase you deploy on `kind` or any other luster lacking ingress capability:
```
kubectl port-forward -n workflows svc/mta 8080:80 &

curl localhost:8080/mta -d '{"foo":"bar"}' -H "Content-Type: application/json"
```

