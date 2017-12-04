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
```
