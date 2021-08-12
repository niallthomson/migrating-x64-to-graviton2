# Migrating an x64 EKS Cluster to Graviton2

## Steps

Create initial cluster:

```
eksctl create cluster -f cluster.yml
```

Get relevant endpoints:

```
todo
```

Add Graviton2 node group:

```
eksctl create nodegroup -f graviton2-nodegroup.yml
```

Add the following to `workload-1` configuration to migrate (`flux/apps/workload-1/release.yaml`):

```
spec:
  [...]
  values:
    [...]
    tolerations:
    - key: "graviton2"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
    nodeSelector:
      kubernetes.io/arch: arm64
```

Commit and Push.

## Cleanup

Delete the cluster:

```
eksctl delete cluster -f cluster.yml --wait
```