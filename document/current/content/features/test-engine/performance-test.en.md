+++
pre = "<b>3.6.5. </b>"
toc = true
title = "Performance Test"
weight = 5
+++

## Target

Performance test is classified as loss test and promotion test according to its verification target. Insert & update & delete which regarded as a set of associated operation and select which focus on sharding optimization are used to evaluate performance for the basic scenarios (single route, master slave & encrypt & sharding, full route). While another set of associated operation, Insert & select & delete, is used to evaluate performance for master slave.
To achieve the result better, these tests are performed based on a certain amount of data with 20 concurrent threads for 30 minutes.

## Test Scenarios

#### Single Route

On the basis of one thousand data volume, four databases that each contains 1024 tables with `id` used for database sharding and `k` used for table sharding are designed for this scenario.          
Single route select sql statement is chosen here.

#### Master Slave

One master database and one slave database is designed for this scenario based on ten thousand data volume. 

#### Master Slave & Encrypt & Sharding

On the basis of one thousand data volume, four databases that each contains 1024 tables with `id` used for database sharding, `k` used for table sharding, `c` encrypted with aes and  `pad` encrypted with md5 are designed for this scenario.
Single route select sql statement is chosen here.

#### Full Route

On the basis of one thousand data volume, four databases that each contains one table are designed for this scenario, field `id` is used for database sharding and `k` is used for table sharding.
Full route select sql statement is chosen here.

## Testing Environment

#### Table Structure of Database

The structure of table here refer to `sbtest` in `sysbench`

```shell
CREATE TABLE `tbl` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `k` int(11) NOT NULL DEFAULT 0,
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
);
```

#### Test Scenarios Configuration

The same configurations are used for Sharding-JDBC and Sharding-Proxy, while MySQL with one database connected is designed for comparision.
The details for these scenarios are shown as follows.

##### Single Route Configuration

```yaml
schemaName: sharding_db

dataSources:
  ds_0:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password: 
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_1:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password: 
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_2:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test 
    password: 
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_3:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
shardingRule:
    tables:
      tbl:
        actualDataNodes: ds_${0..3}.tbl${0..1023}
        tableStrategy:
          inline:
            shardingColumn: k
            algorithmExpression: tbl${k % 1024}
        keyGenerator:
            type: SNOWFLAKE
            column: id
    defaultDatabaseStrategy:
      inline:
        shardingColumn: id
        algorithmExpression: ds_${id % 4}
    defaultTableStrategy:
      none:
```

##### Master Slave Configuration

```yaml
schemaName: sharding_db

dataSources:
  master_ds:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  slave_ds_0:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
masterSlaveRule:
  name: ms_ds
  masterDataSourceName: master_ds
  slaveDataSourceNames:
    - slave_ds_0
```

##### Master Slave & Encrypt & Sharding Configuration

```yaml
schemaName: sharding_db

dataSources:
  master_ds_0:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  slave_ds_0:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  master_ds_1:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  slave_ds_1:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  master_ds_2:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  slave_ds_2:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  master_ds_3:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  slave_ds_3:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
shardingRule:
  tables:
    tbl:
      actualDataNodes: ms_ds_${0..3}.tbl${0..1023}
      databaseStrategy:
        inline:
          shardingColumn: id
          algorithmExpression: ms_ds_${id % 4}
      tableStrategy:
        inline:
          shardingColumn: k
          algorithmExpression: tbl${k % 1024}
      keyGenerator:
        type: SNOWFLAKE
        column: id
  bindingTables:
    - tbl
  defaultDataSourceName: master_ds_1
  defaultTableStrategy:
    none:
  masterSlaveRules:
    ms_ds_0:
      masterDataSourceName: master_ds_0
      slaveDataSourceNames:
        - slave_ds_0
      loadBalanceAlgorithmType: ROUND_ROBIN
    ms_ds_1:
      masterDataSourceName: master_ds_1
      slaveDataSourceNames:
        - slave_ds_1
      loadBalanceAlgorithmType: ROUND_ROBIN
    ms_ds_2:
      masterDataSourceName: master_ds_2
      slaveDataSourceNames:
        - slave_ds_2
      loadBalanceAlgorithmType: ROUND_ROBIN
    ms_ds_3:
      masterDataSourceName: master_ds_3
      slaveDataSourceNames:
        - slave_ds_3
      loadBalanceAlgorithmType: ROUND_ROBIN
encryptRule:
  encryptors:
    encryptor_aes:
      type: aes
      props:
        aes.key.value: 123456abc
    encryptor_md5:
      type: md5
  tables:
    sbtest:
      columns:
        c:
          plainColumn: c_plain
          cipherColumn: c_cipher
          encryptor: encryptor_aes
        pad:
          cipherColumn: pad_cipher
          encryptor: encryptor_md5    
```

##### Full Route Configuration

```yaml
schemaName: sharding_db

dataSources:
  ds_0:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_1:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_2:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
  ds_3:
    url: jdbc:mysql://***.***.***.***:****/ds?serverTimezone=UTC&useSSL=false
    username: test
    password:
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 200
shardingRule:
  tables:
    tbl:
      actualDataNodes: ds_${0..3}.tbl1
      tableStrategy:
        inline:
          shardingColumn: k
          algorithmExpression: tbl1
      keyGenerator:
          type: SNOWFLAKE
          column: id
  defaultDatabaseStrategy:
    inline:
      shardingColumn: id
      algorithmExpression: ds_${id % 4}
  defaultTableStrategy:
    none:  
```

## Test Result Verification

#### SQL Statement
 
```shell
Insert+Update+Delete sql statements:
insert into tbl(k, c, pad) values(1, '###-###-###', '###-###');
update tbl set c='####-####-####', pad='####-####' where id=?;
delete from tbl where id=?

Select sql statement for full route:
select max(id) from tbl where id%4=1

Select sql statement for single route:
select id, k from tbl ignore index(`PRIMARY`) where id=1 and k=1

Insert+Select+Delete sql statements：
insert into tbl1(k, c, pad) values(1, '###-###-###', '###-###');
select count(id) from tbl1;
select max(id) from tbl1 ignore index(`PRIMARY`);
delete from tbl1 where id=?
```

#### Jmeter Class

```shell
See: https://github.com/apache/incubator-shardingsphere-benchmark/tree/master/shardingsphere-benchmark
```

#### Compile & Build

```shell
git clone https://github.com/apache/incubator-shardingsphere-benchmark.git
cd incubator-shardingsphere-benchmark/shardingsphere-benchmark
mvn clean install
```

#### Perform Test

```shell
cp target/shardingsphere-benchmark-1.0-SNAPSHOT-jar-with-dependencies.jar apache-jmeter-4.0/lib/ext
jmeter –n –t test_plan/test.jmx
test.jmx example:https://github.com/apache/incubator-shardingsphere-benchmark/tree/master/report/script/test_plan/test.jmx
```

#### Process Result Data

Make sure the location of result.jtl file is correct.
```shell
sh shardingsphere-benchmark/report/script/gen_report.sh
```