<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
51.通过使用 SimpleJdbc 类简化 JDBC 操作。
  (1) 使用 SimpleJdbcInsert 插入数据:
   public class JdbcActorDao implements ActorDao {
      private SimpleJdbcTemplate simpleJdbcTemplate;
      private SimpleJdbcInsert insertActor;
      public void setDataSource(DataSource dataSource) {
         this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
         this.insertActor = new SimpleJdbcInsert(dataSource).withTableName("t_actor");
      }
      public void add(Actor actor) {
         Map<String, Object> parameters = new HashMap<String, Object>(3);
         parameters.put("id", actor.getId());
         parameters.put("first_name", actor.getFirstName());
         parameters.put("last_name", actor.getLastName());
         insertActor.execute(parameters);
      }
   }
   
  (2) 使用 SimpleJdbcInsert 来获取自动生成的主键:
   public class JdbcActorDao implements ActorDao {
      private SimpleJdbcTemplate simpleJdbcTemplate;
      private SimpleJdbcInsert insertActor;
      public void setDataSource(DataSource dataSource) {
         this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
         this.insertActor = new SimpleJdbcInsert(dataSource).withTableName("t_actor")
                       .usingGeneratedKeyColumns("id");
      }
      public void add(Actor actor) {
         Map<String, Object> parameters = new HashMap<String, Object>(2);
         parameters.put("first_name", actor.getFirstName());
         parameters.put("last_name", actor.getLastName());
         Number newId = insertActor.executeAndReturnKey(parameters);
         actor.setId(newId.longValue());
      }
   }

  (3) 指定 SimpleJdbcInsert 所使用的字段:
   public class JdbcActorDao implements ActorDao {
     private SimpleJdbcTemplate simpleJdbcTemplate;
     private SimpleJdbcInsert insertActor;
     public void setDataSource(DataSource dataSource) {
        this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
        this.insertActor = new SimpleJdbcInsert(dataSource).withTableName("t_actor")
                       .usingColumns("first_name", "last_name").usingGeneratedKeyColumns("id");
     }
     public void add(Actor actor) {
        Map<String, Object> parameters = new HashMap<String, Object>(2);
        parameters.put("first_name", actor.getFirstName());
        parameters.put("last_name", actor.getLastName());
        Number newId = insertActor.executeAndReturnKey(parameters);
        actor.setId(newId.longValue());
     }
   }

  (4) 使用 SqlParameterSource 提供参数值:
    // 前面的方法与 (3) 相同...
    public void add(Actor actor) {
       SqlParameterSource parameters = new BeanPropertySqlParameterSource(actor);
       Number newId = insertActor.executeAndReturnKey(parameters);
       actor.setId(newId.longValue());
    }

    public void add(Actor actor) {
        SqlParameterSource parameters = new MapSqlParameterSource()
                .addValue("first_name", actor.getFirstName())
                .addValue("last_name", actor.getLastName());
        Number newId = insertActor.executeAndReturnKey(parameters);
        actor.setId(newId.longValue());
    }

  (5) 使用 SimpleJdbcCall 调用存储过程:
    public class JdbcActorDao implements ActorDao {
       private SimpleJdbcTemplate simpleJdbcTemplate;
       private SimpleJdbcCall procReadActor;
       public void setDataSource(DataSource dataSource) {
          this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
          this.procReadActor = new SimpleJdbcCall(dataSource).withProcedureName("read_actor");
       }
       public Actor readActor(Long id) {
          SqlParameterSource in = new MapSqlParameterSource().addValue("in_id", id); 
          Map out = procReadActor.execute(in);
          Actor actor = new Actor();
          actor.setId(id);
          actor.setFirstName((String) out.get("out_first_name"));
          actor.setLastName((String) out.get("out_last_name"));
          actor.setBirthDate((Date) out.get("out_birth_date"));
          return actor;
       }
    }
    
   (6) 声明 SimpleJdbcCall 使用的参数:
    public class JdbcActorDao implements ActorDao {
       private SimpleJdbcCall procReadActor;
       public void setDataSource(DataSource dataSource) {
          JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
          jdbcTemplate.setResultsMapCaseInsensitive(true);
          this.procReadActor =
               new SimpleJdbcCall(jdbcTemplate)
                       .withProcedureName("read_actor")
                       .withoutProcedureColumnMetaDataAccess()
                       .useInParameterNames("in_id")
                       .declareParameters(
                               new SqlParameter("in_id", Types.NUMERIC),
                               new SqlOutParameter("out_first_name", Types.VARCHAR),
                               new SqlOutParameter("out_last_name", Types.VARCHAR),
                               new SqlOutParameter("out_birth_date", Types.DATE)
                       );
       }
    }

   (7) 使用 SimpleJdbcCall 调用内置函数:
    public class JdbcActorDao implements ActorDao {
       private SimpleJdbcTemplate simpleJdbcTemplate;
       private SimpleJdbcCall funcGetActorName;
       public void setDataSource(DataSource dataSource) {
          this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
          JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
          jdbcTemplate.setResultsMapCaseInsensitive(true);
          this.funcGetActorName = new SimpleJdbcCall(jdbcTemplate)
                       .withFunctionName("get_actor_name");
       }
       public String getActorName(Long id) {
          SqlParameterSource in = new MapSqlParameterSource().addValue("in_id", id); 
          String name = funcGetActorName.executeFunction(String.class, in);
          return name;
       }
    }

  (8) 使用 SimpleJdbcCall 返回的 ResultSet/REF Cursor:
    public class JdbcActorDao implements ActorDao {
       private SimpleJdbcTemplate simpleJdbcTemplate;
       private SimpleJdbcCall procReadAllActors;
       public void setDataSource(DataSource dataSource) {
          this.simpleJdbcTemplate = new SimpleJdbcTemplate(dataSource);
          JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
          jdbcTemplate.setResultsMapCaseInsensitive(true);
          this.procReadAllActors = new SimpleJdbcCall(jdbcTemplate)
                      .withProcedureName("read_all_actors")
                      .returningResultSet("actors", ParameterizedBeanPropertyRowMapper.newInstance(Actor.class));
       }
       public List getActorsList() {
          Map m = procReadAllActors.execute(new HashMap<String, Object>(0));
          return (List) m.get("actors");
       }
   }
  
52.用 Java 对象来表达 JDBC 操作。
  (1) SqlQuery 类: 可重用、线程安全的类，它封装了一个 SQL 查询。
其子类必须实现 newResultReader() 方法，该方法用来在遍历 ResultSet 的时候能使用一个类来保存结果。 
  (2) MappingSqlQuery 类: 可重用的查询抽象类，其具体类必须实现 mapRow(ResultSet, int) 抽象方法
来将结果集中的每一行转换成 Java 对象。
   private class CustomerMappingQuery extends MappingSqlQuery {
      public CustomerMappingQuery(DataSource ds) {
         super(ds, "SELECT id, name FROM customer WHERE id = ?");
         super.declareParameter(new SqlParameter("id", Types.INTEGER));
         compile();
      }
      public Object mapRow(ResultSet rs, int rowNumber) throws SQLException {
         Customer cust = new Customer();
         cust.setId((Integer) rs.getObject("id"));
         cust.setName(rs.getString("name"));
         return cust;
      } 
  }
 (3) SqlUpdate 类: 封装了一个可重复使用的 SQL 更新操作。
  import java.sql.Types;
  import javax.sql.DataSource;
  import org.springframework.jdbc.core.SqlParameter;
  import org.springframework.jdbc.object.SqlUpdate;
  public class UpdateCreditRating extends SqlUpdate {
     public UpdateCreditRating(DataSource ds) {
        setDataSource(ds);
        setSql("update customer set credit_rating = ? where id = ?");
        declareParameter(new SqlParameter(Types.NUMERIC));
        declareParameter(new SqlParameter(Types.NUMERIC));
        compile();
     }
     public int run(int id, int rating) {
        Object[] params = new Object[] { new Integer(rating), new Integer(id) };
        return update(params);
     }
  }
 (4) StoredProcedure 类: 抽象基类，它是对 RDBMS 存储过程的一种抽象。
  import oracle.jdbc.driver.OracleTypes;
  import org.springframework.jdbc.core.SqlOutParameter;
  import org.springframework.jdbc.object.StoredProcedure;
  import javax.sql.DataSource;
  import java.util.HashMap;
  import java.util.Map;
  public class TitlesAndGenresStoredProcedure extends StoredProcedure {
     private static final String SPROC_NAME = "AllTitlesAndGenres";
     public TitlesAndGenresStoredProcedure(DataSource dataSource) {
        super(dataSource, SPROC_NAME);
        declareParameter(new SqlOutParameter("titles", OracleTypes.CURSOR, new TitleMapper()));
        declareParameter(new SqlOutParameter("genres", OracleTypes.CURSOR, new GenreMapper()));
        compile();
     }
     public Map execute() {
       return super.execute(new HashMap());
     }
  }
 (5) SqlFunction 类: RDBMS 操作类封装了一个 SQL“ 函数”包装器(wrapper), 该包装器适用于查询并返回一个单行结果集。
  public int countRows() {
     SqlFunction sf = new SqlFunction(dataSource, "select count(*) from mytable");
     sf.compile();
     return sf.run();
  }
```
