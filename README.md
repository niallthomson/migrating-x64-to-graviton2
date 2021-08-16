# Migrating an x64 EKS Cluster to Graviton2

## Setup

The example in this repository relies on GitOps, using Flux v2 to setup the workloads in the EKS cluster after it has been provisioned.

The first step is to fork this repository, which will provide you with a space to make changes and push them to the EKS. Then clone the repository locally.

Next, copy the `cluster.yml.template` and name it `cluster.yml`:

```
cp cluster.yml.template cluster.yml
```

You will need to subsitute in your GitHub username in the new `cluster.yml` file in order for Flux to use the correct repository. You can either manually change the `$GITHUB_USERNAME` value in the file, or do something like this:

```
GITHUB_USERNAME="yourusername" eval "echo \"$(sed 's/"/\\"/g' cluster.yml.template)\"" > cluster.yml
```

By default the cluster configuration generated will use the `us-west-2` region, if you wish to modify this you can further edit the `cluster.yml` file. Make sure to change both the region and the availability zones.

You must also [create a GitHub Personal Access Token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) for Flux to use to access GitHub repositories.



## Steps

Create initial cluster:

```
export GITHUB_TOKEN='<personal access token>'

eksctl create cluster -f cluster.yml
```

Get relevant endpoints:

```
todo
```

Add Graviton2 node group:

```
eksctl create nodegroup -f graviton2-nodegroup.yml --skip-outdated-addons-check
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

Commit and push this to your GitHub repository.

If you monitor the resources in the EKS cluster you will see the `Deployment` updated, which will trigger all the `Pod`s to be recycled. They will all end up scheduled on `arm64` nodes.

## Cleanup

Delete the cluster:

```
eksctl delete cluster -f cluster.yml --wait
```