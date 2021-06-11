<h1> Today I Learned </h1>
1. Chrome app with VanilaJS (Start)<br>
2. algorithm study 

<h2> 2. algorithm study(2)  </h2>
<h2> 02. 문자열 뒤집기 </h2>
<h3> <문제> 문자열을 뒤집는 함수를 작성하라. 입력값은 문자 배열이며, 리턴 없이 리스트 내부를 직접 조작하라.</h3><br>
입력 ["h","e","l","l","o"] 출력 ["o","l","l","e","h"]

- 풀이1) 투 포인터를 이용한 스왑<br>
*투 포인터 : 두개의 포인터를 이용해 범위를 조정해가며 풀이하는 방식
```
def reverseString(self, s: List[str]) -> None:
    left, right = 0, len(s)-1
    while left<right:
        s[left], s[right] = s[right], s[left]
        left+=1
        right-=1
```

- 풀이2) Pythonic way
```
def reverseString(self, s: List[str]) -> None:
    s.reverse()
```

<h2> 03. 로그파일 재정렬 </h2>
<h3> <문제> 로그를 재정렬하라. 기준은 다음과 같다.</h3>

- 로그의 가장 앞 부분은 식별자다.
- 문자로 구성된 로그가 <b>숫자로그보다 앞에</b> 온다.
- 식별자는 순서에 영향을 끼치지 않지만, 문자가 동일한 경우 식별자순으로 한다.
- <b>숫자로그는 입력 순서대로</b> 한다.

<h4>정리</h4>

- 숫자는 정렬하지 않아도 된다. (문자는 정렬한다!)
- 문자와 숫자를 구분해서 문자를 먼저 앞에.
- 숫자도 문자타입으로 되어있으므로 바꿔준다.

<h3>1. 람다와 + 연산자를 이용</h3>

```
def reorderLogFiles(logs):
    letters, digits = [], []

    for log in logs: # logs에 있는 문자열마다
        if log.split()[1].isdigit():
            digits.append(log)
        else:
            letters.append(log)
    # 정렬 기준이 될 2개의 키는 람다식으로 정렬
    letters.sort(key=lambda x: (x.split()[1:], x.split()[0]))  # letters에 있는 문자들을 정렬! 문자가 동일하면 식별자 기준으로 정렬
    return letters + digits 
    # 문자열기준 먼저 정렬 후, 뒤에 숫자열 추가 (숫자는 정렬안해도됨)
    # 한번 확인해보자!

logs = ["dig1 8 1 5 1","let1 art can","dig2 3 6","let2 own kit dig","let3 art zero"]
p=reorderLogFiles(logs)
```

- logs가 func에 들어갔을 때, 식별자 다음 순서에 오는 게(x.split()[1]) 문자면 letters로, 숫자면 digits로 추가(append)한다.

```
print(p) 
 ['let1 art can', 'let3 art zero', 'let2 own kit dig', 'dig1 8 1 5 1', 'dig2 3 6']
```

- a로 시작하는 art cat, art zero가 먼저 정렬되고, 그후에 own kit dig, 그리고 숫자로 구성된 문자열들을 추가해서 return한다.

> [람다 표현식]<br>
> 식별자 없이 실행 가능한 함수로, 함수 선언 없이도 하나의 식으로 함수를 단순하게 표현할 수 있음. <br>
> 다만, 더 간결하고 가독성이 높은 <b>리스트 컴프리헨션(List Comprehension)</b>을 주로 사용할 것임

<h2> 04. 가장 흔한 단어</h2>
<h3> <문제> 금지된 단어를 제외한 가장 흔하게 등장하는 단어를 출력하라. 대소문자 구분을 하지 않으며, 구두점(마침표, 쉼표)또한 무시한다.</h3>

<h4>정리</h4>
- 대소문자를 모두 소문자로 변경해야지
- 구두점을 없애야지 
- 금지된 단어는 찾아서 지워야지
- 딕셔너리로 각 단어의 등장 횟수를 세볼까?


### <Collections 모듈이란?>
- 데이터 처리를 위한 유용한 객체가 많이 있음(Counter, defaultdict, deque 등)

1. 카운트하기 - 보유한 주식의 종목별 주식(shares) 합산
> Counter() : dictionary를 확장한 것으로, 유사한 기능을 가지며 iterable의 원소의 개수를 셀 때 편리하게 사용할 수 있는 기능

```
portfolio = [
    ('GOOG', 100, 490.1),
    ('IBM', 50, 91.1),
    ('CAT', 150, 83.44),
    ('IBM', 100, 45.23),
    ('GOOG', 75, 572.45),
    ('AA', 50, 23.15)
]
```
<b>예시) IBM과 GOOG 종목 별 주식(shares) 합산</b>

```
from collections import Counter
total_shares = Counter()
for name, shares, price in portfolio:
    total_shares[name] += shares
    
total_shares['IBM'] # 150
total_shares['GOOG'] # 175
```

2. 일대다 매핑 : 하나의 키를 여러 값에 매핑
> defaultdict: dictionary를 만드는 dict의 서브 클래스로, 객체의 기본값으로 초기화할 수 있음<br>
> 예: defaultdict(<class 'list'>, {})<br>

<b> 예시) 2개의 튜플이 있는 하나의 종목에 주식(shares)과 가격(price) 튜플로 매핑</b>

```
from collections import defaultdict
holdings = defaultdict(list)
print(holdings) # defaultdict(<class 'list'>, {})
for name, shares, price in portfolio:
    holdings[name].append((shares, price))

print(holdings["AA"]) # [(50,23.15)]
print(holdings["GOOG"]) # [(100,490.1),(75, 572.45)]
```

3. 이력 유지하기
> 데크(deque)란?<br>
> 큐의 양방향으로 된 자료구조로 스택처럼, 큐처럼 사용 가능! 양쪽 방향에서 element를 추가&삭제할 수 있음
> 양 끝에 접근하여 삽입/제거할 경우, 일반적인 리스트의 연산 O(n)과 달리 O(1)이 소요된다.

```
deque.append(item) # 오른쪽 끝에 삽입
deque.appendleft(item) # 왼쪽 끝에 삽입
deque.pop() # 오른쪽 끝 element 가져오는 동시에 삭제
deque.popleft() # 왼쪽 끝 element 가져오는 동시에 삭제
deque.extend(array) # 배열을 순환하면서 오른쪽에 추가
deque.extendleft(array) # 배열을 순환하면서 왼쪽에 추가
deque.remove(item) # item을 deque에서 찾아 삭제
deque.rotate(num) # deque를 num만큼 회전(+는 오른쪽으로, -는 왼쪽으로 이동)

# rotate() 예시
deq = deque([1,2,3,4,5])
deq.rotate(1) # ([5,1,2,3,4])
deq.rotate(-2) # ([2,3,4,5,1])
```