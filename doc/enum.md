##### 什么是枚举
enum关键字在java5中引入，表示一种特殊类型的类，继承自java.lang.Enum类。https://www.apiref.com/cpp-zh/c/language/enum.html
##### 解决了哪些问题
枚举在很多时候会和常量拿来对比，可能因为本身大量实际使用枚举的地方就是为了替代常量。以这种方式定义的常量使代码更具可读性，允许进行编译时检查，预先记录可接受值的列表，并避免由于传入无效值而引起的意外行为。

##### 如何在实践中使用
举例：调用短信验证码的时候可能有几种不同的用途，下面这样定义：

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
##### EnumSet and EnumMap
EnumSet 是一种专门为枚举类型所设计的 Set 类型。它提供了类型安全的替代方法，以替代传统的基于int的“位标志”，使我们能够编写更易读和易于维护的简洁代码。在很多场景中的枚举常量集合操作（如：取子集、增加、删除、containsAll和removeAll批操作）使用EnumSet非常合适；如果需要迭代所有可能的常量则使用Enum.values()。

        publicclass Pizza {
            privatestatic EnumSet<PizzaStatus> undeliveredPizzaStatuses =
              EnumSet.of(PizzaStatus.ORDERED, PizzaStatus.READY);
            private PizzaStatus status;
            publicenum PizzaStatus {
                ...
            }
            public boolean isDeliverable() {
                returnthis.status.isReady();
            }
            public void printTimeToDeliver() {
                System.out.println("Time to delivery is " +
                  this.getStatus().getTimeToDelivery() + " days");
            }
            public static List<Pizza> getAllUndeliveredPizzas(List<Pizza> input) {
                return input.stream().filter(
                  (s) -> undeliveredPizzaStatuses.contains(s.getStatus()))
                    .collect(Collectors.toList());
            }
            public void deliver() {
                if (isDeliverable()) {
                    PizzaDeliverySystemConfiguration.getInstance().getDeliveryStrategy()
                      .deliver(this);
                    this.setStatus(PizzaStatus.DELIVERED);
                }
            }
            // Methods that set and get the status variable.
        }
##### EnumMap
EnumMap是一个专门化的映射实现，用于将枚举常量用作键.

        publicstatic EnumMap<PizzaStatus, List<Pizza>>
          groupPizzaByStatus(List<Pizza> pizzaList) {
            EnumMap<PizzaStatus, List<Pizza>> pzByStatus =
              new EnumMap<PizzaStatus, List<Pizza>>(PizzaStatus.class);

            for (Pizza pz : pizzaList) {
                PizzaStatus status = pz.getStatus();
                if (pzByStatus.containsKey(status)) {
                    pzByStatus.get(status).add(pz);
                } else {
                    List<Pizza> newPzList = new ArrayList<Pizza>();
                    newPzList.add(pz);
                    pzByStatus.put(status, newPzList);
                }
            }
            return pzByStatus;
        }
##### 通过枚举实现一些设计模式
《Java与模式》中，作者这样写道，使用枚举来实现单实例控制会更加简洁，而且无偿地提供了序列化机制，并由JVM从根本上提供保障，绝对防止多次实例化，是更简洁、高效、安全的实现单例的方式。<br>
下面的代码段显示了如何使用枚举实现单例模式：

            publicenum PizzaDeliverySystemConfiguration {
                INSTANCE;
                PizzaDeliverySystemConfiguration() {
                    // Initialization configuration which involves
                    // overriding defaults like delivery strategy
                }

                private PizzaDeliveryStrategy deliveryStrategy = PizzaDeliveryStrategy.NORMAL;

                public static PizzaDeliverySystemConfiguration getInstance() {
                    return INSTANCE;
                }

                public PizzaDeliveryStrategy getDeliveryStrategy() {
                    return deliveryStrategy;
                }
            }
使用:
            PizzaDeliveryStrategy deliveryStrategy = PizzaDeliverySystemConfiguration.getInstance().getDelive
通过 PizzaDeliverySystemConfiguration.getInstance() 获取的就是单例的 PizzaDeliverySystemConfiguration。
