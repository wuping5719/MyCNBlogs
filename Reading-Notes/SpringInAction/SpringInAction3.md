19.定义 Velocity 视图: http://jakarta.apache.org/velocity.
  基于 Velocity 的课程列表: courseList.vm
  <html>
    <head>
      <title>Course List</title>
    </head>
    <body>
      <h2>COURSE LIST</h2>
      <table width="600" border="1" cellspacing="1" cellpadding="1">
        <tr bgcolor="#999999">
          <td>Course ID</td>
          <td>Name</td>
          <td>Instructor</td>
          <td>Start</td>
          <td>End</td>
        </tr>
        #foreach($course in $courses)
        <tr>
          <td><a href="displayCourse.htm?id=${course.id}"> ${course.id} </a></td>
          <td> ${course.name} </td>
          <td> ${course.instructor.lastName} </td>
          <td> ${course.startDate} </td>
          <td> ${course.endDate} </td>
        </tr>
        #end  // 在所有课程中循环
      </table>
    </body>
  </html>

20.配置 Velocity 引擎。
 <bean id="velocityConfigurer" class="org.springframework.web.servlet.view.velocity.VelocityConfigurer">
   <property name="resourceLoaderPath">
      <value>WEB-INF/velocity/</value>
   </property>
   <property name="velocityProperties">
      <props>
        <prop key="directive.foreach.counter.name">loopCounter</prop>
        <prop key="directive.foreach.counter.initial.value">0</prop>
      </props>
   </property>
 </bean>

21.解析 Velocity 视图。
 <bean id="viewResolver" class="org.springframework.web.servlet.view.velocity.VelocityViewResolver">
    <property name="suffix"><value>.vm</value></property>
 </bean>
