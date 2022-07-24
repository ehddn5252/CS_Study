# 자주 사용하는 SQL select query
날짜: 2022.07.24

[W3Schools SQL 공부하기](https://www.w3schools.com/mysql/mysql_sql.asp)
- W3Schools 에 SQL이 잘 정리되어 있으니 참고하면 좋다.

<details>
<summary span style="font-size:18px">기본 select 검색 조건</summary>
기본 select form

```sql
select [찾을 column]
from [table 명]
where [조건]
```
ex)
```sql
select name
from board,user
where user.userId=board.userid
```

## 검색 조건
[참고 자료](https://gmlwjd9405.github.io/2019/04/25/db-sql-select.html)
- AND, OR, NOT
```SQL
-- 조건식1 AND 조건식2
SELECT * FROM sample WHERE a<>0 AND b<>0; -- a열, b열이 모두 0이 아닌 행 검색

-- 조건식1 OR 조건식2
SELECT * FROM sample WHERE a<>0 OR b<>0; -- a열이 0이 아니거나 b열이 0이 아닌 행 검색

-- NOT 조건식
SELECT * FROM sample WHERE NOT(a<>0 OR b<>0); --  a열이 0이 아니거나 b열이 0이 아닌 행을 제외한 나머지 행 검색
```
- AND 는 OR 보다 우선순위가 높아 괄호를 잘 써줘야 한다.

- 패턴 매칭에 의한 검색

문법:
```sql
[column 명] LIKE '패턴'
```
예시
```sql
-- text 열에 'Start'을 포함하는 행을 검색
SELECT * FROM sample WHERE text LIKE 'Start%'; -- (전방일치) 
SELECT * FROM sample WHERE text LIKE '%Start%'; -- (중간일치) 

-- text 열에 '%'(메타문자)을 포함하는 행을 검색
SELECT * FROM sample WHERE text LIKE '%\%%'; -- 이스케이프 문자(/) 사용 

-- text 열에 'It's'을 포함하는 행을 검색
SELECT * FROM sample WHERE text LIKE 'It''s'; -- '를 문자열 상수 안에 포함할 경우 2개를 연속해서 기술 
```

</details>
<details>
<summary>order by</summary>
<div markdown="1">

## order by
- 조건에 따라 정렬하는 명령어로 뒤에 나오는 COLUMN 명 기준으로 정렬한다. 
- column 이 여러 개가 오면 앞의 column 부터 비교하면서 같다면 다음 column의 비교기준에 따라 비교한다.
- DESC 는 내림차순, ASC 는 오름차순 옵션이다.

```sql
select [찾을 column]
from [table 명]
where [조건]
order by [정렬 기준 column 명] DESC, [정렬 기준 column 명] asc, [정렬 기준 column 명]
```

</div>
</details>
<details>

<summary>limit</summary>
<div markdown="1">

## limit
- 
- 파라미터가 1개만 올 시에 보여줄 행의 개수이다
- 파라미터가 2개(n,m) 올 시에는 위에서 부터 n+1번째 행부터 m개를 보여준다는 뜻이다.
    - 여기에서 n 은 0부터 시작하므로 0이 첫번째 행이다.
```sql
select name
from board,user
where user.userId=board.userid
limit 3, 5
```
해석: board와 user에서 user의 userId와 board의 userid 가 같은 행의 이름을 위에서 4번째부터(인덱스가 0부터 시작) 5개 보여준다.
</div>
</details>
<details>
<summary span style="font-size:20px">group by</summary>

## group by
- group by 는 유형별로 갯수를 알고 싶을 때에 컬럼별 데이터를 그룹화 할 수 있는 명령어 입니다.
- 
- 다음 블로그에서 잘 정리를 해 놓아서 참고해서 정리했습니다.

[group by 참고 자료](https://extbrain.tistory.com/56)
### 컬럼 그룹화
```sql
select [column명] from [table 명] group by [그룹화할 column 명];
```

### 조건 처리 후에 컬럼 그룹화
```sql
select [column명] from [table 명] where [조건식] group by [그룹화 할 column 명];
```

### 컬럼 그룹화 후에 조건 처리
```sql
select [column 명] group by [그룹화할 컬럼] having [조건식];
```

### 조건 처리 후 컬럼 그룹화 후 조건 처리
```sql
select [column 명] 
from [table 명]
where [조건식]
group by [그룹화할 컬럼명]
having [조건식];
```

### order by 가 존재하는 경우
```sql
select [column 명]
from [table 명]
where [조건식]
group by [그룹화 할 컬럼]
having [조건식]
order by [column 명];
```
예시1
- money 50000 이상인 type 그룹화하여 name 갯수를 가져온 후, 그 중에 갯수가 2개 이상인 데이터를 money 내림차순 정렬로 조회
쿼리
```sql
select money, count(name) as cnt from things
where money >= 50000
group by type
having cnt>=2
order by money desc;
```

예시2
- 보호소에서는 몇 시에 입양이 가장 활발하게 일어나는지 알아보려 합니다. 0시부터 23시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문을 작성해주세요. 이때 결과는 시간대 순으로 정렬해야 합니다.

```sql
set @HOUR = -1;

select (@HOUR:= @HOUR +1) as HOUR,
(select count(*) from animal_outs where hour(datetime)= @HOUR) as count
from animal_outs 
where @hour <24
```

설명: HOUR 이라는 변수를 -1로 설정, 그 후에 HOUR이 24 미만인 때까지 hour +=1 반복해서 보여주고 datetime이 hour에 맞는 것들의 갯수를 보여준다.
</details>

<details>
<summary span style="font-size:22px">join</summary>

## join

![image](https://user-images.githubusercontent.com/51036842/180635402-ae9badb3-ba16-4459-874f-f8d06127db3f.png)
- [join 참고 블로그](https://pearlluck.tistory.com/46)


- join 은 둘 이상의 테이블을 연결해서 데이터를 검색하는 방법이다. 연결하려면 테이블들이 적어도 하나의 컬럼을 공유하고 있어야 하고, 이 공유하고 있는 컬럼을 PK 또는 FK 값으로 사용한다.

- 여기에선 크게 3가지만 알면 된다. 
- 1) INNER JOIN(교집합), 2) LEFT/RIGHT JOIN(부분 집합), 3) OUTER JOIN(합집합) (mysql 에는 없음)
- MySQL은 OUTER JOIN이 없어서 LEFT join + RIGHT JOIN을 합쳐서 사용해야 한다.
    - INNER JOIN 은 교집합으로 해당 column이 동일할 때에만 그 column을 합쳐준다.
    - left join은 왼쪽에 있는 table 은 모두 보여주고 왼쪽 table에 일치하는 것이 없는 오른쪽 table에는 null 정보로 채워진다.
    - outer join 
사용법 예시

```sql
select * 
from board right outer join user
on board.user_id = user.user_id
```

- 해당 user 가 쓴 게시글을 user_id로 연결하기 right outer join 이라서 만약 해당 user가 쓴 글이 없다고 해도 그 user 정보는 보여진다


예시1 (프로그래머스 문제)
- 천재지변으로 인해 일부 데이터가 유실되었습니다. 입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 ID 순으로 조회하는 SQL문을 작성해주세요.
- 본 문제는 Kaggle의 "Austin Animal Center Shelter Intakes and Outcomes"에서 제공하는 데이터를 사용하였으며 ODbL의 적용을 받습니다.

```sql
SELECT outs.animal_id, outs.name  
from animal_ins INS 
right outer join animal_outs OUTS 
on INS.animal_id = OUTS.animal_id 
where INS.ANIMAL_ID is null
```
- 설명: animal_ins에 animal_id 기준으로 animal_outs 테이블 정보가 부가적으로 붙게 만들고 들어온 정보가 없는 행을 찾아서 보여준다. 


예시2 (프로그래머스 문제)
- 보호소에서 중성화 수술을 거친 동물 정보를 알아보려 합니다. 보호소에 들어올 당시에는 중성화1되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL 문을 작성해주세요.

```sql
SELECT ins.animal_id, ins.animal_type, ins.name
from animal_ins ins left outer join animal_outs outs
on ins.animal_id = outs.animal_id
where ins.SEX_UPON_INTAKE like "Intact%" and (outs.SEX_UPON_OUTCOME like "Neutered%" or  outs.SEX_UPON_OUTCOME like "Spayed%")
ORDER BY INS.ANIMAL_ID
```
- 설명: animal_ins(들어온 기록) 와 animal_outs(나간 기록) 를 left outer join 해서 들어온 기록에는 앞에 intact라는 문자가 있고, 나간 기록에는 Natured나 Spayed 라는 이름이 붙은 행을 animal_id 순으로 정렬하여 보여준다. 
</details>

<details>
<summary>참고 자료</summary>

### 참고 자료
- [group by 참고 자료](https://extbrain.tistory.com/56)
- [programmers 고득점 킷](https://school.programmers.co.kr/learn/challenges?tab=sql_practice_kit)
- [sql 쿼리 예시](https://gmlwjd9405.github.io/2019/04/25/db-sql-select.html)
- [서브 쿼리 참고 블로그](https://mozi.tistory.com/233)
</details>

