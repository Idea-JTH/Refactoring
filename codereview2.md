# 추상화&리팩토링 적용기



## 추상화에 대한 기본적인 개념

자바 개발자들이 공부를 하면서 혼란을 겪는 부분이 있는데 인터페이스와 추상화라고 생각한다.

그 중 추상화에 대해 다뤄보려고 한다. 그 동안 나는 자바에 추상화란 기술이 있다고만 생각하고 필요성을 잘 느끼지 못하였고 실질적으로 사용한 적도 거의 없었다. 학교에서도 이런게 있다고만 가르쳤고 더 자세히 실습을 진행하진 않았기 때문이다.

* 객체지향..
  * 일단 자바는 객체지향 언어이다. 따라서 객체의 개념을 언어에 넣어야 한다.
    * 추상 : 무엇인가 덜 구체화 된 것
    * 객체 : 상태와 행동을 가진 것(구체화 된것)
    * 클래스 : 객체를 만들기 위한 틀(붕어빵 틀, 도면 등)
    * 초기화 : 클래스를 이용하여 객체 생성
  * 우리는 클래스를 통해 객체를 생성한다. 
    * 예를 들어 붕어빵으로 객체를 생성할 수 있을까? 바로 위에 쓴 객체 설명엔 구체화 된것이라고 하였다. 붕어빵 틀에서 팥이들어간 붕어빵이, 슈크림이 들어간 붕어빵, 초콜릿이 들어간 붕어빵이 나올지는 아무도 모른다. 즉, 애매하다... 라는 뜻이다. 이렇게 애매한 것을 객체로 만들 수 있을까? 아니 만들지 못한다.
    * 하지만 팥 붕어빵, 슈크림 붕어빵은 객체 생성이 가능하다. 왜냐? 구체적으로 어떤 붕어빵인지 명시되있으니까.. 따라서 애매한 붕어빵 클래스를 추상화를 시켜 객체를 생성하지 못하는 클래스로 만들어버린다.
    
      ![error](https://user-images.githubusercontent.com/39946822/58522735-9ac73900-81fc-11e9-8d02-f10889ad72df.png)



#### 추상화를 사용했을때의 장점

붕어빵이야기로 계속 이어나가자면 추상화된 붕어빵 클래스는 더 쉽게 말해 다른 객체를 생성할 수 있는 붕어빵 클래스들의 밑바탕인 셈이다. 즉, 팥 붕어빵, 슈크림 붕어빵을 구현할때 설계는 이미 되있다는 뜻이다. 붕어빵 클래스는 붕어빵이 가질 수 있는 공통점을 미리 빼놓은 클래스이기 때문에 우리가 붕어빵을 구현할때 좀 더 쉽고 빠르게 구현할 수 있는 셈이다.

쉽게 말하자면 리팩토링 했다고 생각하면 된다.



#### 추상화를 사용하였을때의 단점

지금까지 쓴 글을 보았을땐 추상화가 마냥 좋아보인다. 하지만 추상화를 사용하기 위해선 부모와 자식클래스간의 관계를 생각해보고 애매모호한것을 찾아내야하며 공통적인 부분만 정확하게 빼내야 한다.

따라서 이것들을 실천하기 위해 시간이 많이 소요가 된다.



## 리팩토링이란?

기능은 바꾸지 않으면서 내부 구조를 바꾸어서 최적화 시키는 것을 말하는데 추상화를 통한 코드 최적화도 어찌보면 리팩토링이라고 할 수 있다.

보통은 중복되는 코드, 너무 많은 기능을 하는 메소드, 너무 높은 의존도 등을 기능 변경없이 수정하는 것을 리팩토링이라고 한다.



* 리팩토링이 필요한 경우 예시
  * 중복코드
  * 너무 많은 기능을 하는 메소드
  * 너무 높은 의존도를 가진 클래스
  * 가독성이 떨어지는 코드

![error](http://feel5ny.github.io/images/post_img/46/thumb_default.jpg)



지금 부터는 실제 프로젝트에 적용시킨 사례를 예제로 만들어 설명하겠다.



## 실제 코드 적용

~~~ java
BeforeMovieCutVo {
    간단하게 이미지에 대한 정보를 담고 있는 클래스
        
    여기엔 효과 정보를 담고 있는 effect 필드가 선언되어있다.
        
    Effect effect;
    
    getter&setter...
}
~~~

이미지의 정보를 담고 있는 클래스가 있다. 이 클래스엔 여러가지 효과를 줄 수 가 있다. 예를 들어 깜박이던가 진동을 한다든가 하는 효과들.. 이 효과들의 정보를 담기 위해 effect라는 필드를 선언해놓았다.

여기까진 아직 그리 큰 문제점을 느끼기 어렵다. 문제는 이제부터 조금씩 시작된다...



**Effect** 클래스

~~~java
Effect {
    이미지의 효과에 대한 정보를 담고 있는 클래스
        
    효과엔 여러 종류가 있다. ex)깜박임, 진동, 회전, 확대, 축소
    
    이곳엔 효과별로 세팅하는 메소드가 선언되어있다.
        
    public void blinkSet();
    public void vibrateSet();
    public void wiggleSet();
    public void zoomInSet();
    public void zoomOutSet();
    
    ...
        
   	효과별 세팅 메소드 구현...
}
~~~

효과들의 정보를 담당하는 Effect클래스이다. 이 클래스엔 효과의 기본적인 정보들 뿐만아니라 각각의 효과마다 고유한 정보를 세팅해야하므로 세팅 메소드를 효과의 개수대로 선언 후 구현까지 하였다.



**여기서 한가지 문제점을 발견할 수 있다!**

효과의 종류가 늘어나면 늘어날 수록 그에 따른 세팅 메소드가 증가한다는 것을 알 수 있다.  

생각해보자 만약에 효과가 100개라고 치면 우리가 개발할 때 저 세팅 메소드를 무려 **100개** 나 구현해야된다는 것이다...

또한 그 100개의 메소드가 서로 살짝만 다른 거의 중복코드인 셈이다. 

역시 비효율적이다. 그래서 나는 여기에 추상화를 접목시키기로 했다.



**Effect** 클래스 - 추상화 적용 후

~~~java
Effect {
    효과의 기본 정보들을 담는건 동일하다
        
    하지만 그 많던 효과별 세팅메소드를 모두! 지웠다. 
    대신 추상메소드 한개를 추가했다.
        
    public abstract void setting();
}
~~~

Effect 클래스를 추상화 시킨 결과이다. 결과적으로 일단 중복되고 너무 많았던 효과별 세팅 메소드들이 모두 없어졌다! 하지만 효과별로 세팅해야되는건 마찬가지이므로 추상메소드로 setting()메소드를 **선언**만 해놓았다. **아직 구현은 하지 않았다.**

이제는 부모클래스인 **Effect** 클래스를 상속받는 자식클래스를 생성하여야 한다. 일단 예시로 효과 중 하나인 Blink 효과를 만들어보자.



**Blink** 클래스

~~~java
Blink {
    ...
    
    @Override
    public void setting() {
        이제 메소드별 세팅은 각자의 효과 클래스에서 구현하도록 한다.
    }
}
~~~

자식 클래스인 **Blink** 클래스를 개발하였다. 추상화 전엔 **Effect** 클래스내에서 모든 효과의 정보를 세팅했다면 이제는 상속받은 효과 클래스에서만 세팅하도록 하였다. 그 많던 효과별 메소드는 사라졌고 효과 클래스들에선 그저 부모의 **setting()** 메소드를 **구현**만 하면 되는 것이다. 어떤가? 정말 편해지지 않았나?



추상화를 통해 이런 문제들을 해결했다.

1. 한 클래스가 너무 거대해지는 것
2. 이름만 다른 거의 중복 메소드가 많다는 것



이렇게 나눴을 때의 장점을 확연하게 보여주는 부분이 있다. 다음 코드를 보자



**PrintTest** 클래스

~~~java
PrintTest {
    메인 클래스이다.
        
    이 클래스의 목적은 beforeMovieCutVo에 담긴 정보들을 출력하기 위함이다.
        
    vo setting...
        
    print(before) {
		이 메소드는 effect명에 따라 다른 양식으로 출력하기 위한 메소드이다. 
        switch(effectName) {
            case blink:
            	before.effect.blinkSet();
            	...
                break;
            case vibrate:
                before.effect.vibrateSet();
            	...
                break;
            case zoomin:
                before.effect.zoomInSet();
            	...
                break;
            case wiggle:
                before.effect.wiggleSet();
            	...
                break;
        }
    }
}
~~~

다른 곳보다 **print()** 메소드에 집중해주길 바란다. 이 메소드는 효과 명에 따라 다른 출력을 하는 기능을 가지고 있다. 하지만 자세히 보면 효과명에 따라 다르게 처리한다 하지만 모두 거의 비슷한 것을 알 수있다. 한마디로 중복된 코드가 남발되고 있다는 뜻이다. 효과명에 따라 effect에서 각기 다른 메소드를 호출한다 하는 일은 거의 같지만...



하지만 우리는 아까 추상화를 적용시켰다. 따라서 이 코드도 변화를 줄 수 있다.



~~~java
print(effect) {
    switch(effectName) {
        case : ...
            effect.setting();
            syso...
            break;
            
        case : ...
            effect.setting();
            syso...
            break;
            
        case : ...
            effect.setting();
			syso...
            break;
    }
}
~~~

한가지 변한것을 느낄 수 있을 것이다. 그동안 나는 계속 effect내의 메소드들을 일일이 호출하였다.

하지만 그것은 너무 비효율 적이었고 **Effect** 클래스에 추상화를 적용시켰다.

저 코드에선 효과명에 따른 다른 메소드 호출 방식으로 하지않고 effect에 선언된 추상화 메소드인 **setting()** 메소드를 호출했다. 그럼 한가지 의문이 들 것이다.



**effect 클래스에선 분명 선언만 해두었는데!?**



당연히 이런 의문이 들것이다. 일단 이렇게 코딩하는 이유는 바로 자식클래스인 각 효과 클래스에 있다.

아까 전 예시로 든 **Blink** 클래스에서 부모의 setting() 메소드를 구현했었다. 하지만 print()메소드에선 effect의 setting()메소드를 호출하는데 자바 문법 상  부모의 추상메소드를 호출했을 때 그것을 상속받은 자식클래스의 구현된 메소드가 실행 된다는 것이다. 따라서 정리하자면



**effect.setting() => Blink.setting()** 가 성립이 된다는 뜻이다.



이렇게 나는 그 많고 많던 효과별 세팅 메소드 지옥에서 완전히 벗어나게되었다.

하지만 아직 한가지 문제점이 print() 에 더 남아있다.

어차피 출력양식도 서로 거의 비슷하다. 하지만 효과 이름이 다르다는 이유하나만으로 switch문 안에 또 효과의 개수만큼 케이스를 만들어야 하기 때문에 비효율적이다. 따라서 한번 더 추상메소드를 통한 리팩토링을 진행하기로 한다.



**Effect 클래스**

~~~java
Effect {
    이전과 동일..
    
    public abstract void setting();
    public abstract void printBefore();
}
~~~

먼저 Effect 클래스에 **printBefore()** 라는 추상메소드 한개를 추가하였다. 이제 감이 오는가?

맞다. 이제 자식 클래스인 효과 클래스들에서 이 메소드를 구현하면 된다.



**Blink 클래스**

~~~java
Blink {
    이전과 동일...
    
    @Override
    public void setting();
    
    @Override
    public void printBefore(
    	System.out.println("blink 효과 출력..")
    );
    
}
~~~

예시로 Blink 클래스에서 구현을 한 모습이다. 이렇게 자식클래스에 모두 **자신만의 출력 양식**을 구현해놓으면 print 메소드에서 switch문을 통한 분류작업을 할 필요가 없어지게 된다.



즉, 어떤 뜻이냐면...



~~~java
print(effect) {
    effect.setting();
    effect.printBefore();
}
~~~

print메소드가 이렇게 간단해진다는 뜻이다. 이전 코드와 달리 switch문을 통한 분류작업이 필요없어지게 되었으니 effect의 setting()과 printBefore()만으로 동일한 기능을 하면서 코드는 최적화 하였다.



추상화를 접하고 코드에 적용시킨 결과이다. 어떤가? 가독성이 좋아지고 총 코드 분량이 줄어들지 않았나? 이 예제를 통해 추상화에 대한 개념을 잊어버리지 말고 한번 자신의 프로젝트에 적용시켜보자



---

## 결론

추상화를 통해 여러가지 중복된 코드나 클래스들간의 관계를 정리할 수 있게 된다.

리팩토링을 통해 코드가 최적화 되어 클린한 코드가 된다.



---

## 참고

추상화 참고 사이트 : [https://itewbm.tistory.com/entry/%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4abstract-class%EC%9D%98-%EC%A1%B4%EC%9E%AC-%EC%9D%B4%EC%9C%A0](https://itewbm.tistory.com/entry/추상클래스abstract-class의-존재-이유)



리팩토링 참고 사이트 : https://codereview.tistory.com/3



----

## [2019년 정보처리기사 기출문제 -JAVA(다형성)]

![error](https://user-images.githubusercontent.com/39946822/58530912-7038a880-821b-11e9-8b80-75badfdbd5b1.png)

#### 정답 : 첫번째로 Sub Object출력, 두번째로 Super Object 출력 









