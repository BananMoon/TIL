## Java로 캘린더 만들기
#### first.Calendar 패키지 구조
- Calendar.java
- Prompt.java (main)

#### 추가한 기능
1. 월과 년도 입력
2. 입력한 월에 해당하는 달력 출력
    - 문제) 모든 달력이 일요일에 시작함
3. 윤년

#### 코드
**Calnedar.java**
```java
package first.calendar;

// Ctrl+Alt+L : 코드 자동 정렬
public class Calendar {
    //함수로!
    //변수도 메소드 밖으로!
    //static을 붙여야 아래 main함수에서 Calendar 객체이용해서 공유 가능
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

    public void printCalendar(int year, int month) {
        System.out.printf("    <<%4d%3d>>\n", year, month);    //3칸 오른쪽 정렬
        System.out.println(" SU MO TU WE TH FR SA");
        System.out.println("----------------------");
        int maxDays = getMaxDaysOfMonth(year, month);
        for (int i = 1; i <= maxDays; i++) {
            System.out.printf("%3d",i);
            if (i % 7 == 0) {
                System.out.println();
                continue;
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
// shell 기능(사용자 입력 기능) 분리

package first.calendar;
import java.util.Scanner;

public class Prompt {

    private static final String PROMPT = "cal> ";

    public void runPrompt() {
        Scanner scanner = new Scanner(System.in);
        Calendar cal = new Calendar();

        //횟수 입력  -> 무한 반복으로 변경. -1 입력 시 while문 종료
//        System.out.println("반복할 횟수를 입력하세요: ");
//        int times = scanner.nextInt();
        int month, year = 0;
        while (true) {
            System.out.println("연도를 입력하세요: ");
            System.out.print(PROMPT);
            year = scanner.nextInt();
            System.out.println("달을 입력하세요: ");
            System.out.print(PROMPT);
            month = scanner.nextInt();
            //아래 print문에서 오류발생하므로
            if (month == -1) {
                break;
            }
            if (month > 12) {
                continue;
            }

            cal.printCalendar(year, month);
        }
        System.out.println("bye~");
        scanner.close();
    }

    public static void main(String[] args) {
        Prompt p = new Prompt();
        p.runPrompt();
    }
}
```
