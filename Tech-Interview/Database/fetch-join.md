# 개요
패치 조인(Fetch Join)은 **연관된 엔티티(Entity)나 컬렉션(Collection)을 한번에 조회해주는 기능**을 가지고 있습니다.
즉, 1개의 select 문을 사용해서 부모 객체와 연관된 엔티티나 컬렉션의 값을 초기화시킬 수 있습니다.

그렇기 때문에 애플리케이션에서는, 해당 결과로 연관된 모든 엔티티가 있을 것이라 가정하고 사용해야 합니다???
- :warning: 만약 패치 조인에 별칭을 잘 못 명시하여 컬렉션 결과를 필터링하게 되면 객체 상태와 DB의 상태가 일치하지 않아 일관성이 깨질 수 있습니다. 

> 이는 `JPQL`에서 성능 최적화를 위해 제공하는 기능입니다.

성능 최적화를 위해 제공된다고 언급되는 이유는, Join의 N+1 문제를 해결해주기 때문입니다.
## Join과 Fatch Join이 무슨 차이가 있어?
- Join<br>
select할 때, 연관 엔티티가 존재하더라도 주요 select하는 엔티티만 조회해서 영속화됩니다.<br>
즉, 조인 대상이 되는 엔티티에 대한 영속성은 관여하지 않습니다.<br>
- 언제 쓰는가?<br> 
연관 엔티티가 검색조건으로만 필요하고 데이터는 필요하지 않은 경우에 주로 사용됩니다.<br>  예) 3반에 속한 "문윤지"라는 학생의 


- Fecth Join<br>
조회의 주체가 되는 엔티티 이외에 연관 엔티티도 함께 select하여 모두 영속화합니다.<br>
FetchType이 `Lazy`로 설정된 연관 엔티티인 경우에도 영속화됩니다.<br>

<i>그래서 연관 엔티티가 이미 영속성 컨텍스트에 들어있어 따로 쿼리가 재실행되지 않기 때문에 N+1 문제를 해결할 수 있습니다.</i>

- 언제 쓰는가?<br>
무작정 fetch join을 사용해서 전부 영속성 컨텍스트에 올리는게 아닌, 로직에 꼭 필요한 엔티티만 영속성 컨텍스트에 올려서 사용해야 DB와 객체 간의 일관성을 잘 고려할 수 있습니다.

# xToOne 관계 (OneToOne, ManyToOne)


Student와 Grade는 다대일 관계입니다.
fetch join을 이용해서 Student 컬렉션을 조회하면서 연관된 부모 객체 Grade 엔티티도 함께 조회할 수 있습니다.


Class와 Student는 일대다



# 사용 방법

```sql
SELECT ~~ FROM Team t join fetch t.members
```
