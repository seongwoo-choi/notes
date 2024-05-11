---
{"dg-publish":true,"permalink":"/kubernetes-gateway-api/service-mesh-vs-network-policy/","dgPassFrontmatter":true,"created":"2024-02-01T18:12:04.000+09:00","updated":"2024-05-11T11:39:37.163+09:00"}
---

### Network Policy
- L3, L4 에서 정책 구현 시에 유용
- 커널 레벨에서 구현
- Node 레벨에서 Rule 을 정의

커널 레벨에서 구현 한다는 뜻은, iptable 을 건드린다는 뜻이다. 일반적으로 성능이 좋다.

### Service Mesh
- L7 에서 구현 시 유용
- user space 레벨에서 구현 (애플리케이션 단 정도로 생각하면 될 듯 하다.)
- Pod 레벨에서 Rule 을 정의

### AuthorizationPolicy

```yaml
apiVersion: security.istio.io/v1  
kind: AuthorizationPolicy  
metadata:  
  name: ip-based-numbering-front-gateway-policy  
  namespace: istio-system  
spec:  
  selector:  
    matchLabels:  
      app: numbering-front-ingressgateway  
      istio: ingressgateway  
      environment: dev  
  action: ALLOW  
  rules:  
  - from:  
    - source:  
        remoteIpBlocks: ["210.96.173.2/32", "116.125.181.2/32", "116.125.181.63/32"]
```

remoteIpBlocks 은 http/https 로드 밸런서를 사용하는 경우에 해당 키를 사용해야 한다.  자세한 것은 [공식 문서](https://istio.io/latest/docs/tasks/security/authorization/authz-ingress/) 참조

istio configmap 에서 아래 설정이 추가되어야 한다.(http/https 를 확인하기 위해 헤더 부분에 x-Forwarded-For 을 추가하는 설정)

```yaml
apiVersion: v1
data:
  mesh: |-
    defaultConfig:
      gatewayTopology:
        numTrustedProxies: 1
      discoveryAddress: istiod.istio-system.svc:15012
      tracing:
        zipkin:
          address: zipkin.istio-system:9411
    defaultProviders:
      metrics:
      - prometheus
    enablePrometheusMerge: true
    rootNamespace: istio-system
    trustDomain: cluster.local
    accessLogFile: /dev/stdout
    accessLogEncoding: JSON
  meshNetworks: 'networks: {}'
kind: ConfigMap
metadata:
  labels:
    argocd.argoproj.io/instance: istio
    install.operator.istio.io/owning-resource: HojinShim
    istio.io/rev: default
    operator.istio.io/component: Pilot
    release: istio
    environment: dev
  name: istio
  namespace: istio-system
```

```yaml
...
    defaultConfig:
      gatewayTopology:
        numTrustedProxies: 1
...
```