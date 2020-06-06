# MongoDB Transaction Benchmark with YCSB

MongoDB랑 Postgre NoSQL을 YCSB를 이용해서 벤치마크.

## DB-binding 파일 생성

Maven 3 버전을 사용해야 함.

전체 YCSB를 빌드하려면:

`./YCSB` 폴더에서

```shell
mvn clean package
```

하나의 Database binding을 생성하려면:

```shell
mvn -pl site.ycsb:mongodb-binding -am clean package
mvn -pl site.ycsb:postgrenosql-binding -am clean package
```

자세한 실행 방법은 [Core Properties](https://github.com/brianfrankcooper/YCSB/wiki/Core-Properties) 참고.

## 테스트 방법

### MongoDB

Workload A 실행 예시.

```shell
# load the data
./bin/ycsb load mongodb -s -P workloads/workloada > results/mongodb-outputLoad.txt
# run the workload
./bin/ycsb run mongodb -s -P workloads/workloada > results/mongodb-outputRun.txt
```

테스트가 끝나면 해당 collection의 모든 documents 삭제

```shell
mongo ycsb
db.usertable.remove({});
```

자세한 옵션은 [MongoDB.md](https://github.com/hyunjinjeong/mongodb-transaction-benchmark/blob/master/MongoDB.md) 파일 참조.

### PostgreSQL

1. 초기 설정

```postgresql
CREATE DATABASE test;
CREATE TABLE usertable (YCSB_KEY VARCHAR(255) PRIMARY KEY, YCSB_VALUE JSONB not NULL);
GRANT ALL PRIVILEGES ON DATABASE test to postgres;
```

2. `./postgrenosql.properties` 파일 설정

아래는 기본 옵션.

```config
postgrenosql.url = jdbc:postgresql://localhost:5432/test
postgrenosql.user = postgres
postgrenosql.passwd = postgres
postgrenosql.autocommit = true
```

3. 아래 명령어 실행.

Workload A 실행 예시.

```shell
# load the data
.\bin\ycsb load postgrenosql -s -P .\workloads\workloada -P .\postgrenosql.properties > results/postgres-outputLoad.txt
# run the workload
.\bin\ycsb run postgrenosql -s -P .\workloads\workloada -P .\postgrenosql.properties > results/postgres-outputRun.txt
```

테스트가 끝나면 해당 relation의 모든 records 삭제

```shell
psql -U postgres test
delete from usertable;
```

자세한 옵션은 [PostgreSQL.md](https://github.com/hyunjinjeong/mongodb-transaction-benchmark/blob/master/PostgreSQL.md) 파일 참조.

## DB Versions

- MongoDB 4.2.7

- PostgreSQL 12.3
