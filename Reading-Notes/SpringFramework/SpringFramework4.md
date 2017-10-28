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
```
