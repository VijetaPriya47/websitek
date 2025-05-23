---
title: "Require Images Use Checksums"
category: Sample
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Use of a SHA checksum when pulling an image is often preferable because tags are mutable and can be overwritten. This policy checks to ensure that all images use SHA checksums rather than tags.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/require-image-checksum/require-image-checksum.yaml" target="-blank">/other/require-image-checksum/require-image-checksum.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-image-checksum
  annotations:
    policies.kyverno.io/title: Require Images Use Checksums
    policies.kyverno.io/category: Sample
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      Use of a SHA checksum when pulling an image is often preferable because tags are mutable and can be overwritten. This policy checks to ensure that all images use SHA checksums rather than tags.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: require-image-checksum
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "Images must use checksums rather than tags."
      pattern:
        spec:
          containers:
          - image: "*@*"
          =(ephemeralContainers):
          - image: "*@*"
          =(initContainers):
          - image: "*@*"

```
