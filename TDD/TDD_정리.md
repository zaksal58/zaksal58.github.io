개요
2018.09.19 ~ 20 에 있었던 TDD 워크샵을 통해 IntelliJ 소스 리팩토링한 내역들을 기억/정리하기 위함..

# IntelliJ로 소스 리팩토링하기
## 테스트코드 작성하기  
### 테스트코드 작성 전 주의사항 및 숙지사항
#### 작은 범위부터 전체 coverage로
모든 작업은 작은 것부터 점점 확장해 나가는 방식으로 수행한다. 작은 작업부터 큰 작업으로 점점 범위를 확장시켜 나가는 것이 가장 직관적이고 좋은 방법.  
테스트하고자 하는 모듈 내지는 클래스의 가장 기본적인 동작(특별한 경우가 아닌 이상 클래스의 '생성자' 를 호출하여 객체생성을 하는 경우) 부터 시작하여 전체적인 틀을 잡는다.  
- 테스트 최소단위가 '메소드'가 아닌 클래스의 가장 기본적인 '동작' 임에 집중하자. '동작'을 기준으로 테스트를 하며, 모든 커버리지를 커버하도록 테스트를 하다보면, 자연스레 사용되지 않는 메소드도 걸러내지게 된다.  
#### 1분 단위로 테스트를 할 수 있는 범위로
코드의 리팩토링은 어마어마한 시간을 내서 하는 대규모 작업이 아니라, 점진적으로 코드의 구조를 바꾸어나가는 작업의 연속이다.  
TDD 방식으로 개발과 테스트를 지속하며, 중복된 로직을 합치고 코드스멜을 줄여나가는 작업을 동시에 수행하는 것이 베스트.  
물론 소스의 수정범위 또한, 대형수정 후 일괄 테스트를 돌리는 것이 아닌, 1분 단위로, 컴파일오류없이 테스트를 돌릴 수 있는 수준으로 작게 작게 수정을 가한다.
#### 텍스트 에디팅은 되도록 지양
IntelliJ나, Eclipse 모두 좋은 Refactor 내장 기능을 갖고 있다.  
코드 수정 과정에서 로직의 오류를 최소화하거나, 들이는 시간을 최소한으로 하기 위해 가급적 텍스트 직접 에디팅은 하지 않는것이 좋다.  
  
  
## 테스트코드 작성해 보기
간단한 예제를 통하여 본다.... 워크샵 강의떄 배웠던 내용.

> 100개의 문이 있다. 이 문들은 초기상태는 모두 '닫혀있는' 상태이다. 너는 이 100개의 문을 다 'pass'해야 하는 과제를 수행해야 한다. 첫 'pass'는, 모든 문을 방문하면서 문의 상태를 토글(닫혀있다면 열고, 열려있다면 닫고)한다. 두번째 'pass'는, 2,4,6,8.... 과 같이 'pass'의 배수에 해당하는 문만 방문하면서 문의 상태를 토글한다. 같은 세번째 'pass'에서는, 3,6,9,12.... 과 같이 3의 배수에 해당하는 문만 방문하며 문의 상태를 토글하고, 이 반복을 100번째 'pass'까지 진행한다. 이 때, 100개의 문을 100번째 'pass'까지 수행한 후의 모든 문의 상태는?


### 문제 인식
가장 작은 동작의 단위부터 시작.  
100개의 문과 100번의 'pass'가 아닌, 우선 1개의 문을 가지고 작은 영역부터 문제를 해결해 나간다.  
(사실 우리가 실제 구현하고 해결해야 하는 일련의 서비스로직 내지는 비즈니스 로직은, 문 100개를 100번 pass하는 그런 요구사항이 주어져, 직관적으로 동작을 쪼갤 수 있는 것을 생각해 낼 수 있는 성질의 것이 아니긴 하다....결국 문제 인식과, 전체 요구사항을 간략화 시키는 그 능력은 리팩토링과는 별개로 그 능력을 키워나가야 하는 방안을 별도로 생각해 보아야 할 것 같다는 생각이 들었다.)  
  
### 테스트코드를 기반으로 서비스코드를 

1개의 문을 no pass하는 코드(초기상태). TDD이니 테스트코드부터 작성.  
1개의 문을 no pass 하는 동작을 구현한 테스트코드이다.  
~~~
public class OneHundredDoors2Test {
    @Test
    public void oneDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false}, doorState);
    }
}
~~~

그리고 테스트코드를 통과할 서비스코드는 다음과 같이 작성될 수 있음.
~~~
    public boolean[] getDoorState() {
        boolean[] doorState = new boolean[]{false};
        return doorState;        
    }
~~~

-----------------

뭔가 Hello World 찍는 병맛같은 코드로 보이지만, 테스트케이스 및 동작을 하나하나씩 추가해 보자.  
1문 no pass 다음의 동작을 1문 1pass. 테스트코드는 이렇게 될거다.  
(클래스명은 생략한다.)
~~~
    @Test
    public void oneDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false}, doorState);
    }

    @Test
    public void oneDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true}, doorState);
    }

~~~

이상한점 1.  
no pass와 one pass가 assert문만 다르고 행하는 동작이 같다? 논리적으로 말이 안된다. 따라서 다음과 같이 수정을 한다.  
pass '동작' 추가.... 여기서 집중해야 할 것은, pass하는 동작을 추가한다는 것을, 서비스 코드가 아닌 <span style="color:red">__테스트 코드를 통해__</span> 파악하고, 테스트코드로 인해 서비스코드가 생겼음을 알 수 있다. 이제 Test Driven Develop의 의미를 조금은 알 것 같지 않은가?(라고 말하지만 나도 여기까지밖에 모르는건 함정.)  

따라서 pass 메소드를 추가하였다.(솔직히 난 저렇게 생각안하고 생성자에 파라미터를 추가했었었다....ㅜㅜ) 그럼 서비스코드는 이제 pass메소드를 구현하면 될 것이다.
~~~
    @Test
    public void oneDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false}, doorState);
    }

    @Test
    public void oneDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true}, doorState);
    }
~~~

테스트코드를 통과할 서비스코드는 다음과 같이 작성될 수 있음.
~~~
public class OneHundredDoors2 {

    boolean[] doorState = new boolean[]{false};

    public boolean[] getDoorState() {
        return doorState;
    }

    public void pass() {
        doorState[0] = !doorState[0];
    }
}
~~~
이전과 변경된 점이라면, 
- doorState 변수가 field로 변경
- pass메소드를 구현하였고, 토글을 ! unary operator를 붙여 그대로 기존 변수에 assign함
정도로 파악할 수 있겠다.  

__P.S__
IntelliJ에서 테스트를 돌려보면 어느 영역이 테스트에 Cover되는지 시각적으로 표시해 주는 기능이 있다.  
내 커스텀 IntelliJ 테마를 기준으로 스크린샷을 떠보면 다음과 같이, 테스트코드가 Cover한 테스트영역은 녹색으로 표시가 된다.  
![before](https://raw.githubusercontent.com/zaksal58/zaksal58.github.io/master/TDD/image/before_test_covertage.png)
![after](https://raw.githubusercontent.com/zaksal58/zaksal58.github.io/master/TDD/image/test_coverage.png)

------------

### 테스트범위의 확장
점차적으로 테스트범위를 확장해 본다.
문을 1개 늘리는 것이 다음 단계로 진화하는 '1분개발로 테스트가 가능한' 범위가 될 수 있겠다.
~~~
    @Test
    public void oneDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false}, doorState);
    }

    @Test
    public void oneDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true}, doorState);
    }

    @Test
    public void twoDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false, false}, doorState);
    }
~~~
다음을 수행해 보면 당연히 테스트가 제대로 성공하지 못할 것이다. 모든 서비스코드를 Cover하지만, 원하는 동작은 아니다.  
테스트 로직을 보면, oneDoorNoPass()와 동일한 테스트로직임을 파악할 수 있고,
'문의 갯수' 영역이 빠져있다.  
문의 갯수 영역을 적절히 추가하여 테스트코드를 만든다. 
- 어차피 문의 개수는 초기에 정해지고 고정되는 값이므로, 생성자에 문의 갯수를 추가해도 무방함을 알 수 있겠다..
- twoDoorNoPass메소드 내 테스트로직에, OneHundredDoors2 생성자 파라미터로 문의 갯수를 추가하면, 나머지 oneDoor메소드들에 대해서도 문의 갯수를 정의하여야 한다.
~~~
    @Test
    public void oneDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(1);
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false}, doorState);
    }

    @Test
    public void oneDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(1);
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true}, doorState);
    }

    @Test
    public void twoDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(2);
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false, false}, doorState);
    }
~~~
완벽한 테스트코드이다. 이제 실제 서비스코드를 작성해 본다.
~~~
public class OneHundredDoors2 {

    boolean[] doorState;

    public OneHundredDoors2(int doorCount) {
        doorState = new boolean[doorCount];
    }

    public boolean[] getDoorState() {
        return doorState;
    }

    public void pass() {
        doorState[0] = !doorState[0];
    }
}
~~~
이전과 변경된 점이라면
- 생성자를 통해 doorState문 갯수 및 상태 생성

그럼 이제 문 2개를 1pass, 2pass까지 완벽히 수행하게 할 수 있는 테스트코드를 작성해 본다.
___1문 테스트코드는 생략한다___
~~~
    @Test
    public void twoDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(2);
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false, false}, doorState);
    }

    @Test
    public void twoDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(2);
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, true}, doorState);
    }

    @Test
    public void twoDoorTwoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(2);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false}, doorState);
    }
~~~
주요 변경점은 다음과 같다.
- assertArrayEquals 내용이 조금 복잡해졌다(그렇다고 치자.)
- pass()가 연속으로 2회 호출되었다.

현재 구현한 pass메소드대로라면, 당연히 이 테스트코드를 돌리는 순간 실패하는 케이스가 나올 것이다.  
상기 적어낸 2가지 주요 변경점을 고려하여, 실제 서비스코드를 작성해 본다.  
__아직 뚜렷한 규칙성을 찾을 수 없으므로(우린 잘난 개발자들이지만 일단 그렇다고 치자.)하드코딩을 한다는 느낌으로 코드를 작성한다.__
~~~
public class OneHundredDoors2 {

    private final int doorCount;
    boolean[] doorState;
    private int pass;


    public OneHundredDoors2(int doorCount) {
        this.doorCount = doorCount;
        doorState = new boolean[doorCount];
    }

    public boolean[] getDoorState() {
        return doorState;
    }

    public void pass() {
        pass++;
        if(doorCount == 2) {
            if(pass == 1) {
                doorState[0] = !doorState[0];
                doorState[1] = !doorState[1];
            }
            else if(pass == 2) {
                doorState[1] = !doorState[1];
            }
        }
        else {
            doorState[0] = !doorState[0];
        }

    }
}
~~~
변경된 점
- pass가 연속 2번 호출되는 케이스가 생김으로써 다음과 같이 서비스코드가 변화되었다
  - 이전 pass상태값을 알아야 한다 --> pass가 몇번 발생하였는지 알아야 한다 --> 클래스 필드 pass 생성
  - 같은 pass라도 문의 개수에 따라 수행하는 동작이 다르다 --> 문의 개수를 알아야 한다 --> 클래스 필드(문의 개수는 가변이 아니므로 상수화) doorCount 생성
- pass 메소드가 좀 복잡해지기 시작...  











좋은 테스트케이스는 테스트 메소드 내에 요구사항에 대한 spec이 저절로 명시된다.  
테스트케이스가 더 필요한지 아닐지에 대해서는, TDD방식으로 요구사항을 구현하다보면 저절로 개발자가 파악할 수 있다..  물론 그 기준은 개발자 스스로가 파악하게 되며, (그 판단이 틀릴수도 있다.)  
  
요구사항을 완벽히 충족하였는지에 대한 증명을 할 수 있는 좋은 방법이 TDD. 테스트를 통하여 내가 작성한 코드가 정상적이게 동작하는지를 검증하였기 때문..  





