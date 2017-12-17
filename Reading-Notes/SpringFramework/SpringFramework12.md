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

56.Oracle TopLink。
  (1) SessionFactory 抽象层。
  <bean id="mySessionFactory" class="org.springframework.orm.toplink.LocalSessionFactoryBean">
    <property name="configLocation" value="toplink-sessions.xml"/>
    <property name="dataSource" ref="dataSource"/>
  </bean>

  <toplink-configuration>
    <session>
      <name>Session</name>
      <project-xml>toplink-mappings.xml</project-xml>
      <session-type>
         <server-session/>
      </session-type>
      <enable-logging>true</enable-logging>
      <logging-options/>
    </session>
  </toplink-configuration>

  (2) TopLinkTemplate 和 TopLinkDaoSupport。
  public class TopLinkProductDao implements ProductDao {
    private TopLinkTemplate tlTemplate;
    public void setSessionFactory(SessionFactory sessionFactory) {
        this.tlTemplate = new TopLinkTemplate(sessionFactory);
    }
    public Collection loadProductsByCategory(final String category) throws DataAccessException {
        return (Collection) this.tlTemplate.execute(new TopLinkCallback() {
            public Object doInTopLink(Session session) throws TopLinkException {
                ReadAllQuery findOwnersQuery = new ReadAllQuery(Product.class);
                findOwnersQuery.addArgument("Category");
                ExpressionBuilder builder = this.findOwnersQuery.getExpressionBuilder();
                findOwnersQuery.setSelectionCriteria(
                    builder.get("category").like(builder.getParameter("Category")));
                Vector args = new Vector();
                args.add(category);
                List result = session.executeQuery(findOwnersQuery, args);
                // do some further stuff with the result list
                return result;
            }
        });
    }
  }

  public class ProductDaoImpl extends TopLinkDaoSupport implements ProductDao {
     public Collection loadProductsByCategory(String category) throws DataAccessException {
        ReadAllQuery findOwnersQuery = new ReadAllQuery(Product.class);
        findOwnersQuery.addArgument("Category");
        ExpressionBuilder builder = this.findOwnersQuery.getExpressionBuilder();
        findOwnersQuery.setSelectionCriteria(
            builder.get("category").like(builder.getParameter("Category")));
        return getTopLinkTemplate().executeQuery(findOwnersQuery, new Object[] {category});
     }
  }
  
  (3) 基于原生的 TopLink API 的 DAO 实现。
   直接使用一个注入的 Session 而无需对 Spring 产生的任何依赖。
   它通常基于一个由 LocalSessionFactoryBean 定义的 SessionFactory，
   并通过 Spring 的 TransactionAwareSessionAdapter 暴露成为一个 Session 类型的引用。 
   <beans>
     <bean id="mySessionAdapter"
          class="org.springframework.orm.toplink.support.TransactionAwareSessionAdapter">
        <property name="sessionFactory" ref="mySessionFactory"/>
     </bean>
     <bean id="myProductDao" class="product.ProductDaoImpl">
        <property name="session" ref="mySessionAdapter"/>
     </bean>
   </beans>

  (4) 事务管理。
  TopLinkTransactionManager 能够将一个 TopLink 事务暴露给访问相同的 JDBC DataSource 的 JDBC 访问代码。
  <bean id="myTxManager" class="org.springframework.orm.toplink.TopLinkTransactionManager">
    <property name="sessionFactory" ref="mySessionFactory"/>
  </bean>
  <bean id="myProductService" class="product.ProductServiceImpl">
    <property name="productDao" ref="myProductDao"/>
  </bean>
  <aop:config>
    <aop:pointcut id="productServiceMethods" expression="execution(* product.ProductService.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="productServiceMethods"/>
  </aop:config>
  <tx:advice id="txAdvice" transaction-manager="myTxManager">
    <tx:attributes>
      <tx:method name="increasePrice*" propagation="REQUIRED"/>
      <tx:method name="someOtherBusinessMethod" propagation="REQUIRES_NEW"/>
      <tx:method name="*" propagation="SUPPORTS" read-only="true"/>
    </tx:attributes>
  </tx:advice>

57.iBATIS SQL Maps。 
  (1) 创建 SqlMapClient。
   <sqlMap namespace="Account">
      <resultMap id="result" class="examples.Account">
         <result property="name" column="NAME" columnIndex="1"/>
         <result property="email" column="EMAIL" columnIndex="2"/>
      </resultMap>
      <select id="getAccountByEmail" resultMap="result">
          select ACCOUNT.NAME, ACCOUNT.EMAIL
          from ACCOUNT
          where ACCOUNT.EMAIL = #value#
      </select>
      <insert id="insertAccount">
          insert into ACCOUNT (NAME, EMAIL) values (#name#, #email#)
      </insert>
   </sqlMap>

  (2) 使用 SqlMapClientTemplate 和 SqlMapClientDaoSupport。
  public class SqlMapAccountDao extends SqlMapClientDaoSupport implements AccountDao {
     public Account getAccount(String email) throws DataAccessException {
        return (Account) getSqlMapClientTemplate().queryForObject("getAccountByEmail", email);
     }
     public void insertAccount(Account account) throws DataAccessException {
        getSqlMapClientTemplate().update("insertAccount", account);
     }
  }

  (3) 基于原生的 iBATIS API 的 DAO 实现。
  public class SqlMapAccountDao implements AccountDao {
     private SqlMapClient sqlMapClient;
     public void setSqlMapClient(SqlMapClient sqlMapClient) {
        this.sqlMapClient = sqlMapClient;
     }
     public Account getAccount(String email) {
        try {
            return (Account) this.sqlMapClient.queryForObject("getAccountByEmail", email);
        } catch (SQLException ex) {
            throw new MyDaoException(ex);
        }
     }
     public void insertAccount(Account account) throws DataAccessException {
        try {
            this.sqlMapClient.update("insertAccount", account);
        } catch (SQLException ex) {
            throw new MyDaoException(ex);
        }
     }
  }
```
