## 웹 기초
> 사용자가 요청하면 웹페이지를 보여주기까지 여러 순서를 거친다.
> 1. HTML을 받는 경우<br>
> a) 사용자가 `www.naver.com`을 입력한다.<br>
> b) 서버에서는 html을 css로 예쁘게 만들어주고, javscript로 동적인 이벤트를 준다.<br>
> -> 이때는 페이지 전체를 주는 것이다.
> 2. 데이터만 받는 경우<br>
> a) 사용자가 영화좌석을 예매하기 위해 한 자리를 클릭한다.<br>
> b) 서버에서 그 자리가 빈자리인지 데이터를 조회하여 업데이트(해당 자리에 색깔을 입히는 효과를) 해준다.<br>
> -> 이때는 데이터만 전달하는 것이다.

### 그럼 스프링은 어디를 담당할까?
**서버를 담당한다. 요청을 받으면 눈에 보일 혹은 데이터를 제공해주는 것이다. 이 스프링은 자바 언어를 바탕으로 만든다.**
-> 이번 강의에서는 `스프링으로 서버 만들어보기`가 핵심!


## 자바 기초 문법 -1
### $ 출력
```java
        String myName="문윤지";
        System.out.printf("Hello %s!", myName);
```
<br>

### $ 타입
```java
//int는 21억까지만! 이므로 100억이 되는 순간 빨간줄이 생기므로 'long'으로 바꿔준다.
//long은 접미사 'L'을 붙여줘야한다.
        int a = 1000000000;
        long b = 10000000000L;

//boolean은 true/false
        int age = 20;
        boolean isAdult = age > 20;
        System.out.println(isAdult);
        System.out.println(age != 20);
```
<br>

### $ 배열: 순서 중요, 같은 자료형!, add&get&remove
```java
        //배열 선언
        List<String> myList = new ArrayList<>();
        String course1 = "웹개발의 봄 Spring";
        String course2 = "프론트엔드의 꽃 React";
        myList.add(course1);    //추가
        myList.add(course2);
        System.out.println(myList); //[웹개발의 봄 Spring, 프론트엔드의 꽃, React]

        System.out.println("인덱스로 보여주기: " + myList.get(1));  //출력
        myList.remove(1);   //삭제
        System.out.println("삭제 후 상태: " + myList);
//결과
//        인덱스로 보여주기: 프론트엔드의 꽃 React
//        삭제 후 상태: [웹개발의 봄 Spring]
```
<br>

### $ 메소드
  - 변수명과 규칙 : 영어 사용, 소문자 시작, 공백 없음, 대문자로 구분
```java
public class JavaPrac {
    // 파라미터 X, 반환값 X
    public static void simplePrint() {
        System.out.println("파라미터도 없고, 반환값도 없어요!");
    }

    // 파라미터 O, 반환값 X
    public static void simpleSum(int num1, int num2) {
        System.out.println("num1 :" + num1 + ", num2: " + num2);
    }

    // 파라미터 X, 반환값 O
    public static int simpleReturn() {
        return 3;
    }

    // 파라미터 O, 반환값 O
    public static int sum(int num1, int num2) {
        return num1 + num2;
    }

    // 파라미터 O, 반환값 O
    public static int minus(int num1, int num2) {
        return num2 - num1;
    }
    public static void main(String[] args) {
        // main내에서 메소드를 호출하기위해서는 'static'이 필요
        simplePrint();
        simpleSum(3,2);
        int ret = simpleReturn();
        System.out.println(ret);

        int retSum = sum(10,7);
        System.out.println("합 결과: "+ retSum);

        int retMinus = minus(10,7);
        System.out.println("값을 뺀다: "+retMinus);
    }
}
```

<br>

### $ 반복문
```java
public class JavaPrac {

    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>();
        fruits.add("감");
        fruits.add("배");
        fruits.add("감");
        fruits.add("딸기");
        fruits.add("수박");
        fruits.add("메론");
        fruits.add("수박");
        fruits.add("딸기");
        fruits.add("메론");
        fruits.add("수박");
        fruits.add("메론");
        fruits.add("수박");
        fruits.add("감");
        System.out.println(fruits);

        for (int i=0; i < fruits.size(); i++) {
            String fruit = fruits.get(i);
            System.out.println(fruit);
        }
    }
}
```
<br>

### $ 조건문
```java
public class JavaPrac {

    public static void main(String[] args) {
        int age = 20;
        if (age > 90) {
            System.out.println("완전 어른.");
        } else if (age>50) {
            System.out.println("어른");
        }
        else {
            System.out.println("그냥 어른");
        }
    }
}
```

<br>

### $ 클래스
  - 정보를 묶는 것
  - 현실과 비슷한 개념을 나타냄
  - 클래스는 빵틀, 해당 클래스의 객체는 빵이라 생각하면 쉬움.
  - 클래스의 멤버변수를 생성해주고, 해당 클래스의 객체를 이용해서 멤버변수의 값을 입력한다.
<br>

[새 클래스 Course]

```java
public class JavaPrac {

    public static void main(String[] args) {
        String title = "웹개발의 봄, Spring";
        String tutor = "Moonz";
        int days = 35;
        Course course = new Course(title, tutor, days);
        System.out.println(course.title);
        System.out.println(course.tutor);
    }
}
```
<br>
[main 메서드]

```java
public class JavaPrac {
    public static void main(String[] args) {
        //객체(빵) 생성
        Course course = new Course();

        // 구체화
        course.title = "웹개발의 봄, Spring";
        System.out.println(course.title);
        System.out.println(course.tutor);   //null

        Course course2 = new Course();
        course2.title = "프론트엔드의 꽃, React";
        System.out.println(course2.title);
    }
}
```

<br>

### $ 생성자
  - 객체를 생성할 때 처음부터 기본값이 있도록 하려면? 생성자를 이용한다.
<br>

[Course 클래스]
```java
// 빵틀
public class Course {
    //멤버 변수
    public String title;
    public String tutor;
    public int days;

    //기본 생성자 (자동으로 생성해줌)
    public Course() {
        System.out.println("기본 생성자!");
    }

    //매개변수 있는 생성자 (기본 생성자 생성해줘야함)
    public Course(String title, String tutor, int days) {
        this.title = title;
        this.tutor = tutor;
        this.days = days;
    }
}
```

[main 메서드]
```java
import javax.swing.*;
import java.util.ArrayList;
import java.util.List;

public class JavaPrac {

    public static void main(String[] args) {
        String title = "웹개발의 봄, Spring";
        String tutor = "Moonz";
        int days = 35;
        Course course = new Course(title, tutor, days);
        System.out.println(course.title);
        System.out.println(course.tutor);
    }
}
```
<br>

### $ setter와 getter
> 위의 코드처럼 main함수에서 Course 클래스의 멤버변수를 바꾸고 지정할 수 있었다. 근데 만약 이 정보가 중요한 개인 정보라면? 다음과 같이 바꿔줘야한다.
```java
    private String title;
    private String tutor;
    private int days;
```

  - private으로 선언하면 main함수에서 직접 접근해서 변경할 수 없게 된다!
  - 이때 멤버변수에 직접 접근하는 게 아닌, **메서드를 통해 접근**해야한다.
  - 수정된 코드를 봐보자.<br><br>
[Course 클래스]
```java
// 빵틀
public class Course {
    //멤버 변수
    private String title;
    private String tutor;
    private int days;

    //기본 생성자 (자동으로 생성해줌)
    public Course() {
        System.out.println("기본 생성자!");
    }

    //매개변수 있는 생성자 (기본 생성자 생성해줘야함)
    public Course(String title, String tutor, int days) {
        this.title = title;
        this.tutor = tutor;
        this.days = days;
    }

    // setter : private인 멤버변수를 변경!
    public void setTitle(String title) {
        this.title = title;
    }
    public void setTutor(String tutor) {
        this.tutor = tutor;
    }
    public void setDays(int days) {
        this.days = days;
    }

    //getter
    public String getTitle() {
        return this.title;
    }
    public String getTutor() {
        return this.tutor;
    }
    public int getDays() {
        return this.days;
    }
}
```

[main 메서드]
```java
public class JavaPrac {
    public static void main(String[] args) {
        String title = "웹개발의 봄, Spring";
        String tutor = "Moonz";
        int days = 35;
        Course course = new Course(title, tutor, days);
        course.setTitle(title);
        course.setTutor(tutor);
        course.setDays(days);
    }
}
```
