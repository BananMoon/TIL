## Java로 캘린더 만들기
#### first.Calendar 패키지 구조
- Calendar.java
- Prompt.java (main)

#### 추가한 기능
1. 월과 년도 입력
2. 입력한 월에 해당하는 달력 출력
    - 문제) 모든 달력이 일요일에 시작함
      - 해결) 시작하는 요일을 입력받음
    - 문제) 일요일마다 다음 라인으로 넘어가야함
      - 해결) 규칙을 찾아 나머지가 0 혹은 7 - 시작하는 요일(int형) 일 때 line을 건너뜀
3. 윤년
4. 시작하는 요일을 입력받아 일반 달력 형태로 더 일치화함

#### 코드
**Calnedar.java**
```java
package first.calendar;

// Ctrl+Alt+L : 코드 자동 정렬
public class Calendar {
    //함수로!
    //변수도 메소드 밖으로!
    //static을 붙여야 아래 main함수에서 객체로 공유 가능
    private static final int[] MAX_DAYS = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

    //윤년
//1. 4로 나누어 떨어진다.
//2. 100으로 나누어 떨어지지 않으면서, 그중에선 400으로 나누어 떨어진다.
//3. 윤년은 4년에 하루씩 2월 29일을 주기한다.
    private static final int[] LEAP_MAX_DAYS = {31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
    public boolean isLeapYear(int year) {
        if (year%4 == 0 && (year%100 !=0 || year%400 == 0)) {
            return true;
        } else {
            return false;
        }
    }

    public int getMaxDaysOfMonth(int year, int month) {
        if (isLeapYear(year)) { //윤년이면 윤년형 배열 사용
            return LEAP_MAX_DAYS[month-1];
        } else {
            return MAX_DAYS[month - 1];
        }
    }

    public void printCalendar(int year, int month, String date) {
        System.out.printf("    <<%4d%3d>>\n", year, month);    //3칸 오른쪽 정렬
        System.out.println(" SU MO TU WE TH FR SA");
        System.out.println("----------------------");
        int idx = -1;
        String[] days = {"SU","MO", "TU", "WE", "TH", "FR", "SA"};
        int len = days.length;
        for (int j=0; j<len ; j++) {
            if (date.equals(days[j])) {
                idx = j;
            }
        }
        for (int i=0; i<len; i++) {
            if (i < idx) {
                System.out.print("   ");
            } else{
                break;
            }
        }
        int maxDays = getMaxDaysOfMonth(year, month);
        int count = 7- idx;
        //count가 7일 때는 나머지가 0임.
        int forLine = (count<7)? count:0;

        //first line
        for (int j=1;j<=count;j++) {
            System.out.printf("%3d",j);
        }
        System.out.println();

        //from second line to last
        for (int k= count+1; k <= maxDays; k++) {
            System.out.printf("%3d",k);
            if (k % 7 == forLine) {
                System.out.println();
            }
        }
        System.out.println();
    }
    // main 메서드는 Prompt 클래스로 이동
}
```
---
**Prompt.java**
```java
package first.calendar;

// shell 기능 분리

import java.util.Scanner;

public class Prompt {

    private static final String PROMPT = "cal> ";

    public void runPrompt() {
        //입력받은 달의 최대 일수
        Scanner scanner = new Scanner(System.in);
        Calendar cal = new Calendar();

        //횟수 입력
//        System.out.println("반복할 횟수를 입력하세요: ");
//        int times = scanner.nextInt();
        int month, year = 0;
        String date = "MO";
        while (true) {
            System.out.println("연도를 입력하세요: (exit:-1)");
            System.out.print(PROMPT);
            year = scanner.nextInt();
            if (year == -1)
                break;
            System.out.println("달을 입력하세요: (exit:-1)");
            System.out.print(PROMPT);
            month = scanner.nextInt();
            //아래 print문에서 오류발생하므로
            if (month == -1) {
                break;
            }
            if (month > 12 || month < 1) {
                System.out.println("잘못된 입력입니다.");
                continue;
            }
            System.out.println("첫번째 요일을 입력하세요. (SU, MO, WE, TH, FR, SA)");
            System.out.print(PROMPT);
            date = scanner.next();  //next(): 공백을 기준으로 입력. nextLine():라인을 기준으로 입력.
            cal.printCalendar(year, month, date);
        }
        //for문
//        for (int i=0;i<times; i++) {
//            System.out.print("달을 입력하세요: ");
//            int month = scanner.nextInt();
//            System.out.printf("%d월은 %d일까지 있습니다.\n", month, cal.getMaxDaysOfMonth(month));
//        }
        System.out.println("bye~");
        scanner.close();
    }

    public static void main(String[] args) {
        Prompt p = new Prompt();
        p.runPrompt();
    }
}
```
