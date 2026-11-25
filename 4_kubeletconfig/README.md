# KubeletConfig

These files turn on `autoSizingReserved`. A script on each node then sizes `system-reserved` CPU and memory from the node's capacity, instead of the fixed default of 500m CPU and 1Gi memory.

- `02-master-system-reserved-dynamic.yaml`: enables auto-sizing on the master (control plane) pool.
- `03-worker-system-reserved-dynamic.yaml`: enables auto-sizing on the worker pool.

## OpenShift 4.21 and later (including 4.22)

On a fresh install of 4.21 or later, auto-sizing is already on by default for **worker** nodes. You don't need `03-worker-system-reserved-dynamic.yaml`.

It is still off by default on **control plane** nodes. Apply `02-master-system-reserved-dynamic.yaml` if you want it on masters too.

If the cluster was upgraded from a version earlier than 4.21, auto-sizing stays off for workers. Turn it on by applying the worker file, or by deleting the `50-worker-auto-sizing-disabled` machine config.

## Check

```bash
# Is auto-sizing on? Look for NODE_SIZING_ENABLED=true
oc debug node/<node> -- chroot /host cat /etc/node-sizing-enabled.env

# Is the upgrade "disabled" machine config present?
oc get mc 50-worker-auto-sizing-disabled
```

## Docs

- [Allocating resources for nodes (OCP 4.22)](https://docs.redhat.com/en/documentation/openshift_container_platform/4.22/html-single/nodes/index#nodes-nodes-resources-configuring)
- [Doc source, "Manually allocating resources for nodes" (enterprise-4.22)](https://github.com/openshift/openshift-docs/blob/enterprise-4.22/modules/nodes-nodes-resources-configuring-setting.adoc)
- [Recommended system-reserved values (KB 5843241)](https://access.redhat.com/solutions/5843241)
