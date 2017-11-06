<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
39.集成测试。
  (1) 上下文管理及缓存: Spring 集成测试支持框架提供了 ApplicationContext 的持久化载入和这些上下文的缓存机制。 
  (2) 测试 fixtures 依赖注入: 可以在各种测试场景中重用应用上下文
(例如配置 Spring 管理的对象图， 事务代理 DataSource 等)，从而能避免为单个的测试案例重复进行测试 fixture 搭建。
  (3) 事务管理: Spring 集成测试支持框架可以通过调用一个继承下来的钩子(Hook)方法或声明特定注解来让事务提交而不是回滚。
  (4) 集成测试支持类: ApplicationContext - 用来进行显式 bean 查找或整体测试上下文状态。
JdbcTemplate 或 SimpleJdbcTemplate - 用来查询并确认状态。

40.常用注解。
  (1) @IfProfileValue 
  @IfProfileValue(name="test-groups", values={"unit-tests", "integration-tests"})
  public void testProcessWhichRunsForUnitOrIntegrationTestGroups() {
    // some logic that should run only for unit and integration test groups
  }
  (2) @ProfileValueSourceConfiguration
  @ProfileValueSourceConfiguration(CustomProfileValueSource.class)
  public class CustomProfileValueSourceTests {
    // class body...
  }
  (3) @DirtiesContext
  @DirtiesContext
  public void testProcessWhichDirtiesAppCtx() {
    // some logic that results in the Spring container being dirtied
  }
  (4) @ExpectedException
  @ExpectedException(SomeBusinessException.class)
  public void testProcessRainyDayScenario() {
    // some logic that should result in an Exception being thrown
  }
  (5) @Timed
  @Timed(millis=1000)
  public void testProcessWithOneSecondTimeout() {
    // some logic that should not take longer than 1 second to execute
  }
  (6) @Repeat
  @Repeat(10)
  public void testProcessRepeatedly() { }
  (7) @Rollback
  @Rollback(false)
  public void testProcessWithoutRollback() { }
  (8) @NotTransactional
  @NotTransactional 
  public void testProcessWithoutTransaction() { }

41.JUnit 3.8 遗留支持。
  (1) 上下文管理及缓存:
   protected String[] getConfigLocations();
   protected String[] getConfigPaths();
   protected String getConfigPath();
  (2) 测试 fixture 依赖注入:
  public final class HibernateTitleDaoTests extends AbstractDependencyInjectionSpringContextTests  {
    // this instance will be (automatically) dependency injected    
    private HibernateTitleDao titleDao;
    // a setter method to enable DI of the 'titleDao' instance variable
    public void setTitleDao(HibernateTitleDao titleDao) {
       this.titleDao = titleDao;
    }
    public void testLoadTitle() throws Exception {
       Title title = this.titleDao.loadTitle(new Long(10));
       assertNotNull(title);
    }
    // specifies the Spring configuration to load for this test fixture
    protected String[] getConfigLocations() {
       return new String[] { "classpath:com/foo/daos.xml" };
    }
  }

42.Spring TestContext Framework。
  事务管理:
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration
  @TransactionConfiguration(transactionManager="txMgr", defaultRollback=false)
  @Transactional
  public class FictitiousTransactionalTest {
     @BeforeTransaction
     public void verifyInitialDatabaseState() {
        // logic to verify the initial state before a transaction is started
     }
     
     @Before
     public void setUpTestDataWithinTransaction() {
        // set up test data within the transaction
     }
     
     @Test
     // overrides the class-level defaultRollback setting
     @Rollback(true)
     public void modifyDatabaseWithinTransaction() {
        // logic which uses the test data and modifies database state
     }
     
     @After
     public void tearDownWithinTransaction() {
        // execute "tear down" logic within the transaction
     }
     
     @AfterTransaction
     public void verifyFinalDatabaseState() {
        // logic to verify the final state after transaction has rolled back
     }

     @Test
     @NotTransactional
     public void performNonDatabaseRelatedAction() {
       // logic which does not modify database state
     }
  }
```
