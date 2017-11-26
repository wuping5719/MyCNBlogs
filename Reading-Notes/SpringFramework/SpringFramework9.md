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

45.声明式事务管理。
  (1) 回滚.
  <tx:advice id="txAdvice">
     <tx:attributes>
        <tx:method name="*" rollback-for="Throwable" no-rollback-for="InstrumentNotFoundException"/>
     </tx:attributes>
  </tx:advice>
  (2) 使用 @Transactional.
  @Transactional(readOnly = true)
  public class DefaultFooService implements FooService {
     public Foo getFoo(String fooName) {
         // do something
     }

     // these settings have precedence for this method
     @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW)
     public void updateFoo(Foo foo) {
        // do something
     }
  }

46.编程式事务管理。
   Spring 提供两种方式的编程式事务管理：
   (1) 使用 TransactionTemplate;
   public class SimpleService implements Service {
      // single TransactionTemplate shared amongst all methods in this instance
      private final TransactionTemplate transactionTemplate;

      // use constructor-injection to supply the PlatformTransactionManager
      public SimpleService(PlatformTransactionManager transactionManager) {
         Assert.notNull(transactionManager, "The 'transactionManager' argument must not be null.");
         this.transactionTemplate = new TransactionTemplate(transactionManager);
      }

      public Object someServiceMethod() {
          return transactionTemplate.execute(new TransactionCallback() {
               // the code in this method executes in a transactional context
               public Object doInTransaction(TransactionStatus status) {
                  updateOperation1();
                  return resultOfUpdateOperation2();
               }
         });
     }
   }

   (2) 直接使用一个 PlatformTransactionManager 实现.
   DefaultTransactionDefinition def = new DefaultTransactionDefinition();
   // explicitly setting the transaction name is something that can only be done programmatically
   def.setName("SomeTxName");
   def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
 
   TransactionStatus status = txManager.getTransaction(def);
   try {
      // execute your business logic here
   } catch (MyException ex) {
      txManager.rollback(status);
      throw ex;
   }
   txManager.commit(status);

47.一致的 DAO 支持抽象类。
  (1) JdbcDaoSupport - JDBC数据访问对象的基类。 需要一个DataSource，同时为子类提供 JdbcTemplate。 
  (2) HibernateDaoSupport - Hibernate 数据访问对象的基类。 需要一个 SessionFactory，
同时为子类提供 HibernateTemplate。也可以选择直接通过提供一个 HibernateTemplate 来初始化，
这样就可以重用后者的设置，例如 SessionFactory， flush模式，异常翻译器（exception translator）等等。 
  (3) JdoDaoSupport - JDO 数据访问对象的基类。 需要设置一个 PersistenceManagerFactory， 同时为子类提供 JdoTemplate。 
  (4) JpaDaoSupport - JPA 数据访问对象的基类。 需要一个 EntityManagerFactory，同时为子类提供 JpaTemplate。
```
