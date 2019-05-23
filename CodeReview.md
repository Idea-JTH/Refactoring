# 3가지 코드 분석&비교

1. 추상화를 사용하지 않았을 때 짰을 코드
2. 추상화를 사용했을 때의 코드
3. 2번의 코드에서 리팩토링을 거친 코드

---

### 1. 추상화를 사용하지 않은 코드

* Effect 클래스	(beforeVO가 포함하는 효과 vo이다.)  장면전환효과 클래스도 이와 거의 똑같다.

  * ~~~java
    public class Effect {
        private Rect location;
        private int sTime;
        private int eTime;
       // ...
        
        //getter & setter
        
        
        // 효과별 세팅 메소드 ~ ..
    }
    ~~~

    * 장면효과들을 담당하는 Effect클래스가 있다. 현재는 추상화 클래스를 전혀 이용하지 않은 상태이렇게 만들게 되면 이 Effect클래스에 값을 세팅할 때 매우 피곤해진다... 일일이 set()메소드를 실행시켜 값을 넣어주어야 한다. 그렇게 하면 중복되는 코드가 굉장히 늘어날것이다...



* PrintTest클래스

  * 이 클래스는 before vo들을 세팅하여 String형식으로 임시 출력하기위해 만들 클래스이다.

  * main()

    ~~~java
    public class PrintTest {
        public static void main(String args[]) {
            List<BeforeMovieCutVo> befores = new ArrayList<BeforeMovieCutVo>();
            
            BeforeMovieCutVo before1 = new BeforeMovieCutVo();
            BeforeMovieCutVo before2 = new BeforeMovieCutVo();
            BeforeMovieCutVo before3 = new BeforeMovieCutVo();
            
            befores.add(before1);
            befores.add(before2);
            befores.add(before3);
            beforeVoSetting(before1, before2, before3); //vo setting
            
            for(/*befores의 크기만큼*/) {
                for(/*첫번째 before의 시작시간 부터 마지막 before의 종료시간까지*/) {
         			printBefore(befores, i , j);	// vo print           
                }
            }
        }
    }
    ~~~

    * befores 리스트에 3개의 값의 세팅된 beforeVo를 추가 후 출력하는 메소드의 인자로 들어간다.

  

  *  beforeVoSetting()

    ~~~java
    private static void beforeSetting(BeforeMovieCutVo before1, BeforeMovieCutVo before2, BeforeMovieCutVo before3) {
        	Effect effect1 = new Effect();
        	Effect effect2 = new Effect();
        	Effect effect3 = new Effect();
        
        
    		before1.setsTime(30);
    		before1.seteTime(40);
    		before1.seteName("blink");
    		before1.setLocation("1");
    		before1.setScName("scale-up");
        	effect1.setLocation("1");
        	effect1.setsTime(32);
        	effect1.seteTime(36);
        	effetc1.set()...// effect클래스 필드들 모두 세팅
                //장면전화 효과일 경우에도 이와 동일한 방식으로 세팅한다.
                
            //before2~~~...
           	//befoer3~~~...
        	
    }
~~~
    
  * 파라미터로 들어온 beforeVo 3개를 세팅하는 메소드이다. 현재는 추상화를 사용하기 전 코드라 before자체의 필드와 그 안에 들어있는 effect와 secenChange 도 동일하게 모두 일일이 set()메소드를 통해 값을 넣어주어야 한다. 벌써 매우 비효율적이란걸 알 수 있다.
    
    
  

  
  * printBefore()
  
    ~~~java
    private static void printBefore(List<BeforeMovieCutVo> befores, int i, int j) {
    //		System.out.println(j);
    //		System.out.println("--" + befores.get(i).getAbEffect().getsTime());
    		if(befores.get(i).getAbEffect() != null && befores.get(i).getAbEffect().getsTime() <= j && j <= befores.get(i).getAbEffect().geteTime()) {
    			System.out.println("이펙트");
    			checkEffect(befores.get(i), j, i, befores.get(i).getAbEffect());
    			
    		} else if(befores.get(i).getStartSecenChange() != null || befores.get(i).getEndSecenChange() != null) {
    			System.out.println("장면전환");
    			checkSC(befores.get(i), j, i, befores.get(i).getStartSecenChange(), befores.get(i).getEndSecenChange());
    			
    			
    		} else {
    			System.out.println(j+"초 " + "before" + (i+1) + " (" + befores.get(i).getLocation().x + " " + befores.get(i).getLocation().y + ") 효과 없음");
  		}
    	}
  ~~~
  
  * 여기서 i는 몇번째 before인지 뜻하고 j는 i번째 before의 초단위 이다. j번째에서 이펙트인가 장면전환효과인가로 나누어서 진행한다.
  
  
  
  * checkEffect() (장면전환 효과 체크인 checkSC()도 동일한 형태이다...)
  
    ~~~java
    private static void checkEffect(BeforeMovieCutVo before, int j, int i, Effect effect) {
    		String ename = before.geteName().toLowerCase();
    		switch(ename) {			
    		case "blink":
    			effect.blinkSetting();
    			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + effect.getLocation() + " 현재 : " + effect.now + "번 깜박임");
    			break;
    			
    		case "vibrate":
                effect.vibrateSetting();
    			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ")");
    			
    			System.out.println("effect : " + before.geteName() + " " + effect.getLocation());
    			break;
    			
    		case "zoominout":
    			effect.zoominoutSetting();
    			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + effect.getLocation() + " 현재 : " + effect.now + "% 축소 또는 확대");
    			break;
    		case "Wiggle":
    			effect.wiggleSetting();
    			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + effect.getLocation() + " 현재 : " + effect.now + "%");
    			break;
  		}
    	}
    ~~~
  
    * ename은 여기서 효과명을 말한다. 모든 효과 출력양식이 비슷하지만 ename에 따라서 출력양식이 달라지며 effect에 선언된 효과별 세팅들이 호출된다. 즉, effect클래스는 필드변수도 많지만 효과가 많을 수록 그의 따른 세팅메소드도 많아진다..



---

### 2. 추상화를 사용했을 때의 코드

* 추상화를 사용했을 때는 사용하지 않았을 때의 코드와 크게 차이는 별로 없다. 하지만 중복 코드를 줄이고 세팅도 좀 더 간단하게 하면서 코드를 간소화 시켰다. 아래 달라진 코드를 보자.

  

* Effect 클래스 - 추상화

  ~~~java
  public abstract class Effect {
      protected Rect location;
  	protected int sTime;
  	protected int eTime;
  	protected int length;
  	
     	protected int now = 0;
      
      gettter&&setter
          
      setWeight();
      setting();
  }
  ~~~

  * 달라진 effect클래스이다.
    * 먼저 코드의 양이 눈의띄게 줄어들었다. 원래 코드는 effect가 가질 수 있는 모든 것들을 이 effect클래스 한곳에 몰아넣었었다. 하지만 지금은 효과 모두가 가지는 공통적인 부분들만 선언해 놓은 상태이다. 따라서 클래스가 간소화되었다.
    * 추상화를 더 자세히 설명하자면..
      * ![error](http://cfile226.uf.daum.net/image/032FD643516BAF5D1FD5F1)
      * 이처럼 여러개의 클래스가 모두 공통되는 기능이 있을 경우 한 클래스에서 모두 구현하지 않고 부모 클래스에 메소드를 선언한뒤 자식 클래스들이 이를 상속받아 구현하는것이다.
      *  좀 더 쉽게 이야기하자면 저 사진엔 유닛이라는 부모 클래스와 히드라, 질럿, 마린이라는 자식 클래스가 있다. 유닛을 상속받은 자식 클래스에는 어떤 기능이 공통으로 들어가있을까?? 일단 모두 움직일 수 있으며 상대방에게 공격을 가할 수 있다는 공통점이 들어가있다. 그러면 유닛 클래스에 공격(), 이동() 메소드를 선언한 뒤 자식 클래스에서 상속받아 오버라이드할 수 있는 것이다.

  

* beforeVoSetting() - effect 추상화 후

  ~~~java
  EFBlink blink = new EFBlink(before1);
  Effect effect = blink;		
  before1.seteffect(effect);
  ~~~

  * 추상화 전 코드에선 effect를 세팅하려면 effect객체를 생성후 넣어줘어야 하는 값들을 모두 set()메소드를 통해 세팅하였다. 하지만 추상화 후엔 상속받는 자식 클래스의 객체를 생성 후 beforeVo에 넣어주기만 하면 된다.  추상화 클래스는 객체를 만들 수 없다. 따라서 자식 클래스의 객체를 만든 후 effect에 넣는다. 자세한 세팅은 자식클래스에서 따로 진행하게 된다.

  

* checkEffect() - effect 추상화 후

  ~~~java
  		case "blink":
  			effect.setWeight();
  			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + abEffect.getLocation() + " 현재 : " + abEffect.now + "번 깜박임");
  			break;
  			
  		case "vibrate":
  			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ")");
  			effect.setting((j%4)+1);	// abEffect를 상속받은 EFVibrate의 setting 메소드 실행(로케이션 갱신)
  			System.out.println("effect : " + before.geteName() + " " + abEffect.getLocation());
  			break;
  			
  		case "zoominout":
  			effect.setWeight();
  			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + abEffect.getLocation() + " 현재 : " + abEffect.now + "% 축소 또는 확대");
  			break;
  		case "Wiggle":
  			effect.setWeight();
  			System.out.println(j + "초 " + "before"+ (i+1) + " (" + before.getLocation().x + " " + before.getLocation().y + ") effect : " + before.geteName() + " " + abEffect.getLocation() + " 현재 : " + abEffect.now + "%");
  			break;
  ~~~

  * 이전 코드와 변경된 점이 거의 없다. 하지만 잘 보면 각 효과들마다 세팅하는 메소드가 있었지만 이 코드에선 모두 effect의 setting() 이나 setWeight() 메소드로 통일되있다.
    * 통일된 이유는 부모 클래스에서 선언된 메소드를 자식 클래스에서 구현했을 때 부모 클래스의 메소드를 호출하면 구현된 자식 클래스의 메소드가 실행된다. 따라서 코드상으로 모두 effect의 setting을 호출하지만 정작 실행되는 것은 effect를 상속받은 클래스의 setting메소드이기 때문이다.



---

### 3. 2번코드에서 리팩토링 했을 때의 코드

* 2번에서 추상화를 통해 코드를 보기좋게 했다 하더라도 완전히 뜯어고친것은 아니기에 많은 효과를 보진 못했다. 따라서 리팩토링을 통해 더욱 깔끔한 코드를 만들려고 하였다.

* checkEffect()

  * ~~~java
    effect.print(j);
    ~~~

    * 2번코드까지에선 switch문을 실행시켜서 효과마다 케이스를 두고 진행하는 방식이었다. 하지만 그건 너무 더러운 코드였다. 왜냐하면 비슷한 출력양식이 계속 중복되는 방식이었고 만약에 효과가 총 100개가 있다면 100개의 케이스를 만들어야하는 상황이었기 때문이다. 

    * 또한 메소드 이름에 맞지않는 기능을 하고있었다.  분명 메소드 이름은 체크를 하기 위해 checkEffect라는 이름을 붙였지만 2번코드에선 체크와 출력 두 기능을 동시에 하고있었다.

    * 따라서 나는 checkEffect메소드에선 저 코드만을 실행시키기로 하였다.

      * 저 코드를 실행시키려면 일단 부모 클래스에 이 메소드를 선언시켜야 한다.

        * Effect클래스

          * ~~~java
            public abstract void print(int j);
            ~~~

            * 부모 클래스에 이 메소드를 선언하면 당연히 이것을 상속받은 클래스에서 사용이 가능하다.

              * ex) EFBlink 클래스 - 깜박임 효과 클래스

                * ~~~java
                  public class EFBlink extends Effect {
                      @Override
                      public void print(int j) {
                          this.setting();
                          System.out.println("~~~~")
                      }
                      
                      @Override
                      public void setting() {
                      	//~~~
                      }
                  ~~~
                  * 이렇게 상속받아진 자식클래스에서 부모의 메소드를 가져다 쓸수있다. 이 코드에선 print() 메소드에서 setting() 메소드까지 실행시킨다. 이러면 checkEffect에서 일일이 따져가며 출력할 필요가 없어지게 된다.

* checkEffect - 리팩토링을 거친 후

  * 

    ~~~java
    checkEffect(~~~) {
        
        /*
        	switch() {
        		case:
        		System.out.println();
        		brek;
        		case:
        		System.out.println();
        		brek;
        		case:
        		System.out.println();
        		brek;
        	}
        
        */
        
        effect.print():
    }
    ~~~

    * switch로 로직을 짰을때보다 훨씬 코드가 줄어들었다. 또한 성능도 올라갔을 것이다. 
    * 코드가 보기에 매우 편해진다. 다른 개발자를 위해 print메소드에 주석까지 달아놓으면 더욱 좋을 것.