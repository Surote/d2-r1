# Taint

A taint on a node repels pods. A toleration on a pod lets that pod schedule onto a node with a matching taint.
A toleration permits placement but does not force it. To pin pods to the tainted node, also use a nodeSelector or node affinity.

| Effect | What it does |
|---|---|
| `NoSchedule` | New pods without a matching toleration are not scheduled on the node. |
| `PreferNoSchedule` | The scheduler tries to avoid the node, but can still use it. |
| `NoExecute` | New pods are not scheduled, and running pods without a toleration are evicted. |

## Files

- `1_deployment_toleration.yaml`: deployment `demo-toleration-yellow`, 5 replicas of the `py-http-call` image, tolerates `application=yellow:NoSchedule`.
- `1_deployment_toleration_new.yaml`: deployment `timecheck-deployment-lw`, 5 replicas of the `fast-localtime-check` image on port 8000, same toleration.

Neither file sets a nodeSelector, so pods can land on both the tainted node and untainted nodes.

## Demo

```bash
# Taint a node
oc adm taint nodes <node> application=yellow:NoSchedule

# List worker nodes and their taints
oc get node -o custom-columns=NAME:.metadata.name,TAINT:.spec.taints -l node-role.kubernetes.io/worker

# Optional: label the node so a nodeSelector can target it
oc label node <node> application=yellow

# Deploy
oc apply -f 1_deployment_toleration.yaml
oc apply -f 1_deployment_toleration_new.yaml

# Check which node each pod runs on
oc get pods -o wide

# Remove the taint
oc adm taint nodes <node> application=yellow:NoSchedule-
```
