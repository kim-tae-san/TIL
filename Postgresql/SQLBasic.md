# SQL 명령어 종류

* 데이터 정의 언어 (DDL : Data Definition Language)
* 데이터 조작 언어 (DML : Data Manipulation Language)
* 데이터 제어 언어 (DCL : Data Control Language)

## DDL
> DATABASE

    CREATE DATABASE [데이터베이스명];

    DROP DATABASE databasename;

> TABLE

    CREATE TABLE "테이블명" (
    "필드명1" "데이터 유형",
    "필드명2" "데이터 유형",
    "필드명3" "데이터 유형",
    ... );

    DROP TABLE "테이블명";

    TRUNCATE TABLE "테이블명";

> VIEW

    CREATE VIEW "VIEW_NAME" AS "SQL문";

    DROP VIEW "[데이터베이스명.]VIEW_NAME";

> INDEX

    CREATE INDEX index_name
    ON table_name (column1, column2, ...);

    DROP INDEX [ IF EXISTS ] index_name;

## DCL

    DCL (Data Control Language) 는 데이터를 제어하는 언어이다. 데이터의 보안, 무결성, 회복, 병행 수행제어 등을 정의하는데 사용한다.
    
    COMMIT : 트랜잭션의 작업 결과를 반영
    ROLLBACK : 트랜잭션의 작업을 취소 및 원래대로 복구
    GRANT : 사용자에게 권한 부여
    REVOKE : 사용자 권한 취소

> TCL

    일부에서는 DCL 에서 트랜잭션을 제어하는 명령인 COMMIT 과 ROLLBACK 만을 따로 분리해서 TCL (Transaction Control Language) 라고 표현하기도 한다.

