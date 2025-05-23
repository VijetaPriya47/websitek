---
title: "Require Container Port Names in CEL expressions"
category: Other in CEL
version: 
subject: Pod
policyType: "validate"
description: >
    Containers may define ports on which they listen. In addition to a port number, a name field may optionally be used. Including a name makes it easier when defining Service resource definitions and others since the name may be referenced allowing the port number to change. This policy requires that for every containerPort defined there is also a name specified.      
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other-cel/require-container-port-names/require-container-port-names.yaml" target="-blank">/other-cel/require-container-port-names/require-container-port-names.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-container-port-names
  annotations:
    policies.kyverno.io/title: Require Container Port Names in CEL expressions
    policies.kyverno.io/category: Other in CEL 
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Containers may define ports on which they listen. In addition to a port number,
      a name field may optionally be used. Including a name makes it easier when defining
      Service resource definitions and others since the name may be referenced allowing
      the port number to change. This policy requires that for every containerPort defined
      there is also a name specified.      
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: port-name
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
          expressions:
            - expression: "object.spec.containers.all(container, container.?ports.orValue([]).all(port, has(port.name)))"
              message: Name is required for every containerPort.


```
