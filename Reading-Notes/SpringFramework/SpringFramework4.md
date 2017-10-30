<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
14.使用 Spring 的 Validator 接口进行校验。
   public class CustomerValidator implements Validator {
      private final Validator addressValidator;
      public CustomerValidator(Validator addressValidator) {
         if (addressValidator == null) {
             throw new IllegalArgumentException(
                 "The supplied [Validator] is required and must not be null.");
         }
         if (!addressValidator.supports(Address.class)) {
             throw new IllegalArgumentException(
                 "The supplied [Validator] must support the validation of [Address] instances.");
         }
         this.addressValidator = addressValidator;
      }
      // This Validator validates Customer instances, and any subclasses of Customer too
      // supports(Class)：表示这个Validator是否支持该 Class 的实例.
      public boolean supports(Class clazz) {
         return Customer.class.isAssignableFrom(clazz);
      }
      // 对提供的对象进行校验，并将校验的错误注册到传入的 Errors 对象中.
      public void validate(Object target, Errors errors) {
         ValidationUtils.rejectIfEmptyOrWhitespace(errors, "firstName", "field.required");
         ValidationUtils.rejectIfEmptyOrWhitespace(errors, "surname", "field.required");
         Customer customer = (Customer) target;
         try {
            errors.pushNestedPath("address");
            ValidationUtils.invokeValidator(this.addressValidator, customer.getAddress(), errors);
         } finally {
            errors.popNestedPath();
         }
      }
   }

15.Bean 处理和 BeanWrapper。
  (1) 设置和获取属性值以及嵌套属性：
  public class Company {
    private String name;
    private Employee managingDirector;
    public String getName()	{ 
       return this.name; 
    }
    public void setName(String name) { 
       this.name = name; 
    } 
    public Employee getManagingDirector() { 
       return this.managingDirector; 
    }
    public void setManagingDirector(Employee managingDirector) {
        this.managingDirector = managingDirector;
    }
  }

  public class Employee {
    private String name;
    private float salary;
    public String getName()	{
       return this.name;
    }
    public void setName(String name) {
       this.name = name;
    }
    public float getSalary() {
       return salary;
    }
    public void setSalary(float salary) {
       this.salary = salary;
    }
  }

  BeanWrapper company = BeanWrapperImpl(new Company());
  // setting the company name.
  company.setPropertyValue("name", "Some Company Inc.");
  // ... can also be done like this:
  PropertyValue value = new PropertyValue("name", "Some Company Inc.");
  company.setPropertyValue(value);
  
  // ok, let's create the director and tie it to the company:
  BeanWrapper jim = BeanWrapperImpl(new Employee());
  jim.setPropertyValue("name", "Jim Stravinsky");
  company.setPropertyValue("managingDirector", jim.getWrappedInstance());
  
  // retrieving the salary of the managingDirector through the company
  Float salary = (Float) company.getPropertyValue("managingDirector.salary");

  (2) 内建的 PropertyEditor 实现：
  public class FooBeanInfo extends SimpleBeanInfo {
    public PropertyDescriptor[] getPropertyDescriptors() {
        try {
            final PropertyEditor numberPE = new CustomNumberEditor(Integer.class, true);
            PropertyDescriptor ageDescriptor = new PropertyDescriptor("age", Foo.class) {
                public PropertyEditor createPropertyEditor(Object bean) {
                    return numberPE;
                };
            };
            return new PropertyDescriptor[] { ageDescriptor };
        }
        catch (IntrospectionException ex) {
            throw new Error(ex.toString());
        }
    }
  }

  (3) 使用 PropertyEditorRegistrars：
  package com.foo.editors.spring;
  public final class CustomPropertyEditorRegistrar implements PropertyEditorRegistrar {
    public void registerCustomEditors(PropertyEditorRegistry registry) {
       // it is expected that new PropertyEditor instances are created
       registry.registerCustomEditor(ExoticType.class, new ExoticTypeEditor());
       // you could register as many custom property editors as are required here.
    }
  }

  <bean class="org.springframework.beans.factory.config.CustomEditorConfigurer">
    <property name="propertyEditorRegistrars">
        <list>
            <ref bean="customPropertyEditorRegistrar"/>
        </list>
    </property>
  </bean>
  <bean id="customPropertyEditorRegistrar" class="com.foo.editors.spring.CustomPropertyEditorRegistrar"/>

  public final class RegisterUserController extends SimpleFormController {
     private final PropertyEditorRegistrar customPropertyEditorRegistrar;
     public RegisterUserController(PropertyEditorRegistrar propertyEditorRegistrar) {
         this.customPropertyEditorRegistrar = propertyEditorRegistrar;
     }
     protected void initBinder(HttpServletRequest request, ServletRequestDataBinder binder) 
               throws Exception {
         this.customPropertyEditorRegistrar.registerCustomEditors(binder);
     }
     // other methods to do with registering a User
  }
```
