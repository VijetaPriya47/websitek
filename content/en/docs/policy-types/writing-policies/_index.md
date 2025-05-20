---
title: Writing Policies
description: >
  Learn how to write and manage Kyverno policies effectively.
weight: 40
---

# Writing Policies

This guide provides an overview of how to write and manage Kyverno policies effectively. Kyverno policies are declarative YAML resources that allow you to enforce rules and configurations in your Kubernetes clusters.

## Key Concepts

- **Policy Types**: Kyverno supports various policy types, including `ValidatingPolicy`, `ImageValidatingPolicy`, and `CleanupPolicy`.
- **Policy Rules**: Define rules for validation, mutation, generation, and cleanup of resources.
- **Context Variables**: Use context variables to fetch data from other resources for policy evaluation.

## Example Policy

Here's a simple example of a `ValidatingPolicy` that ensures all Pods have a specific label:

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: ValidatingPolicy
metadata:
  name: require-labels
spec:
  validationActions:
    - Deny
  matchConstraints:
    resourceRules:
      - apiGroups: [""]
        apiVersions: ["v1"]
        operations: ["CREATE", "UPDATE"]
        resources: ["pods"]
  validations:
    - expression: "has(object.metadata.labels['app'])"
      message: "All Pods must have an 'app' label."
```

## Further Reading

- [ValidatingPolicy](/docs/policy-types/validating-policy)
- [ImageValidatingPolicy](/docs/policy-types/image-validating-policy)
- [CleanupPolicy](/docs/policy-types/cleanup-policy) 