사실상 '하둡은 어떤 것이다'
'하둡이 대표적으로 사용하고 있는 모델은 "맵리듀스" 이다' 를 구체적으로 설명하자면 끝도 없고, 지금 나에겐 시간도 없음...
간단간단하게 겉핥기식으로 원리만 꺠우치고 troubleshooting이랑 case-study식으로 너줄너줄 의식의 흐름대로 적어나갈 예정.


# 하둡
아파치 재단에서 개발하는 '분산처리 프레임워크'  
뭐 어렵게 생각하지 말고 요정도로 정리하면 이해가 되지 않을까....
- 일단 하둡을 사용(할)하는 사용자 입장에서는, 리눅스 파일시스템과 비슷한 형식의 하둡 파일시스템을 제공받음 --> HDFS
  - bash마냥, hdfs에다가 커맨드를 날리면, hdfs가 커맨드를 해석하여 원하는 결과값을 출력해 줌.
- 일반적인 리눅스 파일시스템은, one instance - one filesystem형식인데 반해, 하둡의 HDFS(Hadoop FileSystem)은
  many instance - one filesystem형식.
- 저장 형식은 구글링해보니 다음과 같이 정리가 될 수 있다.(틀릴수도.) --> https://opentutorials.org/module/2926/17055 참고함..
[![HDFS 저장구조](zaksal58.github.io/Machine Learning/Image/6496.png)]



# 맵리듀스


