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
##### Map的computeIfAbsent
利用Map的computeIfAbsent方法，可以保证获取到的对象非空，从而避免了不必要的空判断和重新设置值

          普通：
          Map<Long, List<UserDO>> roleUserMap = new HashMap<>();
          for (UserDO userDO : userDOList) {
              Long roleId = userDO.getRoleId();
              List<UserDO> userList = roleUserMap.get(roleId);
              if (Objects.isNull(userList)) {
                  userList = new ArrayList<>();
                  roleUserMap.put(roleId, userList);
              }
              userList.add(userDO);
          }
          
          精简：
          Map<Long, List<UserDO>> roleUserMap = new HashMap<>();
          for (UserDO userDO : userDOList) {
              roleUserMap.computeIfAbsent(userDO.getRoleId(), key -> new ArrayList<>())
                  .add(userDO);
          }
##### 链式编程
链式编程，也叫级联式编程，调用对象的函数时返回一个this对象指向对象本身，达到链式效果，可以级联调用。链式编程的优点是：编程性强、可读性强、代码简洁。

          普通
          StringBuilder builder = new StringBuilder(96);
          builder.append("select id, name from ");
          builder.append(T_USER);
          builder.append(" where id = ");
          builder.append(userId);
          builder.append(";");
          
          精简：
          StringBuilder builder = new StringBuilder(96);
          builder.append("select id, name from ")
              .append(T_USER)
              .append(" where id = ")
              .append(userId)
              .append(";");
#### 工具方法
##### 避免空值判断

          普通：
          if (userList != null && !userList.isEmpty()) {
              // TODO
          }
          
          精简：
          if (CollectionUtils.isNotEmpty(userList)) {
              // TODO
          }
##### 避免条件判断

          普通：
          double result;
          if (value <= MIN_LIMIT) {
              result = MIN_LIMIT;
          } else {
              result = value;
          }
          
          精简：
          double result = Math.max(MIN_LIMIT, value);
##### 简化赋值语句

          普通：
          public static final List<String> ANIMAL_LIST;
          static {
              List<String> animalList = new ArrayList<>();
              animalList.add("dog");
              animalList.add("cat");
              animalList.add("tiger");
              ANIMAL_LIST = Collections.unmodifiableList(animalList);
          }
          
          精简：
          // JDK流派
          public static final List<String> ANIMAL_LIST = Arrays.asList("dog", "cat", "tiger");
          // Guava流派
          public static final List<String> ANIMAL_LIST = ImmutableList.of("dog", "cat", "tiger");
          注意：Arrays.asList 返回的 List 并不是 ArrayList ，不支持 add 等变更操作。
##### 简化数据拷贝

          普通：

          UserVO userVO = new UserVO();
          userVO.setId(userDO.getId());
          userVO.setName(userDO.getName());
          ...
          userVO.setDescription(userDO.getDescription());
          userVOList.add(userVO);
          
          精简：
          UserVO userVO = new UserVO();
          BeanUtils.copyProperties(userDO, userVO);
          userVOList.add(userVO);
##### 简化异常断言

          普通：
          if (Objects.isNull(userId)) {
              throw new IllegalArgumentException("用户标识不能为空");
          }
          
          精简：
          Assert.notNull(userId, "用户标识不能为空");
          注意：可能有些插件不认同这种判断，导致使用该对象时会有空指针警告。
          
##### 简化测试用例
把测试用例数据以JSON格式存入文件中，通过JSON的parseObject和parseArray方法解析成对象。虽然执行效率上有所下降，但可以减少大量的赋值语句，从而精简了测试代码。

          普通：
          @Test
          public void testCreateUser() {
              UserCreateVO userCreate = new UserCreateVO();
              userCreate.setName("Changyi");
              userCreate.setTitle("Developer");
              userCreate.setCompany("AMAP");
              ...
              Long userId  = userService.createUser(OPERATOR, userCreate);
              Assert.assertNotNull(userId, "创建用户失败");
          }
          
          精简：
          @Test
          public void testCreateUser() {
              String jsonText = ResourceHelper.getResourceAsString(getClass(), "createUser.json");
              UserCreateVO userCreate = JSON.parseObject(jsonText, UserCreateVO.class);
              Long userId  = userService.createUser(OPERATOR, userCreate);
              Assert.assertNotNull(userId, "创建用户失败");
          }
#### 利用数据结构
##### 利用数组简化
对于固定上下限范围的 if-else 语句，可以用数组+循环来简化

          普通：
          public static int getGrade(double score) {
              if (score >= 90.0D) {
                  return 1;
              }
              if (score >= 80.0D) {
                  return 2;
              }
              if (score >= 60.0D) {
                  return 3;
              }
              if (score >= 30.0D) {
                  return 4;
              }
              return 5;
          }
          
          精简：
          private static final double[] SCORE_RANGES = new double[] {90.0D, 80.0D, 60.0D, 30.0D};
          public static int getGrade(double score) {
              for (int i = 0; i < SCORE_RANGES.length; i++) {
                  if (score >= SCORE_RANGES[i]) {
                      return i + 1;
                  }
              }
              return SCORE_RANGES.length + 1;
          }
##### 利用Map简化

          普通：
          public static String getBiologyClass(String name) {
              switch (name) {
                  case "dog" :
                      return "animal";
                  case "cat" :
                      return "animal";
                  case "lavender" :
                      return "plant";
                  ...
                  default :
                      return null;
              }
          }
          
          精简：
          private static final Map<String, String> BIOLOGY_CLASS_MAP
              = ImmutableMap.<String, String>builder()
                  .put("dog", "animal")
                  .put("cat", "animal")
                  .put("lavender", "plant")
                  ...
                  .build();
          public static String getBiologyClass(String name) {
              return BIOLOGY_CLASS_MAP.get(name);
          }
##### 利用容器类简化

          普通：
          @Setter
          @Getter
          @ToString
          @AllArgsConstructor
          public static class PointAndDistance {
              private Point point;
              private Double distance;
          }

          public static PointAndDistance getNearest(Point point, Point[] points) {
              // 计算最近点和距离
              ...

              // 返回最近点和距离
              return new PointAndDistance(nearestPoint, nearestDistance);
          }
          
          精简：
          public static Pair<Point, Double> getNearest(Point point, Point[] points) {
              // 计算最近点和距离
              ...

              // 返回最近点和距离
              return ImmutablePair.of(nearestPoint, nearestDistance);
          }
##### 利用ThreadLocal简化
ThreadLocal提供了线程专有对象，可以在整个线程生命周期中随时取用，极大地方便了一些逻辑的实现。用ThreadLocal保存线程上下文对象，可以避免不必要的参数传递。

          普通：
          由于 DateFormat 的 format 方法线程非安全（建议使用替代方法），在线程中频繁初始化 DateFormat 性能太低，如果考虑重用只能用参数传入 DateFormat 。例子如下：
          public static String formatDate(Date date, DateFormat format) {
              return format.format(date);
          }
          public static List<String> getDateList(Date minDate, Date maxDate, DateFormat format) {
              List<String> dateList = new ArrayList<>();
              Calendar calendar = Calendar.getInstance();
              calendar.setTime(minDate);
              String currDate = formatDate(calendar.getTime(), format);
              String maxsDate = formatDate(maxDate, format);
              while (currDate.compareTo(maxsDate) <= 0) {
                  dateList.add(currDate);
                  calendar.add(Calendar.DATE, 1);
                  currDate = formatDate(calendar.getTime(), format);
              }
              return dateList;
          }
          
          精简：
          可能你会觉得以下的代码量反而多了，如果调用工具方法的地方比较多，就可以省下一大堆 DateFormat 初始化和传入参数的代码。


          private static final ThreadLocal<DateFormat> LOCAL_DATE_FORMAT = new ThreadLocal<DateFormat>() {
              @Override
              protected DateFormat initialValue() {
                  return new SimpleDateFormat("yyyyMMdd");
              }
          };

          public static String formatDate(Date date) {
              return LOCAL_DATE_FORMAT.get().format(date);
          }

          public static List<String> getDateList(Date minDate, Date maxDate) {
              List<String> dateList = new ArrayList<>();
              Calendar calendar = Calendar.getInstance();
              calendar.setTime(minDate);
              String currDate = formatDate(calendar.getTime());
              String maxsDate = formatDate(maxDate);
              while (currDate.compareTo(maxsDate) <= 0) {
                  dateList.add(currDate);
                  calendar.add(Calendar.DATE, 1);
                  currDate = formatDate(calendar.getTime());
              }
              return dateList;
          }
          注意：ThreadLocal 有一定的内存泄露的风险，尽量在业务代码结束前调用 remove 方法进行数据清除。
#####
