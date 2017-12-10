<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
54.Hibernate.
  (1) 资源管理: 提供了对 Hibernate 和 JDO 的支持，包括 HibernateTemplate / JdoTemplate 类似于 JdbcTemplate，
HibernateInterceptor / JdoInterceptor 以及一个 Hibernate / JDO 事务管理器。

  (2) 在 Spring 容器中创建 SessionFactory:
  <beans>
    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
       <property name="driverClassName" value="org.hsqldb.jdbcDriver"/>
       <property name="url" value="jdbc:hsqldb:hsql://localhost:9001"/>
       <property name="username" value="sa"/>
       <property name="password" value=""/>
    </bean>
    <bean id="mySessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
       <property name="dataSource" ref="myDataSource"/>
       <property name="mappingResources">
          <list>
             <value>product.hbm.xml</value>
          </list>
       </property>
       <property name="hibernateProperties">
          <value>hibernate.dialect=org.hibernate.dialect.HSQLDialect</value>
       </property>
    </bean>
  </beans>

  (3) HibernateTemplate:
  public class ProductDaoImpl implements ProductDao {
    private HibernateTemplate hibernateTemplate;
    public void setSessionFactory(SessionFactory sessionFactory) {
        this.hibernateTemplate = new HibernateTemplate(sessionFactory);
    }
    public Collection loadProductsByCategory(final String category) throws DataAccessException {
        return this.hibernateTemplate.execute(new HibernateCallback() {
            public Object doInHibernate(Session session) {
                Criteria criteria = session.createCriteria(Product.class);
                criteria.add(Expression.eq("category", category));
                criteria.setMaxResults(6);
                return criteria.list();
            }
        };
    }
  }

  (4) 不使用回调的基于 Spring 的 DAO 实现:
   public class HibernateProductDao extends HibernateDaoSupport implements ProductDao {
     public Collection loadProductsByCategory(String category) throws DataAccessException, MyException {
        Session session = getSession(false);
        try {
            Query query = session.createQuery("from test.Product product where product.category=?");
            query.setString(0, category);
            List result = query.list();
            if (result == null) {
                throw new MyException("No search results.");
            }
            return result;
        }
        catch (HibernateException ex) {
            throw convertHibernateAccessException(ex);
        }
    }
  }

  (5) 基于 Hibernate3 的原生 API 实现 DAO:
   public class ProductDaoImpl implements ProductDao {
      private SessionFactory sessionFactory;
      public void setSessionFactory(SessionFactory sessionFactory) {
         this.sessionFactory = sessionFactory;
      }
      public Collection loadProductsByCategory(String category) {
         return this.sessionFactory.getCurrentSession()
               .createQuery("from test.Product product where product.category=?")
               .setParameter(0, category).list();
      }
   }

  (6) 编程式的事务划分:
   <beans>
      <bean id="myTxManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
         <property name="sessionFactory" ref="mySessionFactory"/>
      </bean>
      <bean id="myProductService" class="product.ProductServiceImpl">
         <property name="transactionManager" ref="myTxManager"/>
         <property name="productDao" ref="myProductDao"/>
      </bean>
   </beans>

  (7) 声明式的事务划分: 支持通过配置 Spring 容器中的 AOP Transaction Interceptor 来替换事务划分的硬编码。
   <beans>
     <bean id="myTxManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
       <property name="sessionFactory" ref="mySessionFactory"/>
     </bean>
     <bean id="myProductService" class="org.springframework.aop.framework.ProxyFactoryBean">
       <property name="proxyInterfaces" value="product.ProductService"/>
       <property name="target">
          <bean class="product.DefaultProductService">
             <property name="productDao" ref="myProductDao"/>
          </bean>
       </property>
       <property name="interceptorNames">
          <list>
            <value>myTxInterceptor</value> <!-- the transaction interceptor (configured elsewhere) -->
          </list>
       </property>
     </bean>
   </beans>

  (8) 事务管理策略: 
  TransactionTemplate 和 TransactionInterceptor 都将真正的事务处理委托给一个
PlatformTransactionManager 实例来处理。
  对于横跨多个 Hibernate SessionFacotry 的分布式事务，只需简单地将 JtaTransactionManager 同多个
LocalSessionFactoryBean 的定义结合起来作为事务策略。

55.JDO。
  (1) 建立 PersistenceManagerFactory。
  <beans>
     <bean id="myPmf" class="org.springframework.orm.jdo.LocalPersistenceManagerFactoryBean">
        <property name="configLocation" value="classpath:kodo.properties"/>
     </bean>
  </beans>

  (2) JdoTemplate 和 JdoDaoSupport。
  每一个基于 JDO 的 DAO 类都需要通过 IoC 来注入一个 PersistenceManagerFactory。
  public class ProductDaoImpl implements ProductDao {
     private JdoTemplate jdoTemplate;
     public void setPersistenceManagerFactory(PersistenceManagerFactory pmf) {
        this.jdoTemplate = new JdoTemplate(pmf);
     }
     public Collection loadProductsByCategory(final String category) throws DataAccessException {
        return (Collection) this.jdoTemplate.execute(new JdoCallback() {
            public Object doInJdo(PersistenceManager pm) throws JDOException {
                Query query = pm.newQuery(Product.class, "category = pCategory");
                query.declareParameters("String pCategory");
                List result = query.execute(category);
                // do some further stuff with the result list
                return result;
            }
        });
     }
   }

  (3) 基于原生的 JDO API 实现 DAO。
  public class ProductDaoImpl implements ProductDao {
     private PersistenceManagerFactory persistenceManagerFactory;
     public void setPersistenceManagerFactory(PersistenceManagerFactory pmf) {
        this.persistenceManagerFactory = pmf;
     }
     public Collection loadProductsByCategory(String category) {
        PersistenceManager pm = this.persistenceManagerFactory.getPersistenceManager();
        try {
           Query query = pm.newQuery(Product.class, "category = pCategory");
           query.declareParameters("String pCategory"); 
           return query.execute(category);
        } finally {
           pm.close();
        }
     }
  }

  (4) 事务管理。
   <bean id="myTxManager" class="org.springframework.orm.jdo.JdoTransactionManager">
      <property name="persistenceManagerFactory" ref="myPmf"/>
   </bean>
   <bean id="myProductService" class="product.ProductServiceImpl">
      <property name="productDao" ref="myProductDao"/>
   </bean>
   <tx:advice id="txAdvice" transaction-manager="txManager">
      <tx:attributes>
         <tx:method name="increasePrice*" propagation="REQUIRED"/>
         <tx:method name="someOtherBusinessMethod" propagation="REQUIRES_NEW"/>
         <tx:method name="*" propagation="SUPPORTS" read-only="true"/>
      </tx:attributes>
   </tx:advice>
   <aop:config>
      <aop:pointcut id="productServiceMethods" expression="execution(* product.ProductService.*(..))"/>
      <aop:advisor advice-ref="txAdvice" pointcut-ref="productServiceMethods"/>
   </aop:config>

  (5) JdoDialect。
  JdoTemplate 和 interfacename 都支持一个用户自定义的 JdoDialect 作为 “jdoDialect” 的 bean 属性进行注入。
```
