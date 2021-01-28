# Knative kustomize example

Demonstrates how to add additional attributes to Knative's release manifests using `kustomize` overlays.

This example adds `tolerations` and `affinity` attributes to Knative's controllers using either a strategic merge patch
or a JSON patch.

### Structure

```
knative
├── base
└── overlays
    └── production
```

### Try it

Generate the release YAML and print it to stdout:

```console
$ kubectl kustomize knative/overlays/production

apiVersion: v1
kind: Namespace
...
```

The generated YAML document should include the new attributes in the manifests of the `eventing-controller` and
`eventing-webhook` deployments:

```yaml
# ...

---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    eventing.knative.dev/release: v0.20.1
    knative.dev/high-availability: "true"
  name: eventing-controller
  namespace: knative-eventing
spec:

# ...

    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-pool
                operator: In
                values:
                - control-plane

# ...

      tolerations:
      - effect: NoSchedule
        key: node-pool
        operator: Equal
        value: control-plane

# ...
```

### References

[Kustomize docs](https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/)
