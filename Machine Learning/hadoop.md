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
- ![HDFS 저장구조](https://zaksal58.github.io/Machine%20Learning/Image/6496.png)
  - 마스터 <--> 슬레이브 구조.
    - NameNode가 마스터 역할을 하고, DataNode가 슬레이브 역할을 함..(네이트온때의 Mysql Blackhole구조를 생각하면 될듯..)
  - 블록 구조의 파일시스템임
  - 하나의 블록은 3개의 수정가능한 블록으로 복제됨

이정도의 spec을 갖고 있고, 어차피 사용하는 사람의 입장에서는
잘 정리된 HDFS Client와 대화하면서 HDFS에 적절한 커맨드만 날려주면 될 듯 함......


# 맵리듀스
학부시절 알고리즘 시간떄 배웠던 전형적인 Divide and Conquer 방식임.(정작 Divide and Conquer가 뭔지는 다 까먹은게함정..)  
Divide and Conquer공법을 엔지니어링 측면에서 실제적으로 구혀한 것이 맵리듀스 알고리즘...

## Map Reduce란..
여러개의 Task를 잘개 쪼개어 Task를 간략화 하고, 간략화한 Task내에서 문제를 해결하는 알고리즘의 대표적인 공법? 중 하나.  
ex) 머지소트  
참고로 Divide and Conquer는 논리 교환법칙과 논리 결합법칙이 성립하는 Task여야 적용 가능.  

## 하둡에서의 맵리듀스 과정
Input데이터를 받아들이고, Splitting, Mapping, Shuffling, Reducing 을 거쳐, 최종적으로 result를 내뱉음.
http://12bme.tistory.com/154 참고함...

하둡에서의 Divide and Conquer  
default상태의 Apache Access log를 하둡의 맵리듀스를 이용하여, HTTP Response Code의 수를 파악하는 Task를 한다고 치자.  
- Input으로는 수많은 Response Code가 있을 것임.(200, 404, 500, 302 등등...)
- Input들을 적당한 Delimiter로 쪼갠다 ---> Splitting
- 수많은 input별로, Delimiter로 쪼개진 Response Code 를 key,value로 매핑한다 ---> Mapping
  - 해당 예제에서의 Key,Value설계는 (Respose Code, 출현횟수) 로 정의함.
- Mapping기준에 의거하여 생성된 데이터들을, key(Response Code)별로 분류한다. 200은 200끼리, 404는 404끼리.... ---> shuffling
- 내가 설계한 Reduce 알고리즘을 태워(해당 예제에서는 '출현빈도' 출력이니, 당연히 Key가 같은것들끼리 묶어 '+'연산을 취해주는것이 알고리즘이 될 것이다.) 결과값을 얻는다 ---> reducing
- 최종적으로 Reducing까지 된 결과물이 result가 된다.
 




