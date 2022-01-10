##### 什么是枚举
Enum关键字在java5中引入，表示一种特殊类型的类，继承自java.lang.Enum类。被enum关键字修饰的类型就是枚举类型，提供了一些基本方法：
如果枚举值不添加任何方法，默认值从0开始，例如：enum Color { RED, GREEN, BLUE }
values()：返回 enum 实例的数组，而且该数组中的元素严格保持在 enum 中声明时的顺序。

name()：返回实例名。

ordinal()：返回实例声明时的次序，从0开始。

getDeclaringClass()：返回实例所属的 enum 类型。

equals()：判断是否为同一个对象。

可以使用 ==来比较enum实例。

此外，java.lang.Enum实现了Comparable和 Serializable接口，所以也提供 compareTo()方法。https://www.apiref.com/cpp-zh/c/language/enum.html
##### 解决了哪些问题
枚举和常量对比，允许进行编译时检查，预先记录可接受值的列表，并避免由于传入无效值而引起的意外行为。

##### 如何在实践中使用
举例：短信验证码有几种不同的用途，定义：

          publicenum PinType {
            REGISTER(100000, "注册使用"),
            FORGET_PASSWORD(100001, "忘记密码使用"),
            UPDATE_PHONE_NUMBER(100002, "更新手机号码使用");

            privatefinalint code;
            privatefinal String message;

            PinType(int code, String message) {
                this.code = code;
                this.message = message;
            }
            public int getCode() {
                return code;
            }
            public String getMessage() {
                return message;
            }
            @Override
            public String toString() {
                return"PinType{" +
                        "code=" + code +
                        ", message='" + message + '\'' +
                        '}';
            }
        }
实际使用：

        System.out.println(PinType.FORGET_PASSWORD.getCode());
        System.out.println(PinType.FORGET_PASSWORD.getMessage());
        System.out.println(PinType.FORGET_PASSWORD.toString());
        
        Output:
        100001
        忘记密码使用
        PinType{code=100001, message='忘记密码使用'}
##### 错误码枚举类型的定义

       public enum ErrorCodeEn {
              OK(0, "成功"),
              ERROR_A(100, "错误A"),
              ERROR_B(200, "错误B");

              ErrorCodeEn(int number, String description) {
                  this.code = number;
                  this.description = description;
              }
              private int code;
              private String description;
              public int getCode() {
                  return code;
              }
              public String getDescription() {
                  return description;
              }
              public static void main(String args[]) {
                  for (ErrorCodeEn s : ErrorCodeEn.values()) {
                      System.out.println("code: " + s.getCode() + ", description: " + s.getDescription());
                  }
              }
          }
##### 枚举可以实现接口
通过实现接口，可以约束它的方法。

          public interface INumberEnum {
              int getCode();
              String getDescription();
          }
          public enum ErrorCodeEn2 implements INumberEnum {
              OK(0, "成功"),
              ERROR_A(100, "错误A"),
              ERROR_B(200, "错误B");

              ErrorCodeEn2(int number, String description) {
                  this.code = number;
                  this.description = description;
              }

              private int code;
              private String description;

              @Override
              public int getCode() {
                  return code;
              }

              @Override
              public String getDescription() {
                  return description;
              }
          }
##### 通过枚举实现一些设计模式
策略枚举：

            enum PayrollDay {
              MONDAY(PayType.WEEKDAY), TUESDAY(PayType.WEEKDAY), WEDNESDAY(
                      PayType.WEEKDAY), THURSDAY(PayType.WEEKDAY), FRIDAY(PayType.WEEKDAY), SATURDAY(
                      PayType.WEEKEND), SUNDAY(PayType.WEEKEND);

              private final PayType payType;
              PayrollDay(PayType payType) {
                  this.payType = payType;
              }
              double pay(double hoursWorked, double payRate) {
                  return payType.pay(hoursWorked, payRate);
              }
              private enum PayType {
                  WEEKDAY {
                      double overtimePay(double hours, double payRate) {
                          return hours <= HOURS_PER_SHIFT ? 0 : (hours - HOURS_PER_SHIFT)
                                  * payRate / 2;
                      }
                  },
                  WEEKEND {
                      double overtimePay(double hours, double payRate) {
                          return hours * payRate / 2;
                      }
                  };
                  private static final int HOURS_PER_SHIFT = 8;

                  abstract double overtimePay(double hrs, double payRate);

                  double pay(double hoursWorked, double payRate) {
                      double basePay = hoursWorked * payRate;
                      return basePay + overtimePay(hoursWorked, payRate);
                  }
              }
          }
测试：

          System.out.println("时薪100的人在周五工作8小时的收入：" + PayrollDay.FRIDAY.pay(8.0, 100));
          System.out.println("时薪100的人在周六工作8小时的收入：" + PayrollDay.SATURDAY.pay(8.0, 100));
