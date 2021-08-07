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

## Cleanup

Delete the Graviton2 node group:

```
eksctl delete nodegroup -f graviton2-nodegroup.yml --wait
```

Delete the cluster:

```
eksctl delete cluster -f cluster.yml --wait
```