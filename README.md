This repo demonstrates maintaing multi serverless workflow definitions, using kustomize and possibly adding argocd

Structure:
```
kustomiztion.yaml        // the base kustomization yaml
flow-x:                  // per-flow directory
  sonataflow.yaml        // sonataflow CR
  configMap.yaml         // the sonataflow's configMap (the target quarkus application.properties)
  specs/                 // specs dirs for openapi specs
    svc-openapi.yaml     // some service's open-api spec
  kustomization.yaml     // per-flow kustomization
```

Run kustomize to get all the manifests:
```
kustomize build .
```

Deploy using kustomize only:
```
kusttomize build . | kubectl apply -f -`
```

Or argocd application for a more managed approach:
```
argocd app create workflows --repo https://github.com/rgolangh/sonataflow-argocd-app --path ./ --dest-server https://kubernetes.default.svc --dest-namespace workflows 

argocd sync workflows 
```

Expose the flows to interact with them incase you deploy on `kind` or any other luster lacking ingress capability:
```
kubectl port-forward -n workflows svc/greeting-a 8080:80 &
```



