---
title: 커버링 인덱스(mysql)
author: 한주희
layout: post
categories: db
permalink: mysql-convering.html
---

 최근에 커버링 인덱스에 대해서 알게 되어 정리 한다.  
 (참조 : https://lalwr.blogspot.com/2016/02/db-index.html, https://gywn.net/2012/04/mysql-covering-index/)

## 인덱스란?
 1) 인덱스의 의미  
* RDBMS에서 검색 속도를 높이기 위해 사용하는 하나의 기술이다.  
* INDEX는 색인으로, 해당 TABLE의 컬럼을 색인(따로 저장)하여 검색시 해당 TABLE의 레코드를 full scan 하는게 아니라, 색인화 되어 있는 INDEX파일을 검색하여 검색속도를 빠르게 한다.  
  * ```풀스캔(full scan)이란?``` - 말 그대로, DB 테이블에 포함된 데이터를 처음부터 끝가지(열과 행 모두!) 체크하며 원하는 데이터를 추출 하는 것을 의미한다.  
* 이런 인덱스는 TREE 구조로 색인화 하여, RDBMS 에서 사용하는 인덱스는 Balanace Search Tree(B-Tree)를 사용한다.  


 2) 인덱스의 원리
* 인덱스를 해당 컬럼에 주게 되면 초기 TABLE 생성시 만들어진 MYD, MYI, FRM 3개의 파일 중에서 MYI에 해당 컬럼을 색인화 하여 저장한다.
* 물론 인덱스를 사용안할 시에는 MYI파일은 비어 있다. 그래서 INDEX를 해당 컬럼에 만들게 되면 해당 컬럼을 따로 인덱싱하여 MYI파일에 입력한다.
* 그래서 사용자가 SELECT쿼리로 INDEX가 사용하는 쿼리를 사용시 해당 TABLE을 검색하는 것이 아니라 빠른 Tree로 정리해둔 MYI파일의 내용을 검색한다.

## 커버링 인덱스란?  
* 커버링 인덱스란 원하는 데이터를 인덱스에서만 추출할 수 있는 인덱스를 의미한다.
* B-Tree 스캔만으로 원하는 데이터를 가져올 수 있으며, 컬럼을 읽기 위해 굳이 데이터 블록을 보지 않아도 된다.
* 인덱스는 행 전체 크기보다 훨씬 작으며, 인덱스 값에 따라 정렬이 되기 때문에 Sequential Read 접근할 수 있기 때문에, 커버링 인덱스를 사용하면 결과적으로 쿼리 성능을 비약적으로 올릴 수 있다. 