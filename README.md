# How to reproduce

```
git clone https://github.com/patrick-bug-repro/tilt-kustomize.git
tilt up
```

in another terminal:

```
kubectl get configmap config-domain -n knative-serving -o yaml | grep patrick.space
```
