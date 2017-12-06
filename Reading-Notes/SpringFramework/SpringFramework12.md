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
```
