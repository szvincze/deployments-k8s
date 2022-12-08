# Basic examples

Contain basic setup for NSM that includes `nsmgr`, `forwarder-vpp`, `registry-k8s`. This setup can be used to check mechanisms combination or some kind of NSM [features](../features).

## Requires

- [spire](../spire)

## Includes

- [Memif to Memif Connection](../use-cases/Memif2Memif)
- [Kernel to Kernel Connection](../use-cases/Kernel2Kernel)
- [Kernel to Memif Connection](../use-cases/Kernel2Memif)
- [Memif to Kernel Connection](../use-cases/Memif2Kernel)
- [Kernel to VXLAN to Kernel Connection](../use-cases/Kernel2Vxlan2Kernel)
- [Memif to VXLAN to Memif Connection](../use-cases/Memif2Vxlan2Memif)
- [Kernel to VXLAN to Memif Connection](../use-cases/Kernel2Vxlan2Memif)
- [Memif to VXLAN to Kernel Connection](../use-cases/Memif2Vxlan2Kernel)
- [Kernel to Wireguard to Kernel Connection](../use-cases/Kernel2Wireguard2Kernel)
- [Memif to Wireguard to Memif Connection](../use-cases/Memif2Wireguard2Memif)
- [Kernel to Wireguard to Memif Connection](../use-cases/Kernel2Wireguard2Memif)
- [Memif to Wireguard to Kernel Connection](../use-cases/Memif2Wireguard2Kernel)

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
kubectl apply -f nsm-basic-sample.yaml
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
kubectl delete -n nsm nsm nsm-basic-sample
```

Delete NSM operator and all its dependencies:
```bash
kubectl delete -f ../../apps/nsm-operator/nsm-operator.yaml
```
