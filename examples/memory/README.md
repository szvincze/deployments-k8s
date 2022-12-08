# Memory examples

Memory example contains setup and tear down logic with default NSM infrastructure and memory based registry backend.

## Requires

- [spire](../spire)

## Includes

- [Memif to Memif Connection](./Memif2Memif)
- [Kernel to Kernel Connection](./Kernel2Kernel)
- [Kernel to VXLAN to Kernel Connection](./Kernel2Vxlan2Kernel)

## Run

1. Deploy NSM Operator:
```bash
kubectl apply -k ../../apps/nsm-operator
```

2. Wait for NSM Operator pod status ready:
```bash
kubectl wait -n nsm --timeout=1m --for=condition=ready pod -l control-plane=nsm-operator
```

3. Apply NSM resources for basic tests:

```bash
kubectl apply -f nsm-memory-sample.yaml
```

4. Wait for sample NSM pods status ready:
```bash
kubectl wait -n nsm --timeout=1m --for=condition=ready pod -l app=forwarder
kubectl wait -n nsm --timeout=1m --for=condition=ready pod -l app=nsmgr
kubectl wait -n nsm --timeout=1m --for=condition=ready pod -l app=nsm-registry
WH=$(kubectl get pods -l app=admission-webhook-k8s -n nsm --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
kubectl wait --for=condition=ready --timeout=1m pod ${WH} -n nsm
```

## Cleanup

To free resources follow the next commands:

Delete the webhook:
```bash
WH=$(kubectl get pods -l app=admission-webhook-k8s -n nsm --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
kubectl delete mutatingwebhookconfiguration ${WH}
```

Delete the NSM custom resource:
```bash
kubectl delete -n nsm nsm nsm-memory-sample
```

Delete NSM operator and all its dependencies:
```bash
kubectl delete -k ../../apps/nsm-operator
```
