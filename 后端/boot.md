# 注解

**注解的优势：**

- 不需要配置繁杂的xml文件

- 类型安全对重构可以提供良好的支持
- 减少复杂配置文件的同时亦能享受到springIoC容器提供的功能



- **@SpringBootApplication：**申明让spring boot自动给程序进行必要的配置，这个配置等同于：@Configuration ，@EnableAutoConfiguration 和 @ComponentScan 三个配置。



**请求：**

- **@ResponseBody：**表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用，**用于构建RESTful的api**。在使用@RequestMapping后，返回值通常解析为跳转路径，加上@esponsebody后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。比如异步获取json数据，加上@Responsebody后，会直接返回json数据。**该注解一般会配合@RequestMapping一起使用。**

- **@Controller**：用于定义控制器类，在spring项目中**由控制器负责将用户发来的URL请求转发到对应的服务接口（service层）**，一般这个注解在类中**，通常方法需要配合注解@RequestMapping。**

- **@RestController：**用于标注控制层组件(如struts中的action)，**@ResponseBody和@Controller的合集。**

- **@RequestMapping：**提供路由信息，负责URL到Controller中的具体函数的映射。



- **@PathVariable**：获取url路径参数。参数与大括号里的名字一样要相同
- **@RequestParam**： 常用来处理简单类型的绑定，其原理是通过Request.getParameter() 获取参数值，**可以处理get 方式中参数的值，也可以处理post方式中 表单中参数的值**；**提交方式GET、POST**；注解有两个属性： **value、required**； value用来指定要传入值的id名称(即请求参数的key对应,也是表单属性的name的值对应)，required用来指示参数是否必须绑定,默认为true即请求参数必须携带该参数,不携带将报错；

~~~java
   @PostMapping("/hander/{id}")
    public Result uploadFile (
            @RequestParam("image")MultipartFile file ,
            @PathVariable Integer id) throws IOException {
        String imgUrl=null;
        User user = userService.fineByID(id) ;
        String delUrl = user.getHandUrl() ;
        String filePath = "/image" ;
        try {
            if(!delUrl.equals("http://1.116.162.146:8080/image/noface.jpg")) {
                fileUtil.deleteFile(delUrl) ;
            }
            imgUrl = fileUtil.uploadOne(filePath , file) ;
            userService.updateHandUrl(id ,imgUrl) ;
        }catch (Exception e) {
            e.printStackTrace();
        }
        return new Result(200 , imgUrl , "上传成功！" ) ;
    }
~~~



- **@RequestBody:**  适用于以POST为请求方式,请求参数以Json格式放在请求体中的前端请求

  - 在使用@RequestBody接收数据时，一般都用POST方式进行提交，并将参数放以Json的格式放在请求体中。

  - 处理的数据的请求头Content-Type:application/json, application/xml等,不能是application/x-www-form-urlencoded编码的内容；
  - 在后端接口中，@RequestBody与@RequestParam()可以同时使用，但得注意@RequestBody最多只能有一个，而@RequestParam()可以有多个。

  ~~~java
  @PutMapping("/update/{id}")
      public Result updateUserInfo( @PathVariable int id , @RequestBody JSONObject obj) {
          String username = obj.getString("username") ;
          User user = userService.fineByUsername(username) ;
          if(user != null && user.getId() != id) {
              return new Result(20000, null , "该昵称已被占用 !") ;
          }
          String cardCase = obj.getString("cardCase")  ;
          String sex = obj.getString("sex")  ;
          Integer code = userService.updateUserinfo(username , cardCase ,sex , id ) ? Code.UPDATE_OK : Code.UPDATE_ERR;
          Map map=new HashMap();
          map.put("username",username);
          map.put("cardCase",cardCase) ;
          map.put("sex" , sex) ;
          String  resObj= JSON.toJSONString(map);
          JSONObject res = JSON.parseObject(resObj);
          return new Result(code , res) ;
      }
  ~~~

  

  ~~~java
  // @RequestBody+List+json：  
  @PostMapping("/list")
      public ApiResponse method8(@RequestBody List<User> users){
          System.out.println(users);
          return users;
      }
  // @RequestBody+实体类对象+json：
  @PostMapping("/obj")
      public ApiResponse method4(@RequestBody User user){
          System.out.println(user.toString());
          return user;
      }
  ~~~

  

  

- **@Configuration：**相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件。



- **@Autowired**：自动导入依赖的bean，当加上（required=false）时，就算找不到bean也不报错。
- **@Inject**：等价于默认的@Autowired，只是没有required属性；



- **@Bean**：用@Bean标注方法等价于XML中配置的bean。

- **@Value**：注入Spring boot application.properties配置的属性的值。



- **@Service**：一般用于修饰service层的组件

- **@Component**：泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。



- **@EnableAutoConfiguration：**SpringBoot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。



- **@ComponentScan：**表示将该类自动发现扫描组件。个人理解相当于，如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。**我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入**。如果没有配置的话，Spring Boot会扫描启动类所在包下以及子包下的使用了@Service,@Repository等注解的类。



- **@Import**：用来导入其他配置类。

- **@ImportResource**：用来加载xml配置文件。



- **@Repository**：使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。



**全局异常处理：**

- **@ControllerAdvice**：包含@Component。可以被扫描到。统一处理异常。

- **@ExceptionHandler（Exception.class）**：用在方法上面表示遇到这个异常就执行以下方法。



**实体类：**

- **@Getter和@Setter（Lombok）**：注解在属性上，为属性提供 setting 方法 和 getting 方法

- **@Data：**注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法

- **@Log4j2** ：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象，和@Log4j注解类似

-  **@AllArgsConstructor**：注解在类上；为类提供一个全参的构造方法

- **@NoArgsConstructor**：注解在类上；为类提供一个无参的构造方法

- **@JsonFormat：**时间格式化注解，比如我们存储在mysql中的数据是date类型的，当我们读取出来封装在实体类中的时候，就会变成英文时间格式，而不是yyyy-MM-dd HH:mm:ss这样的中文时间，因此我们需要用到JsonFormat注解来格式化我们的时间。

  ~~~java
  // 导入依赖
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.2</version>
  </dependency>
  
      // pattern: 表示日期的格式 , timezone: 默认是GMT，中国需要GMT+8 , locale: 根据位置序列化的一种格式 
  @JsonFormat(shape =JsonFormat.Shape.STRING,pattern ="yyyy-MM-dd HH:mm:ss",timezone ="GMT+8")
      private Date createTime;
  ~~~

  

  

  





