#### 核心组件

            1、HandlerMapping(处理器映射器)
                         BeanNameUrlHandlerMapping(默认)：将请求URL映射到同名的控制器Bean上
                         DefaultAnnotationHandlerMapping：将请求映射到标注@RequestMapping注解的控制和处理方法上
                         RequestMappingHandlerMapping      <mvc:annotation-driven/>
            2、HandlerAdapter（适配器）：具体使用Handler来做事的人
                         AnnotationHandlerAdapter
                         RequestMappingHandlerMappingAdapter
            3、ViewResolver(视图解析器)：解析ModeAndView返回View，交给DispatcherServlet返回页面。
                         InternalResourceView
#### Springmvc配置流程
###### DispatcherServlet是springMVC框架的核心控制器，一个请求的入口，在web.xml里配置：
  
            <!-- springMVC的核心控制器 -->
            <servlet>
              <servlet-name>xingjian007</servlet-name>
              <servlet-class>
                org.springframework.web.servlet.DispatcherServlet
              </servlet-class>
              <load-on-startup>3</load-on-startup>
            </servlet>
            
            //然后需要配置拦截条件
            <servlet-mapping>
              <servlet-name>xingjian007</servlet-name>
              <url-pattern>*.do</url-pattern>
            </servlet-mapping>
            //会拦截.do为结尾的请求，交给DispatcherServlet处理
###### 业务逻辑处理器，可以通过配置文件和注解的方式声明，供DispatcherServlet查找
              
              @Controller
              public class LoginController{
                  private static Logger logger = Logger.getLogger(LoginController.class);

                  @Autowired
                  private UserService userService;

                  @Autowired
                  private Message msg;

                  @RequestMapping(value = "/index.do")
                  public String loginPage(){
                    return "login";
                  }

                  @RequestMapping(value = "/loginCheck.do")
                  public ModelAndView loginCheck(HttpServletRequest request,LoginCommand loginCommand){
                      boolean isValidUser =
                          userService.hasMatchUser(loginCommand.getUserName(),
                              loginCommand.getPassword());
                      if (!isValidUser) {
                          return new ModelAndView("login", "error", "用户名或密码错误。");
                      } else {
                          User user = userService.findUserByUserName(loginCommand
                              .getUserName());
                          user.setLastIp(request.getLocalAddr());
                          user.setLastVisit(new Date());
                          userService.loginSuccess(user);
                          request.getSession().setAttribute("user", user);
                          logger.info(request.getParameter("password"));
                          return new ModelAndView("main");
                      }
                  }
              }
              
              引入了org.springframework.web.bind.annotation.RequestMapping来做控制器的映射
              使用@ Controller来声明控制器
              使用@RequestMapping处理URI请求

###### 配置视图解析器，SpringMVC在处理器方法中通常返回的是逻辑视图ModeAndView，把ModeAndView转换为View才能把数据渲染到具体的页面。

            <?xml version="1.0" encoding="UTF-8" ?>
            <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
              xmlns:context="http://www.springframework.org/schema/context"
              xsi:schemaLocation="http://www.springframework.org/schema/beans
                   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
                   http://www.springframework.org/schema/context
                   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
              <!-- 扫描web包，应用Spring的注解 -->
              <context:component-scan base-package="com.zhong717.web"/>

              <!-- 配置视图解析器，将ModelAndView及字符串解析为具体的页面 -->
              <bean
                class="org.springframework.web.servlet.view.InternalResourceViewResolver"
                p:viewClass="org.springframework.web.servlet.view.JstlView"
                p:prefix="/WEB-INF/jsp/"
                p:suffix=".jsp" />
            </beans>
            SpringMVC里提供了多个视图解析器，InternalResourceViewResolver就是其中之一。
