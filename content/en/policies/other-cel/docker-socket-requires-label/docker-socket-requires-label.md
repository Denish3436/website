---
title: "Docker Socket Requires Label in CEL expressions"
category: Other in CEL
version: 
subject: Pod
policyType: "validate"
description: >
    Accessing a container engine's socket is for highly specialized use cases and should generally be disabled. If access must be granted, it should be done on an explicit basis. This policy requires that, for any Pod mounting the Docker socket, it must have the label `allow-docker` set to `true`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other-cel/docker-socket-requires-label/docker-socket-requires-label.yaml" target="-blank">/other-cel/docker-socket-requires-label/docker-socket-requires-label.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: docker-socket-check
  annotations:
    policies.kyverno.io/title: Docker Socket Requires Label in CEL expressions
    policies.kyverno.io/category: Other in CEL 
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Accessing a container engine's socket is for highly specialized use cases and should generally
      be disabled. If access must be granted, it should be done on an explicit basis. This policy
      requires that, for any Pod mounting the Docker socket, it must have the label `allow-docker` set
      to `true`.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: docker-socket-check
    match:
      any:
      - resources:
          kinds:
          - Pod
          operations:
          - CREATE
          - UPDATE
    validate:
      cel: 
        variables:
          - name: hasDockerSocket
            expression: "object.spec.?volumes.orValue([]).exists(volume, volume.?hostPath.?path.orValue('') == '/var/run/docker.sock')"
          - name: isAllowDockerLabelTrue
            expression: "object.metadata.?labels[?'allow-docker'].orValue('false') == 'true'"
        expressions:
          - expression: "!variables.hasDockerSocket || variables.isAllowDockerLabelTrue"
            message: "If a hostPath volume exists and is set to `/var/run/docker.sock`, the label `allow-docker` must equal `true`."


```
