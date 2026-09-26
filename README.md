# omega-spiffe-csi-driver

Deploys the SPIFFE CSI Driver DaemonSet (+ `node-driver-registrar` sidecar)
and the cluster-scoped `CSIDriver` object for `csi.spiffe.io`, so pods can
mount the SPIRE Agent's Workload API socket via an ephemeral inline volume
instead of a hostPath.

Must be deployed alongside `omega-spire-agent`, in the **same namespace**,
with `agentSocketDir` matching that chart's agent socket hostPath exactly
(both default to `/run/secrets/workload-spiffe-uds`).

The `CSIDriver` object is cluster-scoped and singleton by name
(`csi.spiffe.io`) - only install this chart once per cluster.

## Install

```sh
helm install spiffe-csi-driver ./omega-spiffe-csi-driver \
  --namespace spire --create-namespace
```

## Values

| Key | Default | Description |
|---|---|---|
| `image.repository` / `.tag` | `ghcr.io/spiffe/spiffe-csi-driver` / `0.2.6` | CSI driver image |
| `nodeDriverRegistrar.image.repository` / `.tag` | `registry.k8s.io/sig-storage/csi-node-driver-registrar` / `v2.6.0` | Registrar sidecar image |
| `agentSocketDir` | `/run/secrets/workload-spiffe-uds` | Host path shared with `omega-spire-agent`'s socket hostPath |
| `tolerations` | control-plane `NoSchedule` toleration | So the driver also runs on control-plane nodes |
