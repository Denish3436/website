---
title: "Enforce Consul min TLS version"
category: Consul
version: 1.6.0
subject: Mesh
policyType: "validate"
description: >
    This policy will check the TLS Min version to ensure that whenever the mesh is set, there is a minimum version of TLS set for all the service mesh proxies and this enforces that service mesh mTLS traffic uses TLS v1.2 or newer.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//consul/enforce-min-tls-version/enforce-min-tls-version.yaml" target="-blank">/consul/enforce-min-tls-version/enforce-min-tls-version.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: enforce-min-tls-version
  annotations:
    policies.kyverno.io/title: Enforce Consul min TLS version 
    policies.kyverno.io/category: Consul
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Mesh
    kyverno.io/kyverno-version: 1.8.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/description: >-
      This policy will check the TLS Min version to ensure that whenever the mesh is set, there is a minimum version of TLS set for all the service mesh proxies and this enforces that service mesh mTLS traffic uses TLS v1.2 or newer.
spec:
  validationFailureAction: Enforce
  background: true
  rules:
  - name: check-for-tls-version
    match:
      any:
      - resources:
          kinds:
            - Mesh
    validate:
      message: The minimum version of TLS is TLS v1_2
      pattern:
        spec:
          tls:
            incoming:
              tlsMinVersion: TLSv1_2

```
