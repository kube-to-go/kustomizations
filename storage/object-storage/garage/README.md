# garage

## Usage example

Create your **kustomization.yaml** with the required volumes:

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

patches:
- target:
    kind: StatefulSet
    name: garage
  patch: |-
   - op: add
     path: /spec/template/spec/volumes/-1
     value:
       name: data
       hostPath:
         path: /srv/persistent/garage
         type: DirectoryOrCreate
   - op: add
     path: /spec/template/spec/volumes/-1
     value:
       name: metadata
       hostPath:
         path: /srv/persistent/garage.meta
         type: DirectoryOrCreate
```

Create a **garage.toml** file (see [Garage documentation](https://garagehq.deuxfleurs.fr/documentation/quick-start/#generating-a-first-configuration-file)).

Apply your kustomization with `kubectl apply -k .`.

### Persistence

This kustomization requires two volumes which should block storage to Garage.

| Volume     | Description                  | Constraints           |
| ---------- | ---------------------------- | --------------------- |
| `data`     | Persistent storage for blobs | Must be block storage |
| `metadata` | Blob metadata                | Must be block storage |
