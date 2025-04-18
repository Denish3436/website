---
title: "Block Velero Restore to Protected Namespace in CEL expressions"
category: Velero in CEL
version: 
subject: Restore
policyType: "validate"
description: >
    Velero allows on backup and restore operations and is designed to be run with full cluster admin permissions. It allows on cross namespace restore operations, which means you can restore backup of namespace A to namespace B. This policy protect restore operation into system or any protected namespaces, listed in deny condition section.  It checks the Restore CRD object and its namespaceMapping field. If destination match protected namespace then operation fails and warning message is throw.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//velero-cel/block-velero-restore/block-velero-restore.yaml" target="-blank">/velero-cel/block-velero-restore/block-velero-restore.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: block-velero-restore
  annotations:
    policies.kyverno.io/title: Block Velero Restore to Protected Namespace in CEL expressions
    policies.kyverno.io/category: Velero in CEL 
    policies.kyverno.io/subject: Restore
    kyverno.io/kyverno-version: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/description: >-
      Velero allows on backup and restore operations and is designed to be run with full cluster admin permissions.
      It allows on cross namespace restore operations, which means you can restore backup of namespace A to namespace B.
      This policy protect restore operation into system or any protected namespaces, listed in deny condition section. 
      It checks the Restore CRD object and its namespaceMapping field. If destination match protected namespace
      then operation fails and warning message is throw.
spec:
  validationFailureAction: Audit
  background: false
  rules:
  - name: block-velero-restore-to-protected-namespace
    match:
      any:
      - resources:
          kinds:
          - velero.io/v1/Restore
          operations:
          - CREATE
          - UPDATE
    validate:
      cel:
        variables:
          - name: namespaceMappingValues
            expression: "has(object.spec.namespaceMapping) ? object.spec.namespaceMapping.map(nsmap, object.spec.namespaceMapping[nsmap]) : []"
        expressions:
          - expression: "!variables.namespaceMappingValues.exists(val, val in ['kube-system', 'kube-node-lease'])"
            messageExpression: "'Warning! Restore to protected namespace: ' + variables.namespaceMappingValues.join(', ') + ' is not allowed!'"


```
