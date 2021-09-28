#### 利用语法
##### 三元表达式

          普通：
          String title;
          if (isMember(phone)) {
              title = "会员";
          } else {
              title = "游客";
          }
          
          精简：
          String title = isMember(phone) ? "会员" : "游客";
          注意：对于包装类型的算术计算，需要注意避免拆包时的空指针问题。
##### for-each语句
 
          普通：
          double[] values = ...;
          for(int i = 0; i < values.length; i++) {
              double value = values[i];
              // TODO
          }
          List<Double> valueList = ...;
          Iterator<Double> iterator = valueList.iterator();
          while (iterator.hasNext()) {
              Double value = iterator.next();
              // TODO
          }
          
          精简：
          double[] values = ...;
          for(double value : values) {
              // TODO
          }

          List<Double> valueList = ...;
          for(Double value : valueList) {
              // TODO
          }
##### try-with-resource语句

          普通：
          BufferedReader reader = null;
          try {
              reader = new BufferedReader(new FileReader("cities.csv"));
              String line;
              while ((line = reader.readLine()) != null) {
                  // TODO
              }
          } catch (IOException e) {
              log.error("读取文件异常", e);
          } finally {
              if (reader != null) {
                  try {
                      reader.close();
                  } catch (IOException e) {
                      log.error("关闭文件异常", e);
                  }
              }
          }
          
          精简：
          try (BufferedReader reader = new BufferedReader(new FileReader("test.txt"))) {
              String line;
              while ((line = reader.readLine()) != null) {
                  // TODO
              }
          } catch (IOException e) {
              log.error("读取文件异常", e);
          }
##### return关键字

          普通：
          public static boolean hasSuper(@NonNull List<UserDO> userList) {
              boolean hasSuper = false;
              for (UserDO user : userList) {
                  if (Boolean.TRUE.equals(user.getIsSuper())) {
                      hasSuper = true;
                      break;
                  }
              }
              return hasSuper;
          }

          精简：
          public static boolean hasSuper(@NonNull List<UserDO> userList) {
              for (UserDO user : userList) {
                  if (Boolean.TRUE.equals(user.getIsSuper())) {
                      return true;
                  }
              }
              return false;
          }
##### static关键字

          普通：
          public final class GisHelper {
              public double distance(double lng1, double lat1, double lng2, double lat2) {
                  // 方法实现代码
              }
          }
          GisHelper gisHelper = new GisHelper();
          double distance = gisHelper.distance(116.178692D, 39.967115D, 116.410778D, 39.899721D);
          
          精简：
          public final class GisHelper {
              public static double distance(double lng1, double lat1, double lng2, double lat2) {
                  // 方法实现代码
              }
          }
          double distance = GisHelper.distance(116.178692D, 39.967115D, 116.410778D, 39.899721D);
##### lambda表达式

          普通：
          new Thread(new Runnable() {
              public void run() {
                  // todo
              }
          }).start();
          
          精简：
          new Thread(() -> {
              // todo
          }).start();
##### 方法引用

          普通：
          Arrays.sort(nameArray, (a, b) -> a.compareToIgnoreCase(b));
          List<Long> userIdList = userList.stream()
              .map(user -> user.getId())
              .collect(Collectors.toList());
              
          精简：
          Arrays.sort(nameArray, String::compareToIgnoreCase);
          List<Long> userIdList = userList.stream()
              .map(UserDO::getId)
              .collect(Collectors.toList());
##### unchecked异常
Java的异常分为两类：Checked异常和Unchecked异常。Unchecked异常继承了RuntimeException ，特点是代码不需要处理它们也能通过编译，所以它们称作Unchecked异常。利用Unchecked异常，可以避免不必要的 try-catch和throws异常处理。

          普通：
          @Service
          public class UserService {
              public void createUser(UserCreateVO create, OpUserVO user) throws BusinessException {
                  checkOperatorUser(user);
                  ...
              }
              private void checkOperatorUser(OpUserVO user) throws BusinessException {
                  if (!hasPermission(user)) {
                      throw new BusinessException("用户无操作权限");
                  }
                  ...
              }
              ...
          }

          @RestController
          @RequestMapping("/user")
          public class UserController {
              @Autowired
              private UserService userService;

              @PostMapping("/createUser")
              public Result<Void> createUser(@RequestBody @Valid UserCreateVO create, OpUserVO user) throws BusinessException {
                  userService.createUser(create, user);
                  return Result.success();
              }
              ...
          }
          
          精简：
          @Service
          public class UserService {
              public void createUser(UserCreateVO create, OpUserVO user) {
                  checkOperatorUser(user);
                  ...
              }
              private void checkOperatorUser(OpUserVO user) {
                  if (!hasPermission(user)) {
                      throw new BusinessRuntimeException("用户无操作权限");
                  }
                  ...
              }
              ...
          }

          @RestController
          @RequestMapping("/user")
          public class UserController {
              @Autowired
              private UserService userService;

              @PostMapping("/createUser")
              public Result<Void> createUser(@RequestBody @Valid UserCreateVO create, OpUserVO user) {
                  userService.createUser(create, user);
                  return Result.success();
              }
              ...
          }
#### 注解
##### Lombok注解
Lombok提供了一组有用的注解，可以用来消除Java类中的大量样板代码

          普通：
          public class UserVO {
              private Long id;
              private String name;
              public Long getId() {
                  return this.id;
              }
              public void setId(Long id) {
                  this.id = id;
              }
              public String getName() {
                  return this.name;
              }
              public void setName(String name) {
                  this.name = name;
              }
              ...
          }
          
          精简：
          @Getter
          @Setter
          @ToString
          public class UserVO {
              private Long id;
              private String name;
              ...
          }
##### @NonNull注解
Spring的@NonNull注解，用于标注参数或返回值非空。

          普通：
          public List<UserVO> queryCompanyUser(Long companyId) {
              // 检查公司标识
              if (companyId == null) {
                  return null;
              }

              // 查询返回用户
              List<UserDO> userList = userDAO.queryByCompanyId(companyId);
              return userList.stream().map(this::transUser).collect(Collectors.toList());
          }

          Long companyId = 1L;
          List<UserVO> userList = queryCompanyUser(companyId);
          if (CollectionUtils.isNotEmpty(userList)) {
              for (UserVO user : userList) {
                  // TODO
              }
          }
          
          精简：
          public @NonNull List<UserVO> queryCompanyUser(@NonNull Long companyId) {
              List<UserDO> userList = userDAO.queryByCompanyId(companyId);
              return userList.stream().map(this::transUser).collect(Collectors.toList());
          }

          Long companyId = 1L;
          List<UserVO> userList = queryCompanyUser(companyId);
          for (UserVO user : userList) {
              // TODO
          }
##### 利用注解特性
注解有以下特性可用于精简注解声明：
1、当注解属性值跟默认值一致时，可以删除该属性赋值；
2、当注解只有value属性时，可以去掉value进行简写；
3、当注解属性组合等于另一个特定注解时，直接采用该特定注解。

          普通：
          @Lazy(true);
          @Service(value = "userService")
          @RequestMapping(path = "/getUser", method = RequestMethod.GET)
          
          精简：
          @Lazy
          @Service("userService")
          @GetMapping("/getUser")
#### 利用泛型
##### 泛型接口
在Java没有引入泛型前，都是采用Object表示通用对象，最大的问题就是类型无法强校验并且需要强制类型转换。

          普通：
          public interface Comparable {
              public int compareTo(Object other);
          }

          @Getter
          @Setter
          @ToString
          public class UserVO implements Comparable {
              private Long id;
              @Override
              public int compareTo(Object other) {
                  UserVO user = (UserVO)other;
                  return Long.compare(this.id, user.id);
              }
          }
          
          精简：
          public interface Comparable<T> {
              public int compareTo(T other);
          }

          @Getter
          @Setter
          @ToString
          public class UserVO implements Comparable<UserVO> {
              private Long id;

              @Override
              public int compareTo(UserVO other) {
                  return Long.compare(this.id, other.id);
              }
          }
##### 泛型类

          普通：
          @Getter
          @Setter
          @ToString
          public class IntPoint {
              private Integer x;
              private Integer y;
          }

          @Getter
          @Setter
          @ToString
          public class DoublePoint {
              private Double x;
              private Double y;
          }
          
          精简：
          @Getter
          @Setter
          @ToString
          public class Point<T extends Number> {
              private T x;
              private T y;
          }
##### 泛型方法

          普通：
          public static Map<String, Integer> newHashMap(String[] keys, Integer[] values) {
              // 检查参数非空
              if (ArrayUtils.isEmpty(keys) || ArrayUtils.isEmpty(values)) {
                  return Collections.emptyMap();
              }

              // 转化哈希映射
              Map<String, Integer> map = new HashMap<>();
              int length = Math.min(keys.length, values.length);
              for (int i = 0; i < length; i++) {
                  map.put(keys[i], values[i]);
              }
              return map;
          }
          
          精简：
          public static <K, V> Map<K, V> newHashMap(K[] keys, V[] values) {
              // 检查参数非空
              if (ArrayUtils.isEmpty(keys) || ArrayUtils.isEmpty(values)) {
                  return Collections.emptyMap();
              }

              // 转化哈希映射
              Map<K, V> map = new HashMap<>();
              int length = Math.min(keys.length, values.length);
              for (int i = 0; i < length; i++) {
                  map.put(keys[i], values[i]);
              }
              return map;
          }
#####

#####

#####

#####

#####

#####

#####
