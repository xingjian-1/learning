#### MVC模式的理解
###### MVC(Model，View，Controller)是一种web应用程序的开发模式，即Servlet+JSP+JavaBean模式。servlet负责处理用户请求，jsp负责数据显示，javabean负责封装数据。
                        
                        MVC+三层结构   
                        MVC指的是Servlet + Jsp + JavaBean。M(Model)对应JavaBean，V（View）对应Jsp，C（Controller）对应Servlet。
                        三层结构指的是持久层（dao层）、业务逻辑层（service层）、表现层（web层）
###### SpringMVC架构的特点
                        
                        1、Spring MVC在Model、View和Controller方面提供了一个非常清晰的角色划分，这三个方面各司其职，各负其责；
                        2、灵活的配置。因为Spring的核心是IOC，同样在实现MVC上，也可以把各种类当作Bean来通过XML进行配置；
                        3、提供了大量的控制器接口和实现类。开发者可以使用Spring提供 的控制器实现类，也可以自己实现控制器接口；
                        4、真正做到与View的实现无关（JSP、Velocity、XSLT等）。它不会强制开发者使用JSP，也可以根据项目需求使用Velocity、XSLT等技术，使用起来更加灵活；
                        5、国际化支持；
                        6、面向接口编程；
                        7、Spring提供了Web应用开发的一整套流程，不仅仅是MVC它们之间可以很方便地结合一起

                        好框架=减轻开发者处理复杂问题的负担+良好内部扩展+支持它的强大的用户群体。
#### 核心组件
            1、DispatcherServlet(中央处理器)：用户请求到达前端控制器，它就相当于mvc模式中的c,
            dispatcherServlet是整个流程控制的中心，由它调用其他组件处理用户的请求，
            dispatcherServlet的存在降低了组件之间的耦合性。
            
            2、HandlerMapping(处理器映射器)：HandlerMapping负责根据用户请求找到Handler（即处理器），
            springmvc提供了不同的映射器实现不同的映射方式。例如：配置文件方式，实现接口方式，注解方式等
                         BeanNameUrlHandlerMapping(默认)：将请求URL映射到同名的控制器Bean上
                         DefaultAnnotationHandlerMapping：将请求映射到标注@RequestMapping注解的控制和处理方法上
                         RequestMappingHandlerMapping      <mvc:annotation-driven/>
              
            3、HandlerAdapter（适配器）：适配到具体的处理器来处理用户的请求
                         AnnotationHandlerAdapter
                         RequestMappingHandlerMappingAdapter
            4、Handler(处理器)：Handler是继DispatcherServlet前端控制器的后端控制器，控制业务逻辑的处理。
            
            5、ViewResolver(视图解析器)：解析ModeAndView返回View，交给DispatcherServlet返回页面。
            ViewReslover负责处理结果生成View视图，ViewReslover首先根据逻辑视图名解析成物理视图名即具体的页面地址，
            再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。
                         InternalResourceView
            
            6.View(视图)：springmvc框架提供了很多的View视图类型的支持，包括jstView、freemakerView、pdfView等。我们最常用的视图就是jsp。
            
            说明：在springmvc的各个组件中，处理器映射器、处理器适配器、视图解析器成为springmvc的三大组件。  
            需要用户开发的组件有handler、view。
#### 配置流程
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
              <context:component-scan base-package="com.xingjian007.web"/>

              <!-- 配置视图解析器，将ModelAndView及字符串解析为具体的页面 -->
              <bean
                class="org.springframework.web.servlet.view.InternalResourceViewResolver"
                p:viewClass="org.springframework.web.servlet.view.JstlView"
                p:prefix="/WEB-INF/jsp/"
                p:suffix=".jsp" />
            </beans>
            SpringMVC里提供了多个视图解析器，InternalResourceViewResolver就是其中之一。
###### SpringMVC执行流程:

                1.用户发送请求至前端控制器DispatcherServlet
                2.DispatcherServlet收到请求调用处理器映射器HandlerMapping。
                3.处理器映射器根据请求url找到具体的处理器，
                  生成处理器执行链HandlerExecutionChain(包括处理器对象和处理器拦截器)一并返回给DispatcherServlet。
                4.DispatcherServlet根据处理器Handler获取处理器适配器HandlerAdapter执行HandlerAdapter处理一系列的操作
                 ，如：参数封装，数据格式转换，数据验证等操作
                5.执行处理器Handler(Controller，Controller调用服务层方法处理业务逻辑)。
                6.Handler执行完成返回ModelAndView
                7.HandlerAdapter将Handler执行结果ModelAndView返回到DispatcherServlet
                8.DispatcherServlet将ModelAndView传给ViewReslover视图解析器
                9.ViewReslover解析后返回具体View
                10.DispatcherServlet对View进行渲染视图（即将模型数据model填充至视图中）。
                11.DispatcherServlet响应用户。
            
