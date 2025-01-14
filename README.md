# furbar
cka projects follow along

```
 helm install cilium cilium/cilium --version 1.16.1 \
                                   --namespace kube-system \
                                   --set tunnel=disabled \
                                   --set bpf.masquerade=false \
                                   --set prometheus.enabled=true \
                                   --set hubble.enabled=true \
                                   --set hubble.metrics.enabled="{dns,drop,tcp,flow,port-distribution,icmp,http}" \
                                   --set hubble.relay.enabled=true \
                                   --set hubble.ui.enabled=true \
                                   --set hubble.peer.target="hubble-peer.kube-system.svc.cluster.local:4244" \
                                   --set directRoutingDevice=eth0
```

idpbuilder create --build-name local --kind-config cluster.yaml

helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
