# garage

## Usage example

Create your **kustomization.yaml**:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: object-storage

resources:
- https://github.com/kube-to-go/kustomizations/storage/object-storage/garage/base
- ingress.yaml	# Optional, if you want to access Garage from outside the cluster

configMapGenerator:
- name: garage
  files:
  - ./garage.toml
```

Create a **garage.toml** file (see [Garage documentation](https://garagehq.deuxfleurs.fr/documentation/quick-start/#generating-a-first-configuration-file)).

Apply your kustomization with `kubectl apply -k .`.