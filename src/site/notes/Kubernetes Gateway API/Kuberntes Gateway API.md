---
{"dg-publish":true,"permalink":"/Kubernetes Gateway API/Kuberntes Gateway API/","created":"2024-03-12T16:24:31.000+09:00","updated":"2024-05-11T11:40:56.312+09:00"}
---

## Gateway
클라우드 환경에서 어떤 로드 밸런서를 사용할 지 정의하는 것처럼, 외부에서 들어오는 트래픽을 처리하는 인프라 인스턴스를 정의한다. Service 와 같은 백엔드에 대한 필터링, 로드밸런싱 등 트래픽 처리에 사용될 수 있는 네트워크 엔드포인트를 정의한다.

다시 말하자면, 외부에서 트래픽이 쿠버네티스 클러스터 안으로 들어온 이후 해당 트래픽이 쿠버네티스가 알고 있는 곳으로 전달할 수 있도록 변환하는 방법을 정의해놓았다고 생각하면 된다.

Gateway 리스너는 아래처럼 HTTPRoute 를 제한할 수 있다.
1. hostname
	- Gateway 에 지정된 hostname 리스너 필드가 HTTPRoute 에도 있어야 연결 가능
2. namespace
	- Same: 디폴트 값으로, Gateway 와 동일한 nameSpace 의 HTTPRoute 만 연결 가능
	- All: 모든 nameSpace 의 HTTPRoute 와 연결 가능
	- Selector: HTTPRoute 가 있는 nameSpace 의 label 에 Selector 로 지정한 값이 있을 경우 HTTPRoute 로 연결 가능
3. kind
	- 말 그대로 Route 의 kind 를 지정하여 연결할 수 있다.
		- HTTPRoute / TCPRoute / TLSRoute 등... 해당하는 Kind 만 Gateway 에 연결할 수 있는 것이다.

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1  
kind: Gateway  
metadata:  
	name: numbering-front-gateway  
	# gateway deployment 가 생성되는 namespace 와 동일해야 된다.  
	namespace: istio-system  
	labels:  
		app: numbering-front-ingressgateway  
		istio: ingressgateway  
		environment: dev  
spec:  
	gatewayClassName: istio  
	addresses:  
	# {service-name}.{namespace}.svc.cluster.local  
	- value: numbering-front-ingressgateway-service.istio-system.svc.cluster.local  
	type: Hostname  
	listeners:  
	-  name: numbering-front-gateway  
		hostname: "*.dev.my-service.com"  
		port: 80  
		protocol: HTTP  
		allowedRoutes:  
			namespaces:  
				from: All
```

여기서 address 필드는 Gateway 가 트래픽을 수신하는 데 사용할 주소를 지정한다. 
value 부분에 Gateway 서비스의 FQDN 을 지정하고 type: hostname 은 이 값이 호스트 이름임을 타나낸다. 
이 설정은 특정 호스트 이름으로 수신(\*.dev.musinsa.com)되는 트래픽을 numbering-front-ingressgateway Gateway 가 처리하도록 지정한다
만약 address 필드가 없을 경우 Kubernetes Gateway API 구현체(istio)의 기본 동작이나 구성을 사용하여 트래픽을 수신할 주소를 결정할 수 있다. -> Gateway 가 클러스터 내부 또는 외부의 기본 설정으로 지정된 주소에 바인딩 할 수 있다는 뜻이다.
## HTTPRoute
HTTPRoute: Gateway 에서 수신한 트래픽을 맵핑하기 위한 HTTP 관련 규칙을 정의한다. 일반적으로 Service 에 맵핑한다.
HTTPRoute 와 TCPRoute 를 통해 트래픽이 라우팅되어 Service 로 도달한다면, Gateway 와 Service 에서 트래픽은 암호화 될 수 있다
![Pasted image 20240312162655.png](/img/user/Kubernetes%20Gateway%20API/static/Pasted%20image%2020240312162655.png)
위 그림은 Kubernetes Gateway API 가 어떤 식으로 연결되어 있는지 보여 준다.
Gateway 는 딱 하나의 GatewayClass 와 연결된다. GatewayClass 는 Gateway Controller 를 명시한다.
HTTPRoute 는 여러 Gateway 에 연결될 수 있다. Gateway 는 수신한 트래픽을 연결된 HTTPRoute 를 확인하여 라우팅한다.
![Pasted image 20240312163109.png](/img/user/Kubernetes%20Gateway%20API/static/Pasted%20image%2020240312163109.png)
위 그림은 트래픽이 들어와 Gateway 와 HTTPRoute 를 사용하여 서비스로 라우팅되는 과정을 보여준다.
여기서 Gateway 는 Reverse Proxy 로 봐도 무방하다.

HTTPRoute 는 여러 Gateway 에 연결될 수 있다.