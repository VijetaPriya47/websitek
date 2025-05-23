---
title: "Require Unique Service Selector"
category: Other
version: 
subject: Service
policyType: "validate"
description: >
    Services select eligible Pods by way of label matches. Having multiple Service apply based on same labels can cause conflicts and have unintended consequences. This policy ensures that within the same Namespace a Service has a unique set of labels as a selector.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/require-unique-service-selector/require-unique-service-selector.yaml" target="-blank">/other/require-unique-service-selector/require-unique-service-selector.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-unique-service-selector
  annotations:
    policies.kyverno.io/title: Require Unique Service Selector
    policies.kyverno.io/category: Other
    kyverno.io/kyverno-version: 1.9.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: Service
    policies.kyverno.io/description: >-
      Services select eligible Pods by way of label matches. Having multiple
      Service apply based on same labels can cause conflicts and have unintended
      consequences. This policy ensures that within the same Namespace a Service has
      a unique set of labels as a selector.
spec:
  validationFailureAction: Audit
  background: false
  rules:
    - name: check-service-selector
      match:
        any:
          - resources:
              kinds:
                - Service
      preconditions:
        all:
          - key: "{{request.operation || 'BACKGROUND'}}"
            operator: NotEquals
            value: DELETE
      context:
        - name: services
          apiCall:
            urlPath: "/api/v1/namespaces/{{request.namespace}}/services"
            jmesPath: "items[?spec.selector]"
        - name: service_count
          variable:
            jmesPath: "services[?label_match(spec.selector, `{{request.object.spec.selector}}`)] | length(@)"
      validate:
        message: "There is already a matching selector for this Service."
        deny:
          conditions:
            any:
              - key: "{{service_count}}"
                operator: GreaterThan
                value: 0

```
