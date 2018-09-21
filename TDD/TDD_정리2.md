# 1에 이어서...

1~2개의 문으로는 뚜렷한 규칙성 및, 리팩토링을 할 만큼의 요소들을 찾지 못하였으므로 곧바로 문이 6개(6문)일때의 테스트코드를 작성해 본다.

##참고로
처음에 테스트코드 작성 및, 리팩토링시 주의할 점 중에
- __1분 단위로 테스트를 할 수 있는 범위로__
코드를 수정을 가해야 한다고 하였다.
매번 서비스코드에 변경을 가할 때 마다, 반드시 테스트코드를 작성하여 기존의 로직과 100%똑같은 테스트케이스 통과를 하는지를 반복적으로 수행해야 함이 TDD를 기반으로 한 리팩토링의 핵심이다.

__기존 테스트코드는 지면상 생략__
#### 테스트코드(6문 6 pass)
~~~java
    @Test
    public void sixDoorNoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{false, false, false, false, false, false}, doorState);
    }

    @Test
    public void sixDoorOnePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, true, true, true, true, true}, doorState);
    }

    @Test
    public void sixDoorTwoPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false, true, false, true, false}, doorState);
    }

    @Test
    public void sixDoorThreePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false, false, false, true, true}, doorState);
    }

    @Test
    public void sixDoorFourPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false, false, true, true, true}, doorState);
    }

    @Test
    public void sixDoorFivePass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false, false, true, false, true}, doorState);
    }

    @Test
    public void sixDoorSixPass() throws Exception {
        OneHundredDoors2 oneHundredDoors2 = new OneHundredDoors2(6);
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        oneHundredDoors2.pass();
        boolean[] doorState = oneHundredDoors2.getDoorState();
        assertArrayEquals(new boolean[]{true, false, false, true, false, false}, doorState);
    }
~~~

그리고 서비스 코드를 만든다. 1문과 2문에서 만든것처럼 하드코딩으로 만들도록 한다.

#### 서비스코드(6문)
~~~java
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
        if (doorCount == 6) {
            if (pass == 1) {
                doorState[0] = !doorState[0];
                doorState[1] = !doorState[1];
                doorState[2] = !doorState[2];
                doorState[3] = !doorState[3];
                doorState[4] = !doorState[4];
                doorState[5] = !doorState[5];
            }
            if (pass == 2) {
                doorState[1] = !doorState[1];
                doorState[3] = !doorState[3];
                doorState[5] = !doorState[5];
            }
            if (pass == 3) {
                doorState[2] = !doorState[2];
                doorState[5] = !doorState[5];
            }
            if (pass == 4) {
                doorState[3] = !doorState[3];
            }
            if (pass == 5) {
                doorState[4] = !doorState[4];
            }
            if (pass == 6) {
                doorState[5] = !doorState[5];
            }
        }
        else if (doorCount == 2) {
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
- pass메소드가 엄청 복잡해짐

우선 pass메소드를 extrace method로 별도의 메소드로 뺀다.(IntelliJ 기준 cmd+alt+M)
#### 서비스코드(pass메소드에서 복잡한 로직을 extract method로 뺀 코드)
~~~java
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
        changeDoorState();
    }

    private void changeDoorState() {
        if (doorCount == 6) {
            if (pass == 1) {
                doorState[0] = !doorState[0];
                doorState[1] = !doorState[1];
                doorState[2] = !doorState[2];
                doorState[3] = !doorState[3];
                doorState[4] = !doorState[4];
                doorState[5] = !doorState[5];
            }
            if (pass == 2) {
                doorState[1] = !doorState[1];
                doorState[3] = !doorState[3];
                doorState[5] = !doorState[5];
            }
            if (pass == 3) {
                doorState[2] = !doorState[2];
                doorState[5] = !doorState[5];
            }
            if (pass == 4) {
                doorState[3] = !doorState[3];
            }
            if (pass == 5) {
                doorState[4] = !doorState[4];
            }
            if (pass == 6) {
                doorState[5] = !doorState[5];
            }
        }
        else if (doorCount == 2) {
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
- pass메소드의 복잡한 영역을 changeDoorState메소드로 뺌

changeDoorState마저도 복잡한 로직을 갖고 있다.  
if문이 3~4개이상 중첩 반복되는 경우는 반복으로 뺄 수 있거나, (switch문도 이와 동일하다. 가급적 switch문은 지양한다.) 코드를 줄일 수 있는 중요 요소이다.  

중첩된 if문에서 제일 안쪽 요소부터 본다.
#### 서비스코드(changeDoorState 내 doorCount == 6 내부 블럭)
~~~java
            if (pass == 1) {
                doorState[0] = !doorState[0];
                doorState[1] = !doorState[1];
                doorState[2] = !doorState[2];
                doorState[3] = !doorState[3];
                doorState[4] = !doorState[4];
                doorState[5] = !doorState[5];
            }
            if (pass == 2) {
                doorState[1] = !doorState[1];
                doorState[3] = !doorState[3];
                doorState[5] = !doorState[5];
            }
            if (pass == 3) {
                doorState[2] = !doorState[2];
                doorState[5] = !doorState[5];
            }
            if (pass == 4) {
                doorState[3] = !doorState[3];
            }
            if (pass == 5) {
                doorState[4] = !doorState[4];
            }
            if (pass == 6) {
                doorState[5] = !doorState[5];
            }
~~~

수많은 토글로직과, 매직넘버가 존재한다. 우선 doorState내에 있는 매직넘버를 풀어본다
#### 서비스코드(위 코드에서 매직넘버 숫자를 + 로 풀어봐서 규칙성을 찾아봄.)
~~~java
            if (pass == 1) {
                doorState[0] = !doorState[0];
                doorState[0+1] = !doorState[0+1];
                doorState[0+1+1] = !doorState[0+1+1];
                doorState[0+1+1+1] = !doorState[0+1+1+1];
                doorState[0+1+1+1+1] = !doorState[0+1+1+1+1];
                doorState[0+1+1+1+1+1] = !doorState[0+1+1+1+1+1];
            }
            if (pass == 2) {
                doorState[1] = !doorState[1];
                doorState[1+2] = !doorState[1+2];
                doorState[1+2+2] = !doorState[1+2+2];
            }
            if (pass == 3) {
                doorState[2] = !doorState[2];
                doorState[2+3] = !doorState[2+3];
            }
            if (pass == 4) {
                doorState[3] = !doorState[3];
            }
            if (pass == 5) {
                doorState[4] = !doorState[4];
            }
            if (pass == 6) {
                doorState[5] = !doorState[5];
            }

~~~
규칙을 볼 수 있을까?  
각 pass를 기준으로, '다음에 토글되어야 할 문의 번호가, 이전에 토글된 문의 번호를 기준으로 __pass값만큼__ 커짐'을 알 수 있다.  
각 if문의 매직넘버값을 pass로 치환...
#### 서비스코드(위 코드에서 매직넘버값을 치환함)
~~~java
            if (pass == 1) {
                doorState[0] = !doorState[0];
                doorState[0+pass] = !doorState[0+pass];
                doorState[0+pass+pass] = !doorState[0+pass+pass];
                doorState[0+pass+pass+pass] = !doorState[0+pass+pass+pass];
                doorState[0+pass+pass+pass+pass] = !doorState[0+pass+pass+pass+pass];
                doorState[0+pass+pass+pass+pass+pass] = !doorState[0+pass+pass+pass+pass+pass];
            }
            if (pass == 2) {
                doorState[1] = !doorState[1];
                doorState[1+pass] = !doorState[1+pass];
                doorState[1+pass+pass] = !doorState[1+pass+pass];
            }
            if (pass == 3) {
                doorState[2] = !doorState[2];
                doorState[2+pass] = !doorState[2+pass];
            }
            if (pass == 4) {
                doorState[3] = !doorState[3];
            }
            if (pass == 5) {
                doorState[4] = !doorState[4];
            }
            if (pass == 6) {
                doorState[5] = !doorState[5];
            }
~~~
또한, 각 if문의 첫번째 토글하는 문의 index값도 특정한 규칙이 있을을 알 수 있다.  
pass-1

#### 서비스코드(위 코드에서 각 pass의 첫번째 index값을 pass-1로 치환함)
~~~java
            if (pass == 1) {
                doorState[(pass-1)] = !doorState[(pass-1)];
                doorState[(pass-1)+pass] = !doorState[(pass-1)+pass];
                doorState[(pass-1)+pass+pass] = !doorState[(pass-1)+pass+pass];
                doorState[(pass-1)+pass+pass+pass] = !doorState[(pass-1)+pass+pass+pass];
                doorState[(pass-1)+pass+pass+pass+pass] = !doorState[(pass-1)+pass+pass+pass+pass];
                doorState[(pass-1)+pass+pass+pass+pass+pass] = !doorState[(pass-1)+pass+pass+pass+pass+pass];
            }
            if (pass == 2) {
                doorState[(pass-1)] = !doorState[(pass-1)];
                doorState[(pass-1)+pass] = !doorState[(pass-1)+pass];
                doorState[(pass-1)+pass+pass] = !doorState[(pass-1)+pass+pass];
            }
            if (pass == 3) {
                doorState[(pass-1)] = !doorState[(pass-1)];
                doorState[(pass-1)+pass] = !doorState[(pass-1)+pass];
            }
            if (pass == 4) {
                doorState[(pass-1)] = !doorState[(pass-1)];
            }
            if (pass == 5) {
                doorState[(pass-1)] = !doorState[(pass-1)];
            }
            if (pass == 6) {
                doorState[(pass-1)] = !doorState[(pass-1)];
            }
~~~
이제 각 pass문에 대해서 '반복된 규칙'이 어느정도 보일 것이다.
반복된 규칙엔 당연히 반복문을 써 주면 될 것이다.

#### 서비스코드(위 코드에서 각 pass마다 반복된 토글문을 반복문으로 변경)
~~~java
            if (pass == 1) {
                for(int i = pass-1; i < 6; i+=1) {
                    doorState[i] = !doorState[i];
                }
            }
            if (pass == 2) {
                for(int i = pass-1; i < 6 ; i+=2) {
                    doorState[i] = !doorState[i];
                }
            }
            if (pass == 3) {
                for(int i = pass-1 ; i < 6 ; i+=3) {
                    doorState[i] = !doorState[i];
                }
            }
            if (pass == 4) {
                for(int i = pass-1 ; i < 6 ; i+=4) {
                    doorState[i] = !doorState[i];
                }
            }
            if (pass == 5) {
                for(int i = pass-1 ; i < 6 ; i+=5) {
                    doorState[i] = !doorState[i];
                }
            }
            if (pass == 6) {
                for(int i = pass-1 ; i < 6 ; i+=6) {
                    doorState[i] = !doorState[i];
                }
            }
~~~
이제 각 반복문들 또한 규칙을 찾을 수 있을 것이다.
최종적으로 다음과 같은 반복문으로 치환 가능
#### 서비스코드(위 코드에서 반복문의 if문을 제거)
~~~java
            for(int i = pass-1; i < doorCount; i+=pass) {
                doorState[i] = !doorState[i];
            }

~~~

6문일때의 규칙을 기준으로 코드의 중복을 없애고, 매직넘버를 없앤 결과는 다음과 같다.
#### 서비스코드
~~~java
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
        changeDoorState();
    }

    private void changeDoorState() {
        if (doorCount == 6) {
            for(int i = pass-1; i < doorCount; i+=pass) {
                doorState[i] = !doorState[i];
            }
        }
        else if (doorCount == 2) {
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

해당 코드를 살펴보면, doorCount가 2일때의 구문과, 나머지 else영역이, doorCount가 6일때의 반복문 코드로도 cover 함을 알 수가 있다.  
최종적으로 다음과 같은 코드로 정리가 가능.
#### 최종 refactoring을 수행한 서비스코드
~~~java
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
        changeDoorState();
    }

    private void changeDoorState() {
        for(int i = pass-1; i < doorCount; i+=pass) {
            doorState[i] = !doorState[i];
        }
    }
}

~~~







# 끝
쉬운 예제를 통하여 길게 문서를 작성하여 다소 쉬워보일 수 있으나, 여기서의 핵심은 '작은 단위로 코드를 쪼개어 1분정도의 시간마다 테스트코드를 동작 가능하게끔 매우 짧은 단위로 로직을 가져가는' 것이 핵심이다.  
물론 실 서비스에 이 방식을 이용하여 코드를 수정하는 것은 많은 Learning curve를 갖게 될 것이지만.... 그래도 코드의 품질을 높이기 위해서는 반드시 필요한 요소가 아닐까 생각됨.

