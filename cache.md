캐시 표준 정리
===

# Cache-Control
참고 : https://www.netmanias.com/ko/post/blog/5654/cdn-http/http-cache-control-expiration-and-validation

###### Freshness Validation
Origin server의 웹리소스가 업데이트 되었을때, Cache server의 캐시한 웹리소스 데이터는 유효하지 않은 데이터이다.  
이때 필요한 것이 바로 Freshness Validation이다.  
Cache server에서 이것이 어떤 의미를 가지느냐면, Origin server가 보장해주는 cache의 max-age 타임의 일정 %를 기준으로... 일정 %범위 내에 Client가 웹리소스 요청을 하면 Origin server에 Freshness Validation요청을 하고, 그렇지 않으면 Freshness Validation요청을 하는 구조이다.  
다음과 같은 구조로 볼 수 있다.  
