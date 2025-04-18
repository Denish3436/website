---
title: "Disallow Default TLSOptions"
category: Traefik
version: 
subject: TLSOption
policyType: "validate"
description: >
    The TLSOption CustomResource sets cluster-wide TLS configuration options for Traefik when  none are specified in a TLS router. Since this can take effect for all Ingress resources, creating the `default` TLSOption is a restricted operation. This policy ensures that only a cluster-admin can create the `default` TLSOption resource.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//traefik/disallow-default-tlsoptions/disallow-default-tlsoptions.yaml" target="-blank">/traefik/disallow-default-tlsoptions/disallow-default-tlsoptions.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-default-tlsoptions
  annotations:
    policies.kyverno.io/title: Disallow Default TLSOptions
    policies.kyverno.io/category: Traefik
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: TLSOption
    kyverno.io/kyverno-version: 1.6.0
    kyverno.io/kubernetes-version: "1.21"
    policies.kyverno.io/description: >-
      The TLSOption CustomResource sets cluster-wide TLS configuration options for Traefik when 
      none are specified in a TLS router. Since this can take effect for all Ingress resources,
      creating the `default` TLSOption is a restricted operation. This policy ensures that
      only a cluster-admin can create the `default` TLSOption resource.
spec:
  validationFailureAction: Audit
  background: false
  rules:
  - name: disallow-default-tlsoptions
    match:
      any:
      - resources:
          names:
          - default
          kinds:
          - TLSOption
    exclude:
      clusterRoles:
      - cluster-admin
    validate:
      message: "Only cluster administrators are allowed to set default TLSOptions."
      deny: {}

```
