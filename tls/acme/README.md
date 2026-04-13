# acme

## Directory structure

Create a `base` and `overlays` directory.
-  `base/kustomization.yaml` will pull the base kustomization.
-  `overlays/` will contain one overlay per domain.

A minimal directory structure with two domains will look like this:

```
.
├── base
│   └── kustomization.yaml
├── overlays
│   ├── my-first-domain-net
│   │   ├── .env
│   │   └── kustomization.yaml
│   └── my-second-domain-org
│       ├── .env
│       └── kustomization.yaml
└── kustomization.yaml
```

## Base Kustomization

**TODO**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: acme

resources:
- git@github.com:kube-to-go/kustomizations.git/tls/acme/base
```

## Overlay Kustomizations

For each domain/overlay, proceed as follows.

Create your `overlays/my-domain-com/kustomization.yaml`, adjusting the configuration accordingly:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: acme

# Required and must be unique if using multiple domains
nameSuffix: -xyz

components:
- git@github.com:kube-to-go/kustomizations.git/tls/acme/components/acme-dns
#- ../../components/acme-dns

# Main DNS provider and domain configuration (adjust values below)
configMapGenerator:
- name: configuration
  literals:
  # DNS API, see https://github.com/acmesh-official/acme.sh/wiki/dnsapi
  - dns-api=dns_cf
  # Domain name (wildcard will be appended automatically)
  - domain=my-domain.com
  # Namespace in which the secret will be created
  - target-namespace=envoy

# DNS provider credentials (leave this block unchanged)
secretGenerator:
- name: credentials
  envs: [.env]
```

Next to your overlay `kustomization.yaml`, create a `.env` file with the required credentials.
The required contents depend on your DNS provider.
Refer to the [acme.sh documentation](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) for information
on which credentials must be provided and how they can be obtained.

```
CF_Account_ID=my-account-id
CF_Token=my-token
```

## Combined Kustomization (optional)

Create a final `kustomization.yaml` to combine the `base` and all `overlays`.

**TODO**