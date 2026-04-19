# state-rbac

A minimal kustomization providing a ServiceAccount with RBAC for hosting an OpenTofu/Terraform state backend in your cluster.

## Usage example

Create your **kustomization.yaml**:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: iac

resources:
- https://github.com/kube-to-go/kustomizations/iac/state-rbac/base
```

Apply your kustomization with `kubectl apply -k .`.

### Obtaining a kubeconfig/token

For your convenience, a `justfile` allows you to generate a kubeconfig for the service account:

```sh
just generate-kubeconfig > kubeconfig.yaml
```

This writes a kubeconfig file with the token. Adjust `namespace` and `service-account` in the justfile if you used different values.

### Configuring OpenTofu/Terraform

Place the generated kubeconfig in your IaC directory, and configure OpenTofu/Terraform to use it, e.g.:

```hcl
terraform {
  backend "kubernetes" {
    config_path   = "./kubeconfig.yaml"
    namespace     = "iac"
    secret_suffix = "prod"
  }
}
```

## RBAC permissions

The included Role grants CRUD permissions on `secrets` and `coordination.k8s.io/leases`.
These are the permissions that are required by OpenTofu/Terraform for state management.