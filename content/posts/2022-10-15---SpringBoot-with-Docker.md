---
title: "Spring boot with docker"
date: "2022-10-15T20:20:10.284Z"
template: "post"
draft: false
slug: "springboot-docker"
category: "Development"
tags:
  - "SpringData"
  - "Cassandra"
  - "Java"
description: "building a Docker image for running a Spring Boot application"
socialImage: ""
---

## AWS keyspace 에 데이터 베이스 생성하기

```sql
use kspc_test;

create table kspc_test.event (
  type text,
  target_idx bigint,
  target_type tinyint,
  source text,
  send_time timestamp,
  ext_data text,
  primary key (target_idx, send_time)
) WITH CLUSTERING ORDER BY (send_time DESC);
```

- primary key 에 등록한 키값으로 유니크 값을 만들고 이 값에 따라 카산드라 디비에 저장되고 복제됨
- Clustering 에 미리 order by를 지정해 주지않으면 select 쿼리 문에서 사용할 수 없음

## Insert Row

### CassandraRepository

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories.definition-tuning](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories.definition-tuning)

```java
package org.springframework.data.cassandra.repository;

@NoRepositoryBean
public interface CassandraRepository<T, ID> extends CrudRepository<T, ID> {
	<S extends T> S insert(S entity);
	<S extends T> List<S> insert(Iterable<S> entities);
}
```

```java
interface UserRepository extends CassandraRepository<User, Long> {
  User save(User user);
}
```

### CassandraOperaions / CassandraTemplate

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.template.insert-update](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.template.insert-update)

```java
Person bob = new Person("Bob", 33);
cassandraTemplate.insert(bob);
```

## Select Row

### CassandraRepository

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories)

- **CrudRepository**를 확장해서 사용하는 방법 (JPA를 사용하는 방법과 유사하다)
    
    ```java
    public interface CrudRepository<T, ID> extends Repository<T, ID> {
    
      <S extends T> S save(S entity);        // Saves the given entity.
    
      Optional<T> findById(ID primaryKey);   // Returns the entity identified by the given ID.
    
      Iterable<T> findAll();                 // Returns all entities.
    
      long count();                        
    
      void delete(T entity);               
    
      boolean existsById(ID primaryKey);   
    
      // … more functionality omitted.
    }
    ```
    
- Cassandra repository supports Spring Data Repository (less boilerplate code to implement data access layers)
[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#repositories)
- CassandraPageRequest.of(page, limit, sort)
 because classic paging patterns using limit/offset are not applicable to Cassandra.
- keywords for query methods
[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories.queries](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories.queries)

```java
@Repository
public interface MsgCassandraRepository extends CassandraRepository<ChatMsg, MsgKey> {

    Slice<ChatMsg> findByDestTypeAndDestAndMsgIdxIsLessThanEqual(byte destType, String dest, long startMsgIdx, CassandraPageRequest msg_idx);
}
```

### CqlTemplate

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.cql-template](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.cql-template)

foundational building block for **CassandraTemplate**

```java
int countOfActorsNamedJoe = cqlTemplate.queryForObject(
    "SELECT COUNT(*) FROM t_actor WHERE first_name = ?", Integer.class, "Joe");
```

### CassandraOperaions / CassandraTemplate

- Once configured, `CassandraTemplate` is thread-safe and can be reused across multiple instances.
- CassandraOperations를 가지고 cql 또는 query object 대신 도메인 오브젝트를 전달

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.template.query](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.template.query)

9.10. Querying Rows 챕터 확인

## Reference

[https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/)