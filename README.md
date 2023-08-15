This repo demonstrates maintaing multi serverless workflow definitions, using kustomize and possibly adding argocd

Structure:
```
kustomiztion.yaml        // the base kustomization yaml
flow-x:                  // per-flow directory
  sonataflow.yaml        // sonataflow CR
  configmap.yaml         // the sonataflow configmap (the target quarkus application.properties)
  specs/                 // specs dirs for openapi specs
    foo.json             // some service's open-api spec
  kustomization.yaml     // per-flow kustomization
```

Run kustomize to get all the manifests:
```
kustomize build .
```

Deploy this on a cluster using by kubectl apply or with with an argocd application from this directory.
