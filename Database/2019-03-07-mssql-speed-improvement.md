# mssql 쿼리 팁 - group by 없이 카운트하는 방법 & 페이징 처리를 위한 전체 레코드 갯수 구하기

- Title : mssql 쿼리 팁 - group by 없이 카운트하는 방법 & 페이징 처리를 위한 전체 레코드 갯수 구하기
- Date : 2019-03-07
- Category: Database

프로시저 속도 개선하는 과정에서 배웠던 sql 팁으로, group by 없이 칼럼과 함께 갯수를 출력하는 방법과
페이징 처리에 필요한 전체 레코드 갯수를 구하는 방법에 대한 내용입니다.

관리자 페이지 유지보수 중에 한 프로시저의 실행속도가 굉장히 느린 것을 발견했다.  
실무자에게 확인해 본 결과 페이지 넘길 때마다 기본 일분은 기다린다는 답을 받았다.  
이 프로시저는 어떤 정보를 프로시저 내에서 페이징 처리를 하여 조회하는 쿼리로 4개의 left outer join 과 복잡한 case 조건의 where 절을 가졌다.  
심지어 left join 되는 테이블은 조건과 그룹 처리를 하는 서브쿼리로 구성되었다.

# Group by 없이 칼럼과 함께 갯수 출력하기

<span class="clr-grey">
처음에는 left join 때문이라고 생각했으나 아니였다.      
진짜 원인은 select 절에서 각 그룹의 갯수를 조회하는 서브쿼리의 문제였다.    
이를 left outer join 으로 바꿔봤지만 해결되지 않았고 Group by 처리하자니 조회할 컬럼 값이 너무 많았다.    
고민하다가 검색을 통해 좋은 방법을 찾았다.  
</span>

```sql
count(yy) over (partition by xx)
```

위는 Group by 없이 컬럼값들과 함께 갯수를 출력할 수 있는 쿼리이다.

```sql
count(seq) over (partition by groupSeq)
```

이를 참고해 select에서 서브쿼리로 조회하던 방식을 위와 같이 수정했더니 실행시간이 13초에서 3초 정도로 줄었다

# 페이징 처리에 필요한 전체 레코드 갯수를 구하는 방법

[전체갯수구하기 참고 블로그](https://m.blog.naver.com/monkeychoi/220629982940)

<span class="clr-grey">
하지만 아직 한국인을 만족시킬 속도가 아니였기에 속도를 잡아먹는 또 다른 원인을 찾아보았다.     
바로 전체 레코드 갯수를 구하는 부분이었다.  페이징처리를 위해서 전체 레코드 갯수를 구하는 건 필수였기에 해당 쿼리를 빼는 거는 불가능했다.   
</span>

기존 페이징 쿼리

```sql
;WITH myCTE AS
(
	SELECT
		COUNT(*) OVER() AS TOTALCNT,	-- 이 쿼리때문에 느려짐
		ROW_NUMBER() OVER (ORDER BY GroupSeq DESC, seq ASC) AS ROWNUM,
		*
	FROM DBO.MY_BOARD
)
SELECT * FROM myCTE WHERE ROWNUM BETWEEN (@I_PAGE -1) * @I_PAGESIZE) + 1 AND @I_PAGE * @I_PAGESIZE
```

수정 후 쿼리

```sql
;WITH myCTE AS
(
	SELECT
		ROW_NUMBER() OVER (ORDER BY GroupSeq DESC, seq ASC) AS ROWNUM,
		*
	FROM DBO.MY_BOARD
)
SELECT *, (SELECT COUNT(*) FROM myCTE) AS TOTALCNT
FROM myCTE WHERE ROWNUM BETWEEN (@I_PAGE -1) * @I_PAGESIZE) + 1 AND @I_PAGE * @I_PAGESIZE
```

위와 같이 쿼리를 수정한 결과 실행시간이 3초에서 1초로 줄었다.
