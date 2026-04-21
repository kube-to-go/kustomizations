# iac-state

A minimal kustomization providing a ServiceAccount with permissions for hosting an OpenTofu/Terraform state backend in your cluster.

## Usage example

Create your **kustomization.yaml**:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: iac

resources:
- https://github.com/kube-to-go/kustomizations/rbac/iac-state/base
```

Apply your kustomization with `kubectl apply -k .`.

### Obtaining a kubeconfig/token

Optionally, a `justfile` provides commands to generate credentials for the service account:

```sh
just generate-kubeconfig > kubeconfig.yaml
eval $(just generate-env)  # For environment variables such as KUBE_HOST, KUBE_TOKEN, ...
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