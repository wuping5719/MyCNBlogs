<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
48.利用 JDBC 核心类控制 JDBC 的基本操作和错误处理。
  (1) JdbcTemplate 类:
  int countOfActorsNamedJoe = this.jdbcTemplate.queryForInt(
       "select count(0) from t_actors where first_name = ?", new Object[]{"Joe"});

  public Collection findAllActors() {
      return this.jdbcTemplate.query( "select first_name, surname from t_actor", new ActorMapper());
  }
  private static final class ActorMapper implements RowMapper {
      public Object mapRow(ResultSet rs, int rowNum) throws SQLException {
         Actor actor = new Actor();
         actor.setFirstName(rs.getString("first_name"));
         actor.setSurname(rs.getString("surname"));
         return actor;
      }
  }

  this.jdbcTemplate.update("delete from actor where id = ?", new Object[] {new Long.valueOf(actorId)});

  this.jdbcTemplate.execute("create table mytable (id integer, name varchar(100))");

  public class JdbcCorporateEventDao implements CorporateEventDao {
     private JdbcTemplate jdbcTemplate;
     public void setDataSource(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
     }
  }

  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
    <bean id="corporateEventDao" class="com.example.JdbcCorporateEventDao">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!-- the DataSource (parameterized for configuration via a PropertyPlaceHolderConfigurer) -->
    <bean id="dataSource" destroy-method="close" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
  </beans>

  (2) NamedParameterJdbcTemplate 类:
   private NamedParameterJdbcTemplate namedParameterJdbcTemplate;
   public void setDataSource(DataSource dataSource) {
      this.namedParameterJdbcTemplate = new NamedParameterJdbcTemplate(dataSource);
   }
   public int countOfActorsByFirstName(String firstName) {
      String sql = "select count(0) from T_ACTOR where first_name = :first_name";
      SqlParameterSource namedParameters = new MapSqlParameterSource("first_name", firstName);
      return namedParameterJdbcTemplate.queryForInt(sql, namedParameters);
   }

  (3) SimpleJdbcTemplate 类:
   private SimpleJdbcTemplate simpleJdbcTemplate;
   public void setDataSource(DataSource dataSource) {
      this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
   }
   public Actor findActor(long id) {
      String sql = "select id, first_name, last_name from T_ACTOR where id = ?";
      ParameterizedRowMapper<Actor> mapper = new ParameterizedRowMapper<Actor>() {
         // notice the return type with respect to Java 5 covariant return types
         public Actor mapRow(ResultSet rs, int rowNum) throws SQLException {
            Actor actor = new Actor();
            actor.setId(rs.getLong("id"));
            actor.setFirstName(rs.getString("first_name"));
            actor.setLastName(rs.getString("last_name"));
            return actor;
         }
      };
      return this.simpleJdbcTemplate.queryForObject(sql, mapper, id);
   }
   
   (4) DataSource 接口:
   DriverManagerDataSource dataSource = new DriverManagerDataSource();
   dataSource.setDriverClassName("org.hsqldb.jdbcDriver");
   dataSource.setUrl("jdbc:hsqldb:hsql://localhost:");
   dataSource.setUsername("sa");
   dataSource.setPassword("");

  (5) SQLExceptionTranslator 接口:
   public class MySQLErrorCodesTranslator extends SQLErrorCodeSQLExceptionTranslator {
      protected DataAccessException customTranslate(String task, String sql, SQLException sqlex) {
         if (sqlex.getErrorCode() == -12345) {
            return new DeadlockLoserDataAccessException(task, sqlex);
         }
         return null;
      }
   }
```
