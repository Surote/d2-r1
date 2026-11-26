## Get the Node IP and NodePort

Find the internal IP of the node where the sharded router pod is running:

```bash
oc get pod -n openshift-ingress -o wide | grep lab | awk '{print $7}' | xargs oc get no -o wide | awk '{print $6}'
```

Get the HTTPS NodePort of the sharded router service:

```bash
oc get svc -n openshift-ingress router-nodeport-lab-router -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}{"\n"}'
```

---
## Apply custom cert to route sharded

```bash
oc create secret tls sharded-custom-cert --cert=./default.crt --key=./default.key -n openshift-ingress
```

```bash
oc patch ingresscontroller.operator lab-router \
  --type=merge \
  -p '{"spec":{"defaultCertificate":{"name":"sharded-custom-cert"}}}' \
  -n openshift-ingress-operator
```

---
## Replacing custome cert to route sharded

```bash
oc create secret tls sharded-custom-cert --cert=./replace_default.crt --key=./replace_default.key -n openshift-ingress --dry-run=client -o yaml | oc replace -f -
```
