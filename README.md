Discontinued
============
This repository is no longer actively maintained. Please take a look at the [original repo](https://github.com/brettwooldridge/HikariCP).


![](https://raw.github.com/wiki/brettwooldridge/HikariCP/Hikari.png)&nbsp;HikariCP <sup><sup>It's Faster.</sup></sup>&nbsp;[![Build Status](https://travis-ci.org/brettwooldridge/HikariCP.png?branch=master)](https://travis-ci.org/brettwooldridge/HikariCP)<img src='https://raw.github.com/wiki/brettwooldridge/HikariCP/space200x1.gif'><sup><sup>[![](https://raw.github.com/wiki/brettwooldridge/HikariCP/twitter.png)](https://twitter.com/share?text=Interesting%20JDBC%20Connection%20Pool&hashtags=HikariCP&url=https%3A%2F%2Fgithub.com%2Fbrettwooldridge%2FHikariCP)&nbsp;[![](https://raw.github.com/wiki/brettwooldridge/HikariCP/facebook.png)](http://www.facebook.com/plugins/like.php?href=https%3A%2F%2Fgithub.com%2Fbrettwooldridge%2FHikariCP&width&layout=standard&action=recommend&show_faces=true&share=false&height=80)</sup></sup>
==========
##### Hi·ka·ri [hi·ka·'lē] &#40;*Origin: Japanese*): light; ray.

There is nothing faster.  There is nothing more correct.  HikariCP is a "zero-overhead" production-quality connection pool.  Coming in at roughly 50Kb, the library is extremely light.  Read about [how we do it here](https://github.com/brettwooldridge/HikariCP/wiki/Down-the-Rabbit-Hole).

***Maven Repository***<br/>
```xml
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>1.3.3</version>
        <scope>compile</scope>
    </dependency>
```
----------------------------------------------------
***Thank you!***<br/>
We just wanted to say "Thank you!" for all of your support. As you can see, you have all helped HikariCP off to a great start over the past few months. The momentum is here. The most valuable thing you can do to help HikariCP is to <sub><sub> [![](https://raw.github.com/wiki/brettwooldridge/HikariCP/twitter.png)](https://twitter.com/share?text=Interesting%20JDBC%20Connection%20Pool&hashtags=HikariCP&url=https%3A%2F%2Fgithub.com%2Fbrettwooldridge%2FHikariCP)</sub></sub> about it.

![](https://github.com/brettwooldridge/HikariCP/wiki/Downloads.png)

----------------------------------------------------
***JMH Benchmarks***<br/>

Using the excellent [JMH microbenchmark framework](http://openjdk.java.net/projects/code-tools/jmh/) developed by the Oracle JVM performance team, extremely accurate microbenchmarks were created to isolate and measure the overhead of HikariCP and other popular pools.  You can checkout the [HikariCP benchmark project for details](https://github.com/brettwooldridge/HikariCP-benchmark) and review/run the benchmarks yourself.

![](https://github.com/brettwooldridge/HikariCP/wiki/Benchmarks.png)

 * One *Connection Cycle* is defined as single ``DataSource.getConnection()``/``Connection.close()``.
   * In *Unconstrained* benchmark, connections > threads.
   * In *Constrained* benchmark, threads > connections (2:1).
 * One *Statement Cycle* is defined as single ``Connection.prepareStatement()``, ``Statement.execute()``, ``Statement.close()``.

<sup>1</sup> BoneCP fails to complete *Statement Cycle* benchmark unless the "CACHED" pool strategy is used.<br/>
<sup>2</sup> Tomcat fails to complete *Statement Cycle* benchmark when "StatementFinalizer" feature is enabled.<br/>
<sup>3</sup> Benchmark results run against 1.3.3<br/>

#### What's wrong with other pools?

Not all pools are created equal.  Read our [pool analysis](https://github.com/brettwooldridge/HikariCP/wiki/Pool-Analysis) here if you care to know the good, bad, and ugly.

------------------------------

#### Configuration (knobs, baby!)
The following are the various properties that can be configured in the pool, their behavior,
and their defaults.  Rather than coming out of the box with almost nothing configured, HikariCP
comes with *sane* defaults that let a great many deployments run without any additional tweaking
(except for the DataSource and DataSource properties).

<sup>:paperclip:</sup>&nbsp;*HikariCP uses milliseconds for all time values.*

:hash:``acquireIncrement``<br/>
This property controls the maximum number of connections that are acquired at one time, with
the exception of pool initialization. *Default: 1*

:hash:``acquireRetries``<br/>
This is a per-connection attempt retry count used during new connection creation (acquisition).
If a connection creation attempt fails there will be a wait of ``acquireRetryDelay``
milliseconds followed by another attempt, up to the number of retries configured by this
property. *Default: 3*

:watch:``acquireRetryDelay``<br/>
This property controls the number of milliseconds to delay between attempts to acquire a
connection to the database.  If ``acquireRetries`` is 0, this property has no effect.
*Default: 750*

:white_check_mark:``autoCommit``<br/>
This property controls the default auto-commit behavior of connections returned from the pool.
It is a boolean value.  *Default: true*

:abc:``catalog``<br/>
This property sets the default *catalog* for databases that support the concept of catalogs.
If this property is not specified, the default catalog defined by the JDBC driver is used.
*Default: none*

:abc:``connectionInitSql``<br/>
This property sets a SQL statement that will be executed after every new connection creation
before adding it to the pool. If this SQL is not valid or throws an exception, it will be
treated as a connection failure and the standard retry logic will be followed.  *Default: none*

:abc:``connectionTestQuery``<br/>
This is for "legacy" databases that do not support the JDBC4 Connection.isValid() API.  This
is the query that will be executed just before a connection is given to you from the pool to
validate that the connection to the database is still alive.  It is database dependent and
should be a query that takes very little processing by the database (eg. "VALUES 1").  **See
the ``jdbc4ConnectionTest`` property for a more efficent alive test.**  One of either this
property or ``jdbc4ConnectionTest`` must be specified.  *Default: none*

:watch:``connectionTimeout``<br/>
This property controls the maximum number of milliseconds that a client (that's you) will wait
for a connection from the pool.  If this time is exceeded without a connection becoming
available, a SQLException will be thrown.  *Default: 5000*

:arrow_right:``dataSource``<br/>
This property is only available via programmatic configuration.  This property allows you
to directly set the instance of the ``DataSource`` to be wrapped by the pool, rather than
having HikariCP construct it via reflection.  When this property is specified, the
``dataSourceClassName`` property and all DataSource-specific properties will be ignored.
*Default: none*

:abc:``dataSourceClassName``<br/>
This is the name of the ``DataSource`` class provided by the JDBC driver.  Consult the
documentation for your specific JDBC driver to get this class name.  Note XA data sources
are not supported.  XA requires a real transaction manager like [bitronix](https://github.com/bitronix/btm).
*Default: none*

:watch:``idleTimeout``<br/>
This property controls the maximum amount of time (in milliseconds) that a connection is
allowed to sit idle in the pool.  Whether a connection is retired as idle or not is subject
to a maximum variation of +30 seconds, and average variation of +15 seconds.  A connection
will never be retired as idle *before* this timeout.  A value of 0 means that idle connections
are never removed from the pool.  *Default: 600000 (10 minutes)*

:negative_squared_cross_mark:``initializationFailFast``<br/>
This property controls whether the pool will "fail fast" if the pool cannot be seeded with
initial connections successfully.  If connections cannot be created at pool startup time,
a ``RuntimeException`` will be thrown from the ``HikariDataSource`` constructor.  This
property has no effect if ``minimumPoolSize`` is 0.  *Default: false*

:white_check_mark:``jdbc4ConnectionTest``<br/>
This property is a boolean value that determines whether the JDBC4 Connection.isValid() method
is used to check that a connection is still alive.  This value is mutually exclusive with the
``connectionTestQuery`` property, and this method of testing connection validity should be
preferred if supported by the JDBC driver.  *Default: true*

:watch:``leakDetectionThreshold``<br/>
This property controls the amount of time that a connection can be out of the pool before a
message is logged indicating a possible connection leak.  A value of 0 means leak detection
is disabled.  While the default is 0, and other connection pool implementations state that
leak detection is "not for production" as it imposes a high overhead, at least in the case
of HikariCP the imposed overhead is only 5μs (*microseconds*) split between getConnection()
and close().  Maybe other pools are doing it wrong, but feel free to use leak detection under
HikariCP in production environments if you wish.  *Default: 0*

:watch:``maxLifetime``<br/>
This property controls the maximum lifetime of a connection in the pool.  When a connection
reaches this timeout, even if recently used, it will be retired from the pool.  An in-use
connection will never be retired, only when it is idle will it be removed.  We strongly
recommend setting this value, and using something reasonable like 30 minutes or 1 hour.  A
value of 0 indicates no maximum lifetime (infinite lifetime), subject of course to the
``idleTimeout`` setting.  *Default: 1800000 (30 minutes)*

:hash:``maximumPoolSize``<br/>
This property controls the maximum size that the pool is allowed to reach, including both
idle and in-use connections.  Basically this value will determine the maximum number of
actual connections to the database backend.  A reasonable value for this is best determined
by your execution environment.  When the pool reaches this size, and no idle connections are
available, calls to getConnection() will block for up to ``connectionTimeout`` milliseconds
before timing out.  *Default: 60*

:hash:``minimumPoolSize``<br/>
This property controls the minimum number of connections that HikariCP tries to maintain in
the pool, including both idle and in-use connections.  If the connections dip below this
value, HikariCP will make a best effort to restore them quickly and efficiently.  A reasonable
value for this is best determined by your execution environment.  *Default: 10*

:abc:``poolName``<br/>
This property represents a user-defined name for the connection pool and appears mainly
in a JMX management console to identify pools and pool configurations.  *Default: auto-generated*

:negative_squared_cross_mark:``registerMbeans``<br/>
This property controls whether or not JMX Management Beans ("MBeans") are registered or not.
*Default: false*

:abc:``transactionIsolation``<br/>
This property controls the default transaction isolation level of connections returned from
the pool.  If this property is not specified, the default transaction isolation level defined
by the JDBC driver is used.  Typically, the JDBC driver default transaction isolation level
should be used.  Only use this property if you have specific isolation requirements that are
common for all queries, otherwise simply set the isolation level manually when creating or
preparing statements.  The value of this property is the constant name from the ``Connection``
class such as ``TRANSACTION_READ_COMMITTED``, ``TRANSACTION_REPEATABLE_READ``, etc.  *Default: none*

***Missing Knobs***<br/>
HikariCP has plenty of "knobs" to turn as you can see above, but comparatively less than some other pools.
This is a design philosophy.  The HikariCP design asthetic is Minimalism.  In keeping with the
*simple is better* or *less is more* design philosophy, some knobs and  features are intentionally left out.
Here are two, and the rationale.

**Statement Cache**<br/>
Most major database JDBC drivers already have a Statement cache that can be configured (Oracle, 
MySQL, PostgreSQL, Derby, etc).  A statement cache in the pool would add unneeded weight and no
additional functionality.  It is simply unnecessary with modern database drivers to implement a
cache at the pool level.

**Log Statement Text / Slow Query Logging**<br/>
Like Statement caching, most major database vendors support statement logging through
properties of their own driver.  This includes Oracle, MySQL, Derby, MSSQL, and others.  Some
even support slow query logging. We consider this a "development-time" feature.  For those few
databases that do not support it, [jdbcdslog-exp](https://code.google.com/p/jdbcdslog-exp/) is
a good option.  Great stuff during development and pre-Production.

----------------------------------------------------

### Initialization

You can use the HikariConfig class like so:
```java
HikariConfig config = new HikariConfig();
config.setMaximumPoolSize(100);
config.setDataSourceClassName("com.mysql.jdbc.jdbc2.optional.MysqlDataSource");
config.addDataSourceProperty("url", "jdbc:mysql://localhost/database");
config.addDataSourceProperty("user", "bart");
config.addDataSourceProperty("password", "51mp50n");

HikariDataSource ds = new HikariDataSource(config);
```
or property file based:
```java
HikariConfig config = new HikariConfig("some/path/hikari.properties");
HikariDataSource ds = new HikariDataSource(config);
```
Example property file:
```ini
acquireIncrement=3
acquireRetryDelay=1000
connectionTestQuery=SELECT 1
dataSourceClassName=org.postgresql.ds.PGSimpleDataSource
dataSource.user=test
dataSource.password=test
dataSource.databaseName=mydb
dataSource.serverName=localhost
```
or ``java.util.Properties`` based:
```java
Properties props = new Properties();
props.setProperty("maximumPoolSize", 100);
props.setProperty("dataSourceClassName", "org.postgresql.ds.PGSimpleDataSource");
props.setProperty("dataSource.user", "test");
props.setProperty("dataSource.password", "test");
props.setProperty("dataSource.databaseName", "mydb");
props.setProperty("dataSource.logWriter", new PrintWriter(System.out));

HikariConfig config = new HikariConfig(props);
HikariDataSource ds = new HikariDataSource(config);
```

#### HikariConfig vs. HikariDataSource
Finally, you can skip the HikariConfig class altogether and configure the ``HikariDataSource`` directly:
```java
HikariDataSource ds = new HikariDataSource();
ds.setMaximumPoolSize(100);
ds.setDataSourceClassName("com.mysql.jdbc.jdbc2.optional.MysqlDataSource");
ds.addDataSourceProperty("url", "jdbc:mysql://localhost/database");
ds.addDataSourceProperty("user", "bart");
ds.addDataSourceProperty("password", "51mp50n");
```
The advantage of configuring via ``HikariConfig`` over ``HikariDataSource`` is that when using the ``HikariConfig`` we know at ``DataSource`` construction-time what the configuration is, so the pool can be initialized at that point.  However, when using ``HikariDataSource`` alone, we don't know that you are *done* configuring the DataSource until ``getConnection()`` is called.  In that case, ``getConnection()`` must perform an additional check to see if the pool as been initialized yet or not.  The cost (albeit small) of this check is incurred on every invocation of ``getConnection()`` in that case.  In summary, intialization by ``HikariConfig`` is every so slightly more performant than initialization directly on the ``HikariDataSource`` -- not just at construction time but also at runtime.

### Play Framework Plugin

Github user [autma](https://github.com/autma) has created a [plugin](https://github.com/autma/play-hikaricp-plugin) for the Play framework.  Thanks!

----------------------------------------------------

### Support <sup><sup>:speech_balloon:</sup></sup>
Google discussion group [HikariCP here](https://groups.google.com/d/forum/hikari-cp), growing [FAQ](https://github.com/brettwooldridge/HikariCP/wiki/FAQ).

[![](https://raw.github.com/wiki/brettwooldridge/HikariCP/twitter.png)](https://twitter.com/share?text=Interesting%20JDBC%20Connection%20Pool&hashtags=HikariCP&url=https%3A%2F%2Fgithub.com%2Fbrettwooldridge%2FHikariCP)&nbsp;[![](https://raw.github.com/wiki/brettwooldridge/HikariCP/facebook.png)](http://www.facebook.com/plugins/like.php?href=https%3A%2F%2Fgithub.com%2Fbrettwooldridge%2FHikariCP&width&layout=standard&action=recommend&show_faces=true&share=false&height=80)

### Wiki
Don't forget the [Wiki](https://github.com/brettwooldridge/HikariCP/wiki) for additional information such as:
 * [FAQ](https://github.com/brettwooldridge/HikariCP/wiki/FAQ)
 * [Hibernate 4.x Configuration](https://github.com/brettwooldridge/HikariCP/wiki/Hibernate4)
 * [MySQL Configuration Tips](https://github.com/brettwooldridge/HikariCP/wiki/MySQL-Configuration)
 * etc.

----------------------------------------------------

### Requirements
 &#8658; Java 6 and above<br/>
 &#8658; Javassist 3.18.1+ library<br/>
 &#8658; slf4j library<br/>

### Contributions
Please perform changes and submit pull requests from the ``dev`` branch instead of ``master``.  Please set your editor to use spaces instead of tabs, and adhere to the apparent style of the code you are editing.  The ``dev`` branch is always more "current" than the ``master`` if you are looking to live life on the edge.

[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/63472d76ad0d494e3c4d8fc4a13ea4ce "githalytics.com")](http://githalytics.com/brettwooldridge/HikariCP)
