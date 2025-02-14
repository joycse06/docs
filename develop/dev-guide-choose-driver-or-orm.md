---
title: Choose Driver or ORM
summary: Learn how to choose a driver or ORM framework to connect to TiDB.
---

# Choose Driver or ORM

TiDB is highly compatible with the MySQL protocol but some features are incompatible with MySQL.

For example:

- Features that are not supported by TiDB:

    - Stored procedures and functions
    - Triggers
    - `FOREIGN KEY` constraints

- Features that are different from MySQL:

    - Auto-increment ID: auto-incremental columns are globally unique in TiDB. They are incremental on a single TiDB server, but ***not*** necessarily incremental among multiple TiDB servers or allocated sequentially.

For a full list of compatibility differences, see [MySQL Compatibility](/mysql-compatibility.md).

## Java

TiDB provides the following two support levels for Java:

- **Full**: indicates that using this driver or ORM does not have any known issues.
- **Verified**: indicates that using this driver or ORM might get errors because of compatibility differences between TiDB and MySQL.

### Java Drivers

**JDBC**

Support level: **Full**

You can follow the [MySQL documentation](https://dev.mysql.com/doc/connector-j/5.1/en/) to download and configure a Java JDBC driver.

> **Note:**
>
> It is strongly recommended to use version 5.1.49, which is the latest version of JDBC 5.1. Since there is an [unresolved bug](https://bugs.mysql.com/bug.php?id=106252) in the current version 8.0.29, which might cause threads to hang when using TiDB. It is recommended that you do not upgrade to version 8.0 until MySQL JDBC 8.0 merges this fix.

For an example of how to build a complete application, see [Build a Simple CRUD App with TiDB and JDBC](/develop/dev-guide-sample-application-java.md).

### Java ORM framework

#### Hibernate

Support level: `Full`

> **Note:**
>
> Currently, Hibernate does [not support nested transactions](https://stackoverflow.com/questions/37927208/nested-transaction-in-spring-app-with-jpa-postgres). Since v6.2.0, TiDB supports [savepoint](/sql-statements/sql-statement-savepoint.md).
>
> If you are using a framework such as `Spring Data JPA`, do not use the `Propagation.NESTED` transaction propagation option in `@Transactional`, that is, do not set `@Transactional( propagation = Propagation.NESTED)`.

To avoid manually managing complex relationships between different dependencies of an application, you can use [Gradle](https://gradle.org/install) or [Maven](https://maven.apache.org/install.html) to get all dependencies of your application, including those indirect ones. Note that only Hibernate `6.0.0.Beta2` or above supports the TiDB dialect.

If you are using **Maven**, add the following to your `<dependencies></dependencies>`:

{{< copyable "" >}}

```xml
<dependency>
    <groupId>org.hibernate.orm</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>6.0.0.CR2</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>
```

If you are using **Gradle**, add the following to your `dependencies`:

{{< copyable "" >}}

```gradle
implementation 'org.hibernate:hibernate-core:6.0.0.CR2'
implementation 'mysql:mysql-connector-java:5.1.49'
```

- For an example of using Hibernate to build a TiDB application by native Java, see [Build a Simple CRUD App with TiDB and Java](/develop/dev-guide-sample-application-java.md).
- For an example of using Spring Data JPA or Hibernate to build a TiDB application by Spring, see [Build a TiDB Application using Spring Boot](/develop/dev-guide-sample-application-spring-boot.md).

In addition, you need to specify the TiDB dialect in your [Hibernate configuration file](https://www.tutorialspoint.com/hibernate/hibernate_configuration.htm): `org.hibernate.dialect.TiDBDialect`, which is only supported by Hibernate `6.0.0.Beta2` or above. If your `Hibernate` version is earlier than `6.0.0.Beta2`, upgrade it first.

> **Note:**
>
> If you are unable to upgrade your `Hibernate` version, use the MySQL 5.7 dialect `org.hibernate.dialect.MySQL57Dialect` instead. However, this setting might cause unpredictable results and the absence of some TiDB-specific features, such as [sequences](/sql-statements/sql-statement-create-sequence.md).
