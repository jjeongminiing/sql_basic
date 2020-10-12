# sql_basic
## sql 기본 명령어 학습

*새 쿼리창 여는 단축키 : `Ctrl+T`


### SQL 쿼리 작성 순서
1. SELECT
2. FROM
3. WHERE
4. GROUP BY
5. HAVING
6. ORDER BY
7. LIMIT

### SQL 실행 순서(해석시 유의)
1. **FROM**
2. **WHERE**
3. **GROUP BY**
4. **HAVING**
5. **SELECT**
6. **ORDER BY**
7. **LIMIT**


### 각 단계별 의미
1. FROM : 어느 테이블을 대상으로 할 것인지를 먼저 결정합니다.
2. WHERE : 해당 테이블에서 특정 조건(들)을 만족하는 row들만 선별합니다.
3. GROUP BY : row들을 그루핑 기준대로 그루핑합니다. 하나의 그룹은 하나의 row로 표현됩니다.
4. HAVING : 그루핑 작업 후 생성된 여러 그룹들 중에서, 특정 조건(들)을 만족하는 그룹들만 선별합니다.
5. SELECT : 모든 컬럼 또는 특정 컬럼들을 조회합니다. SELECT 절에서 컬럼 이름에 alias를 붙인 게 있다면, 이 이후 단계(ORDER BY, LIMIT)부터는 해당 alias를 사용할 수 있습니다.
6. ORDER BY : 각 row를 특정 기준에 따라서 정렬합니다.
7. LIMIT : 이전 단계까지 조회된 row들 중 일부 row들만을 추립니다.


CREATE DATABASE <데이터베이스 이름> : 데이터베이스 생성


### 조건에 따라 추출
`FROM service_name_main.member WHERE age NOT BETWEEN 30 and 39;` 서비스 고객 연령대가 30~39세인 경우
- WHERE age IN (20, 30)은 age 컬럼의 값이 20이거나, 30이어야 한다는 조건

`SELECT * FROM service_name_main.member WHERE sign_up_day BETWEEN '2018-01-01' AND '2018-12-31';` 2018년도 서비스가입자

`SELECT * FROM service_name_main.member WHERE address LIKE '서울%' ;` 주소 컬럼에 서울이 포함되는 값 조회
- LIKE 뒤의 'c_____@%' 이 표현식은 c로 시작하고, 그 뒤에 다섯 글자 그리고 골뱅이 하나, 그 뒤에 임의의 길이를 가진 문자열이 있는 문자열 패턴을 나타냄
- 회원 아이디가 c로 시작하고, 6자리 아이디인 email 주소를 가진 회원들을 조회

`SELECT * FROM service_name_main.member WHERE gender ≠ 'm';` : 남자가 아닌 회원 조회. =, ≠, <> 로 표현


### DATE
`SELECT * FROM service_name_main.member WHERE YEAR(birthday) = '1992';` : 1992년도에 태어난 회원 조회

`SELECT * FROM service_name_main.member WHERE MONTH(sign_up_day) IN (6,7,8) ;` : 여름(6,7,8월)에 태어난 회원 조회

`SELECT * FROM service_name_main.member WHERE DAYOFMONTH(sign_up_day) BETWEEN 15 AND 31;` 각달의 후반부(15일~31일)에 가입한 회원 조회

*DATEDIFF(날짜 a, 날짜 b) : '날짜 a-날짜 b'

`SELECT email, sign_up_day, DATEDIFF(sign_up_day, '2019-01-01') FROM service_name_main.member;` 각 회원이 가입한 일자가 2019년 1월 1일을 기준으로 몇일 이후인지 확인

`SELECT email, sign_up_day, CURDATE(), DATEDIFF(sign_up_day, CURDATE()) FROM service_name_main.member;` 오늘 날짜를 기준으로 확인 (CURDATE: 오늘날짜)

`SELECT email, sign_up_day, DATEDIFF(sign_up_day, birthday) / 365 FROM service_name_main.member;` 

*DATE_ADD() 날짜 더하기, DATE_SUB() 날짜 빼기

`SELECT email, sign_up_day, DATE_ADD(sign_up_day, INTERVAL 300 DAY) FROM service_name_main.member;` 가입일 기준으로 300일 이후의 날짜

`SELECT email, sign_up_day, DATE_SUB(sign_up_day, INTERVAL 250 DAY) FROM service_name_main.member;` 가입일 기준 250일 이전의 날짜

*UNIX_Timestamp : DATE 타입의 값을 1970년 1월 1일을 기준으로 총 몇초가 지났는지로 나타낸 값

`SELECT email, sign_up_day, UNIX_TIMESTAMP(sign_up_day) FROM service_name_main.member ;`



### 복수의 조건 찾기
```SELECT * FROM service_name_main.member
   WHERE gender = 'm'
   AND address LIKE '서울%'
   AND age BETWEEn 25 and 29;
   
서울시에 거주중인 25~29세 남자  
```

```SELECT * FROM service_name_main.member
   WHERE (gender = 'm' AND height >= 180)
    OR (gender = 'f' AND height >= 170)
   ORDER BY height ASC;
   
키 180이상의 남자와 170이상의 여자, 키 순으로 오름차순 (DESC: 내림차순)
```

```
SELECT * FROM service_name_main.member
WHERE gender = 'm'
  AND weight >= 70
ORDER BY height DESC;

성별이 남자고, 무게가 70 이상인 회원들을 키 내림차순으로 정렬
```

```
SELECT sign_up_day, email FROM service_name_main.member
ORDER BY YEAR(sign_up_day) DESC, email ASC;

sign_up_day, email컬럼에서 연도는 내림차순, 이메일은 오름차순
```

```
SELECT * FROM service_name_main.member
ORDER BY sign_up_day DESC
LIMIT 10;

10개 한정 보기
```

```
SELECT * FROM service_name_main.member
ORDER BY sign_up_day DESC
LIMIT 8, 2;

9번째 row부터 2개만 보기
```

`SELECT*FROM review WHERE comment LIKE BINARY '%yummy%';` comment 컬럼의 문자열값에 'yummy'가 포함된 row들을 조회하는데 'Yummy'는 제외하고 싶을 때

`SELECT*FROM sales ORDER BY CAST (registration_num AS signed); ` sales 테이블에서 TEXT 타입의 registration_num에 있는 숫자값들을 기준으로 정렬할때 오름차순

`SELECT COUNT(email) FROM service_name_main.member;` 이메일 칼럼의 총 행 수를 알 수 있음. (null의 개수는 제외하고 셈)

```
SELECT MAX(height) FROM service_name_main.member; height 컬럼에서 가장 큰 키
MIN은 최소값

AVG는 평균값

SUM 합계

STD 표준편차

ABS 절대값

SQRT 제곱근

CEIL 올림

FLOOR 내림

ROUND 반올림
```

`SELECT * FROM service_name_main.member WHERE address IS NOT NULL;` 널 제외, IS NULL ⇒ 널만

```
SELECT * FROM service_name_main.member
WHERE height IS NULL
  OR weight IS NULL
  OR address IS NULL;

세 칼럼 중 하나라도 null이 있는 경우를 조회
```

```
SELECT
COALESCE(height, '####'),
COALESCE(height, '—-'),
COALESCE(height, '@@@')
FROM service_name_main.member;

height 컬럼의 null을 일반적인 단어로 바꿀때
```

```
SELECT
COALESCE(height, '####'),
COALESCE(height, '—-'),
COALESCE(height, '@@@')
FROM service_name_main.member;
```

```
SELECT 
  email,
  height,
  weight,
  weight / ((height/100) * (height/100)) AS BMI
FROM service_name_main.member;

BMI구할 때(컬럼끼리 덧셈, 뺄셈, 곱셈, 나눗셈 등 간단한 연산이 가능함

컬럼 이름을 BMI로 설정 (Alias별명,별칭 붙이기)

AS 없이 스페이스키 하나만으로도 가능은함. AS 붙여주는게 좋긴함
```

```
SELECT
  email,
  CONCAT(height, 'cm', ', ', weight, 'kg') AS '키와 몸무게',
  weight / ((height/100) * (height/100)) AS BMI
FROM service_name_main.member;

컬럼과 컬럼을 이어붙일 때는 CONCAT
```

```
조건에 따라 새로운 컬럼을 만들 때

SELECT 
	email, 
    CONCAT(height, 'cm', ', ', weight, 'kg') AS '키와 몸무게',
    weight / ((height/100) * (height/100)) AS BMI,
(CASE
		WHEN weight IS NULL OR height is NULL THEN '비만 여부 알 수 없음'
		WHEN weight / ((height/100) * (height/100)) >= 25 THEN '과체중 또는 비만'
		WHEN weight / ((height/100) * (height/100)) >= 18.5
			AND weight / ((height/100) * (height/100)) <25
			THEN '정상'
		ELSE '저체중'
END) AS 'obesity_check'
FROM copang_main.member
ORDER BY obesity_check ASC;

```

`SELECT IFNULL(height, 'N/A') FROM service_name_main.member;` 첫번째 인자가 null인 경우에는 두번째 인자를 표시하고 null이 아닌 경우 해당값을 그대로 표현

`SELECT IF(height IS NOT NULL, height, 'N/A') FROM service_name_main.member;` 첫번째 인자가 null인 경우에는 두번째 인자를 표시하고 null이 아닌 경우 해당값을 그대로 표현


```
SELECT 
    name,
    price,
    price/cost AS 'price/cost',
(CASE 
    WHEN price/cost >= 1.7 THEN 'A. 고효율 메뉴'
    WHEN price/cost >= 1.5
        AND price/cost < 1.7 THEN 'B. 중효율 메뉴'
    WHEN price/cost >= 1
        AND price/cost < 1.5 THEN 'C. 저효율 메뉴'
END) AS 'efficiency'
FROM pizza_price_cost
ORDER BY efficiency DESC, price ASC
LIMIT 6;
```

`SELECT(COUNT(DISTINCT(gender)) FROM member;` gender 컬럼에 존재하는 고유값의 개수를 구해줌. pandas의 len(list(unique)) 기능 DISTINCT가 unique

`SELECT*, LENGTH(address) FROM service_name_main.member;` 문자열의 길이 구하는 함수

`SELECT email, UPPER(email) FROM service_name_main.member;` 문자열을 모두 대문자로 바꾸는 함수. LOWER는 모두 소문자로

`SELECT email, LPAD(age, 10, '0') FROM service_name_main.member;`  age컬럼의 값을 왼쪽에 문자 0을 붙여서 총 10자리를 만드는 함수. RPAD는 오른쪽에

`TRIM, LTRIM, RTRIM` 문자열에 존재하는 공백 제거.파이썬의 strip

```
SELECT gender,
  COUNT(*),
  AVG(height),
  MIN(weight),
FROM service_name_main.member 
GROUP BY gender;

성별, 성별 count, 평균 키, 최소 몸무게
```

```
SELECT
  SUBSTRING(address, 1, 2) as region,
  gender,
  COUNT(*),
FROM service_name_main.member
GROUP BY
  SUBSTRING(address, 1, 2),
  gender;
  
주소 앞 2글자 기준으로, gender와 주소 기준으로 카운트
```

```
SELECT
  SUBSTRING(address, 1, 2) as region,
  gender,
  COUNT(*),
FROM service_name_main.member
GROUP BY
  SUBSTRING(address, 1, 2),
  gender
HAVING region = '서울';

region 칼럼이 서울인 값만 조회
```

```
SELECT
  SUBSTRING(address, 1, 2) as region,
  gender,
  COUNT(*),
FROM service_name_main.member
GROUP BY
  SUBSTRING(address, 1, 2),
  gender
HAVING region = '서울'
  AND gender = 'm';
  
서울에 사는 남성회원 그룹
```


```
SELECT
  SUBSTRING(address, 1, 2) as region,
  gender,
  COUNT(*),
FROM service_name_main.member
GROUP BY
  SUBSTRING(address, 1, 2),
  gender
HAVING region IS NOT NULL
ORDER BY
  region ASC,
  gender DESC ; 
  
region이 null이 아닌 경우에 region 오름차순, gender 내림차순
```


### GROUP BY 규칙
GROUP BY를 사용할 때는, **SELECT 절에는**
**(1) GROUP BY 뒤에서 사용한 컬럼들 또는**
**(2) COUNT, MAX 등과 같은 집계 함수만**
**쓸 수 있음. 이건 거꾸로 말해 GROUP BY 뒤에 쓰지 않은 컬럼 이름을 SELECT 뒤에 쓸 수는 없다는 말임.**
**GROUP BY 뒤에 쓰지 않은, 그러니까 그루핑 기준으로 사용하지 않은 컬럼명을 SELECT 절 뒤에 써서 조회하려고 하면,
**각 그룹의 row들 중에서 해당 컬럼의 값을 어느 row에서 가져와야할지 결정할 수가 없다.**
다만 COUNT, MAX 등과 같은 집계 함수는 사용할 수 있음.


### WITH ROLLUP : 

중간중간 세부사항들을 합쳐주는 역할

GROUP BY 뒤에 나오는 그루핑 기준의 등장 순서에 맞춰 계층적인 부분 총계를 보여준다. => GROUP BY 뒤에나오는 그루핑 기준의 등장 순서에 따라 WITH ROLLUP이 출력하는 결과가 달라짐.



### GROUPING
```
SELECT YEAR(sign_up_day) AS s_year, gender, SUBSTRING(address, 1, 2) AS region,
GROUPING(YEAR(sign_up_day)), GROUPING(gender), GROUPING(SUBSTRING(address, 1, 2)),
COUNT(*)
FROM service_name_main.member
GROUP BY YEAR(sign_up_year), gender, SUBSTRING(address, 1, 2) WITH ROLLUP
ORDER BY s_year DESC;

```
NULL값의 출처를 알 수 없을 때, 구분할 수 있도록 해주는 함수 GROUPING. 
(1) 실제로 NULL을 나타내기 위해 쓰인 NULL인 경우에는 0,
(2) 부분 총계를 나타내기 위해 표시된 NULL은 1
을 리턴해서 둘을 구분하게 해주는 함수입니다.


### 테이블 병합

```
SELECT
	item.id,
        item.name,
        stock.item_id,
        stock.inventory_count
FROM item LEFT OUTER JOIN stock
ON item.id = stock.item_id

해석)item 테이블을 기준으로 stock 테이블을 병합

item의 id컬럼, item의 name, stock의 item_id,  stock의 inventory_count 칼럼을 보여줘

이때 item의 id칼럼과 stock의 item_id칼럼은 같은 값이야
```


```
SELECT
	i.id,
        i.name,
        s.item_id,
        s.inventory_count
FROM item  (AS) i LEFT OUTER JOIN stock (AS) s
ON item.id = stock.item_id 

Alias붙일때는 다른 라인도 모두 맞춰줘야함
```


```
SELECT 
	old.id AS old_id,
    old.name AS old_name,
    new.id AS new_id,
    new.name AS new_name
FROM item AS old LEFT OUTER JOIN item_new AS new
ON old.id = new.id ;SELECT
				i.id,
        i.name,
        s.item_id,
        s.inventory_count
FROM item  (AS) i LEFT OUTER JOIN stock (AS) s
ON item.id = stock.item_id   
```

```
SELECT 
    p.name, 
    COALESCE(s.sales_volume, '판매량 정보 없음') AS '판매량' 
FROM pizza_price_cost AS p LEFT OUTER JOIN sales AS s ON p.id = s.menu_id; 
```

```
SELECT 
	old.id AS old_id,
    old.name AS old_name,
    new.id AS new_id,
    new.name AS new_name
FROM item AS old LEFT OUTER JOIN item_new AS new
ON old.id = new.id ;
#ON 대신에 USING도 가능


# LEFT OUTER JOIN : item 기준, 둘 중 하나있을 때 가능
# RIGHT OUTER JOIN : item_new 기준으로 JOIN, 둘 중 하나 있을 때 가능
# INNER JOIN : 둘다 있는 경우
```

```
SELECT * FROM item
UNION
SELECT * FROM item_new

#통합하기, concat 기능인듯, 합집합

UNION 연산과 UNION ALL 연산은 둘다 합집합을 구하되, 전자는 중복을 제거해서 보여주고, 후자는 그런 작업없이 두 테이블을 합친 결과를 그대로 보여준다는 차이가 있다.

만약 중복을 제거하고 깔끔하게 보는 것이 중요한 경우에는 UNION 연산자를 사용하고, 중복을 제거하게 되면 정보 누락이 발생할 수 있는 경우에는 UNION ALL 연산자를 사용하면 됨\
```


### 세가지 테이블 조인할 때

```
SELECT
  i.name, i.id,
  r.item_id, r.star, r.comment, r.mem_id,
  m.id, m.email
FROM
  item AS i LEFT OUTER JOIN review AS r
    ON r.item_id = i.id
  LEFT OUTER JOIN member AS m
    ON r.mem_id = m.id;
```   


```
SELECT  i.id, i.name, AVG(star), COUNT(*)
FROM
		item AS i LEFT OUTER JOIN review AS r
			ON r.item_id = i.id
		LEFT OUTER JOIN member AS m
			ON r.mem_id = m.id
WHERE m.gender = 'f'
GROUP BY i.id, i.name
HAVING COUNT(*) > 1
ORDER BY 
		AVG(star) DESC,
		COUNT(*) DESC;

리뷰수가 1개 이상인 상품 중, 별점이 높->낮은 순
```

```
SELECT 
    YEAR(i.registration_date) AS '등록 연도', 
    count(*) AS '리뷰 개수',
    AVG(star) AS '별점 평균값'
FROM 
    item AS i INNER JOIN review AS r
        ON i.id = r.item_id
    INNER JOIN member AS m
        ON r.mem_id = m.id
WHERE i.gender = 'u'
GROUP BY YEAR(i.registration_date)
HAVING COUNT(*) > 10
ORDER BY AVG(star) DESC;
```


### 서브쿼리

서브쿼리는 SQL문 안에 부품처럼 들어있는 SELECT문. 하나의 SQL문 안에서 원하는 쿼리를 자유자재로 꺼낼 수 있음.

```
SELECT i.id, i.name, AVG(star) AS avg_star
FROM item AS i LEFT OUTER JOIN review AS r
ON r.item_id = i.id
GROUP BY i.id, i.name
HAVING avg_star < (SELECT AVG(star) FROM review)
ORDER BY avg_star DESC;
```

```
SELECT i.id, i.name, AVG(star) AS avg_star
FROM item AS i LEFT OUTER JOIN review AS r
ON r.item_id = i.id
GROUP BY i.id, i.name
HAVING avg_star < (SELECT AVG(star) FROM review)
ORDER BY avg_star DESC;
```

```
SELECT
		id,
        name,
        price,
        (SELECT AVG(price) FROM item) AS avg_price
FROM copang_main.item
WHERE price > (SELECT AVG(price) FROM item);
```


```
- 상품 중 리뷰가 최소 3개 이상 달린 상품들의 정보만 보고싶을 경우 → 서브쿼리로 해결가능

    - IN은 뒤에있는 값 중 하나라도 있으면 만족
    
    - 리뷰 테이블의 각 row를 item_id 기준으로 그룹핑하고, 포함된 row 수가 3개 이상인 그룹들만 추린다


SELECT*FROM item
WHERE id IN
(
SELECT item_id
FROM review
GROUP BY item_id HAVING COUNT(*) >= 3
);
```


```
서브쿼리 활용: 주요 지역 주소 중에 리뷰수가 제일 많고 , 지역이름이 안드/NULL이 아님

⇒ TABLE형태의 서브쿼리를 만들때는 반드시 alias 붙여줘야함


SELECT
  AVG(review_count),
  MAX(review_count),
  MIN(review_count)
FROM
(SELECT
  SUBSTRING(address, 1, 2) AS region,
  COUNT(*) AS riview_count
FROM review AS r LEFT OUTER JOIN member AS m
ON r.mem_id = m.id
GROUP BY SUBSTRING(address, 1, 2)
HAVING region IS NOT NULL
  AND region != '안드) AS review_count_summary;
  
```
