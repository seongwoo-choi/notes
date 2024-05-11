---
{"dg-publish":true,"permalink":"/kubernetes/operator/","dgPassFrontmatter":true,"created":"2024-04-16T09:29:56.507+09:00","updated":"2024-05-11T11:39:03.901+09:00"}
---

https://dev.gmarket.com/65
Operator 는 커스텀 리소스의 컨트롤러 역할을 한다. 그리고 오퍼레이터 패턴을 사용할 경우 쿠버네티스 코드를 수정하지 않고 연결된 커스텀 리소스를 컨트롤하여 클러스터에서 특정 동작을 시킬 수 있다. 

어떻게 특정 동작을 시킬 수 있는가? 
오퍼레이터는 커스텀 리소스의에 의한 etcd 의 변경 사항을 감지하고 쿠버네티스에서 원하는 동작을 하게 한다.

즉, Operator 는 커스텀 리소스를 바탕으로 k8s resource 를 생성하고 CR 을 관리하는 컨트롤러의 역할을 한다.

예를 들어 kiali-operator 를 설치하면 kiali 커스텀 리소스를 보고 kaili 관련된 리소스를 자동으로 생성한다.
이 때 세세한 정보나 설정값들은 모두 커스텀 리소스에 등록하여 관리할 수 있다.

현재 내 클러스터에서 원하는 동작을 하는 오퍼레티어가 없다면 직접 개발을 하여 나만의 오퍼레이터를 만들 수 있다.
- [kubebuilder]("https://book.kubebuilder.io/")
- [operator 프레임워크]("https://operatorframework.io/")

