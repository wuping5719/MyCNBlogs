<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
43.关键抽象。
   (1) Spring 事务抽象的关键是事务策略的概念。
   这个概念由 org.springframework.transaction.PlatformTransactionManager 接口定义如下：
   public interface PlatformTransactionManager {
      TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException;
      void commit(TransactionStatus status) throws TransactionException;
      void rollback(TransactionStatus status) throws TransactionException;
   }

   public interface TransactionStatus {
      boolean isNewTransaction();
      void setRollbackOnly();
      boolean isRollbackOnly();
   }

   <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
      <property name="driverClassName" value="${jdbc.driverClassName}" />
      <property name="url" value="${jdbc.url}" />
      <property name="username" value="${jdbc.username}" />
      <property name="password" value="${jdbc.password}" />
   </bean>

   <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
   </bean>

   (2) HibernateTransactionManager 需要一个指向 SessionFactory 的引用。
   <bean id="sessionFactory" class="org.springframework.orm.hibernate.LocalSessionFactoryBean">
      <property name="dataSource" ref="dataSource" />
      <property name="mappingResources">
         <list>
            <value>org/springframework/samples/petclinic/hibernate/petclinic.hbm.xml</value>
         </list>
      </property>
      <property name="hibernateProperties">
         <value>hibernate.dialect=${hibernate.dialect}</value>
      </property>
   </bean>

   <bean id="txManager" class="org.springframework.orm.hibernate.HibernateTransactionManager">
      <property name="sessionFactory" ref="sessionFactory" />
   </bean>

   <bean id="txManager" class="org.springframework.transaction.jta.JtaTransactionManager"/>
   
44.使用资源同步的事务。
   在 JDBC 环境下，不再使用传统的调用 DataSource 的 getConnection() 方法的方式，
而是使用 Spring 的 org.springframework.jdbc.datasource.DataSourceUtils。
   Connection conn = DataSourceUtils.getConnection(dataSource);
```
