---
title: "Validate Probes"
category: Sample
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Liveness and readiness probes accomplish different goals, and setting both to the same is an anti-pattern and often results in app problems in the future. This policy checks that liveness and readiness probes are not equal. Keep in mind that if both the  probes are not set, they are considered to be equal and hence fails the check.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/ensure-probes-different/ensure-probes-different.yaml" target="-blank">/other/ensure-probes-different/ensure-probes-different.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: validate-probes
  annotations:
    pod-policies.kyverno.io/autogen-controllers: none
    policies.kyverno.io/title: Validate Probes
    policies.kyverno.io/category: Sample
    policies.kyverno.io/severity: medium
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Liveness and readiness probes accomplish different goals, and setting both to the same
      is an anti-pattern and often results in app problems in the future. This policy
      checks that liveness and readiness probes are not equal. Keep in mind that if both the 
      probes are not set, they are considered to be equal and hence fails the check.
spec:
  validationFailureAction: Audit
  background: false
  rules:
    - name: validate-probes
      match:
        any:
        - resources:
            kinds:
            - Deployment
            - DaemonSet
            - StatefulSet
      validate:
        message: "Liveness and readiness probes cannot be the same."
        deny:
          conditions:
            any:
            - key: "{{ request.object.spec.template.spec.containers[?readinessProbe==livenessProbe] | length(@) }}"
              operator: GreaterThan
              value: 0
```
