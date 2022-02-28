###### tags: `Java`
# 尚矽谷Spring框架視頻教程（spring5源碼級講解）
## 01-尚矽谷-課程介紹、02.尚矽谷_框架概述
### IOC
控制反轉（IOC，Inverse Of Control），即把建立物件的權利交給框架，也就是指將物件的建立、物件的儲存、物件的管理交給了Spring容器。Spring容器是Spring中的一個核心模組，用於管理物件，底層可以理解為是一個Map集合。
目的：降低耦合
### AOP
面向切面編程（Aspect-Oriented Programming, AOP） 就是將那些與業務無關，卻為業務模組所共同呼叫的邏輯或責任分開封裝起來，便於減少系統的重複代碼，降低模組間的耦合度，並有利於未來的可操作性和可維護性。
### JdbcTemplate
### 事務管理
## 03.尚矽谷_入門案例
模組：
![](https://i.imgur.com/PR40yBH.png)
1. Spring套件的導入。
2. 物件及其方法建立。
3. 用xml定義欲託管的物件id與class。
4. 並以Junit測試，加載Spring配置文件、從容器中取出託管的物件。
```xml=
<bean id="user" class="com.atguigu.spring5.User"></bean>
```
```java=
ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
User user = context.getBean("user",User.class);
```
## 04.尚矽谷_IOC容器（底層原理1） ～ 06.尚矽谷_IOC容器（底層原理3）
### IOC容器
#### 底層原理
1. 物件調用:XML文件、工廠模式、反射
    1. XML定義文件，配置創建的物件。
    ```xml=
    <bean id="dao" class="com.atguigu.UserDao"></bean>
    ```
    2. 有service類和dao類，創建工廠類。
        透過工廠，取得使用反射創建的物件實體。
    ```java=
    class UserFactory {
        public static UserDao getDao() {
            String classValue = class屬性值; //XML解析
            Class clazz = Class.forName(classValue); //通過反射創建物件
            return (UserDao)clazz.newInstance();
        }
    }
    ```
#### IOC接口（BeanFactory）
1. 基於IOC容器，容器底層就是物件工廠。
2. Spring提供IOC容器兩種實現方式：（兩個接口）
    1. **BeanFactory**：IOC容器基本實現，Spring內部使用的接口，不提供開發人員進行使用。
        * 懶加載：加載配置文件的時候不會創建物件，在獲取（使用）物件時才創建。
    3. **ApplicationContext**：BeanFactory接口的子接口，提供更多更強大的功能，一般由開發人員進行使用。
        * 啟動加載：加載配置文件時就會把在配置文件的物件進行創建。
        * ApplicationContext接口有實現類：
        1. FileSystemXmlApplicationContext：加載路徑為系統路徑。
        2. ClassPathXmlApplicationContext：加載路徑為專案包內的路徑。
## 07.尚矽谷_IOC容器-Bean管理XML方式（創建對象和set注入屬性） ～ 14.尚矽谷_IOC容器-Bean管理XML方式（注入集合類型屬性2）
1. Bean管理指的是兩個操作：
    1. Spring創建物件
    2. Spring注入屬性
2. Bean管理操作的兩個方式：
    1. 基於XML配置文件
    2. 基於註解方式
### IOC操作Bean管理（基於XML方式）
1. 創建物件
    1. 在Spring配置文件中，使用bean標籤，並添加對應屬性，即可實行物件創建。
    2. 常用的屬性：
        * id：唯一的標識。不可加特殊符號。
        * class：類的全路徑（包類路徑）。
        * name：跟id用途一樣，早期用法。可以加特殊符號，為Struts1提供的。
    3. 創建物件時，默認執行無參數建構子方法來創建物件。
2. 注入屬性
    DI，依賴注入(Dependency Injection)
    1. 第一種注入方式：使用set方法進行注入。
        1. 傳統方式，創建類，定義屬性和對應的set方法。
        ```java=
        public class Book {
            //創建屬性
            private String bname;

            //創建屬性對應的set方法
            public void setBname(String bname) {
                this.bname = bname;
            }
        }
        ```
        2. Spring方式：set方法注入屬性。
        ```xml=
        <bean id="book" class="com.atguigu.spring5.Book">
            <!--使用property完成屬性注入
                name：類裡面屬性名稱
                value：向屬性注入的值
            -->
            <property name="bname" value="Hello"></property>
        </bean>
        ```
    2. 第二種注入方式：使用有參數建構子。
        1. 傳統方式：創建類，定義屬性和對應的有參數建構子方法。
        ```java=
        public class Orders {
          //屬性
          private String oname;
          private String address;
          //有參數建構子
          public Orders(String oname,String address) {
              this.oname = oname;
              this.address = address;
          }
        }
        ```
        2. Spring方式：有參數建構子注入屬性
        ```xml=
        <bean id="orders" class="com.atguigu.spring5.Orders">
            <constructor-arg name="oname" value="Hello"></constructor-arg>
            <constructor-arg name="address" value="World"></constructor-arg>
        </bean>
        ```
    3. p名稱空間（或叫命名空間）注入（瞭解即可）
        1. 使用p名稱空間注入，可以簡化基於XML的配置方式。
            1. 添加p名稱空間在配置文件上方：xmlns:p
            ```xml=
            <?xml version="1.0" encoding="UTF-8"?>
            <beans xmlns="http://www.springframework.org/schema/beans"
                   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                   xmlns:p="http://www.springframework.org/schema/p"
            ```
        2. 進行屬性注入，在bean標籤裡面進行操作。類似簡化版的set注入方式。
        ```xml=
        <bean id="book" class="com.atguigu.spring5.Book" p:bname="book" p:bauthor="you">
        </bean>
        ```
    4. XML注入其他類型屬性
        1. null值
        ```xml=
        <property name="address">
            <null/>
        </property>
        ```
        3. 特殊符號
            * 轉譯。例如：把<>進行轉義 &lt; &gt;
            * 把帶特殊符號內容寫到CDATA
        ```xml=
        <property name="address">
            <value><![CDATA[<<南極>>]]></value>
        </property>
        ```
    5. 注入屬性 - 外部bean
        1. 創建兩個類，service類和dao類。
        2. 在service類調用dao裡面的方法。
        3. 在Spring配置文件中進行配置。
        ```xml=
        <bean id="userService" class="com.atguigu.spring5.service.UserService">
            <!--注入userDao物件
                name屬性：類裡面的屬性名稱
                ref屬性：創建userDao物件的bean標籤id值-->
            <property name="userDao" ref="userDaoImpl"></property>
        </bean>
        <bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
        ```
    6. 注入屬性 - 內部bean
        1. 一對多關係：部門和員工
            一個部門有多個員工，一個員工屬於一個部門（部門是一，員工是多）
        2. 在實體類之間表示一對多關係，員工表示所屬部門，使用物件類型屬性進行表示
        ```java=
        //部門類
        public class Dept {
            private String dname;
            public void setDname(String dname) {
                this.dname = dname;
            }
        }

        //員工類
        public class Emp {
            private String ename;
            private String gender;
            //員工屬於某一個部門，使用物件形式表示
            private Dept dept;

            public void setDept(Dept dept) {
                this.dept = dept;
            }
            public void setEname(String ename) {
                this.ename = ename;
            }
            public void setGender(String gender) {
                this.gender = gender;
            }
        }
        ```
        3. Spring文件配置
        ```xml=
        <!--內部bean-->
        <bean id="emp" class="com.atguigu.spring5.bean.Emp">
            <property name="ename" value="Mark"></property>
            <property name="gender" value="男"></property>
            <!--設置物件類型屬性-->
            <property name="dept">
                <bean id="dept" class="com.atguigu.spring5.bean.Dept">
                    <!--內部bean賦值-->
                    <property name="dname" value="人資部"></property>
                </bean>
            </property>
        </bean>
        ```
    7. 注入屬性 - 級聯賦值
        1. 第一種寫法
        ```xml=
        <bean id="emp" class="com.atguigu.spring5.bean.Emp">
            <property name="ename" value="Mark"></property>
            <property name="gender" value="男"></property>
            <!--級聯賦值-->
            <property name="dept" ref="dept"></property>
        </bean>
        <bean id="dept" class="com.atguigu.spring5.bean.Dept">
            <property name="dname" value="行銷部"></property>
        </bean>
        ```
        2. 第二種寫法，執行結果會是技術部。
        ```java=
        //生成dept的get方法（必須有get方法，才能取得物件並設值。）
        public Dept getDept() {
            return dept;
        }
        ```
        ```xml=
        <bean id="emp" class="com.atguigu.spring5.bean.Emp">
            <property name="ename" value="Mark"></property>
            <property name="gender" value="男"></property>
            <!--級聯賦值。設置外部bean中的屬性值。 -->
            <property name="dept" ref="dept"></property>
            <property name="dept.dname" value="技術部"></property>
        </bean>
        <bean id="dept" class="com.atguigu.spring5.bean.Dept">
            <property name="dname" value="行銷部"></property>
        </bean>
        ```
    8. 注入集合屬性
        1. 注入數組（Array）類型屬性
        2. 注入List類型屬性
        3. 注入Map集合
            1. 創建類，定義array、list、map、set 類型屬性，生成對應 set 方法
            ```java=
            public class Stu {
            //array類型屬性
            private String[] courses;
            //list集合類型屬性
            private List<String> list;
            //map集合類型屬性
            private Map<String,String> maps;
            //set集合類型屬性
            private Set<String> sets;

            public void setSets(Set<String> sets) {
                this.sets = sets;
            }
            public void setCourses(String[] courses) {
                this.courses = courses;
            }
            public void setList(List<String> list) {
                this.list = list;
            }
            public void setMaps(Map<String, String> maps) {
                this.maps = maps;
            }
            ```
            2. 在 spring 配置文件進行配置
            ```xml=
            <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
                <!--array類型屬性注入-->
                <property name="courses">
                    <array>
                        <value>Java課程</value>
                        <value>Spring庫課程</value>
                    </array>
                </property>
                <!--list類型屬性注入-->
                <property name="list">
                    <list>
                        <value>張三</value>
                        <value>李四</value>
                    </list>
                </property>
                <!--map類型屬性注入-->
                <property name="maps">
                    <map>
                        <entry key="JAVA" value="java"></entry>
                        <entry key="PHP" value="php"></entry>
                    </map>
                </property>
                <!--set類型屬性注入-->
                <property name="sets">
                    <set>
                        <value>MySQL</value>
                        <value>Redis</value>
                    </set>
                </property>
            </bean>
            ```
        4. 在集合裡面設置物件類型的值
        ```java=
        //學生所學多門課程
        private List<Course> courseList; //創建集合
        public void setCourseList(List<Course> courseList) {
            this.courseList = courseList;
        }
        ```
        ```xml=
        <!--創建多個course物件-->
        <bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
            <property name="cname" value="Spring5框架"></property>
        </bean>
        <bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
            <property name="cname" value="MyBatis框架"></property>
        </bean>

        <!--注入list集合類型，值是外部bean物件-->
       <property name="courseList">
           <list>
               <ref bean="course1"></ref>
               <ref bean="course2"></ref>
           </list>
       </property>
        ```
        5. 把集合注入部份提取出來
        ```xml=
        <!--第一步：在 spring 配置文件中引入名稱空間 util。添加在xmlns:util與xsi:schemaLocation。-->
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:util="http://www.springframework.org/schema/util"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                   http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

            <!--第二步：使用 util 標籤完成 list 集合注入提取-->
            <!--把集合注入部分提取出來-->
            <!--1. 提取list集合類型屬性注入-->
            <util:list id="bookList">
                <value>易筋經</value>
                <value>九陰真經</value>
                <value>九陽神功</value>
            </util:list>

            <!--2. 提取list集合類型屬性注入使用-->
            <bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype">
                <property name="list" ref="bookList"></property>
            </bean>
        ```
## 15.尚矽谷_IOC容器-Bean管理（工廠bean）
1. Spring 有兩種類型bean，一種普通bean，另外一種工廠bean（FactoryBean）。
2. 普通bean：在配置文件中定義的bean類型就是返回類型。
3. 工廠bean：在配置文件定義bean類型可以和返回類型不一樣。

第一步：創建類，讓這個類作為工廠bean，實現接口FactoryBean

第二步：實現接口裡面的方法，在實現的方法中定義返回的bean類型

```java=
public class MyBean implements FactoryBean<Course> {
    //定義返回bean
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }
}
```
```xml=
<bean id="myBean" class="com.atguigu.spring5.factorybean.MyBean">
</bean>
```
```java=
@Test
public void test3() {
    ApplicationContext context =
    new ClassPathXmlApplicationContext("bean3.xml");
    Course course = context.getBean("myBean", Course.class); //返回值類型可以不是定義的bean類型
    System.out.println(course);
}
```
## 16.尚硅谷_IOC容器-Bean管理（bean的作用域）
1. 在Spring裡面，設置創建bean實例是單實例還是多實例。
2. 在Spring裡面，默認情況下，bean是單實例物件。
3. 如何設置單實例還是多實例
    1. 在spring配置文件bean標籤裡面有屬性（scope）用於設置單實例還是多實例。
    2. 2. scope屬性值：
        * singleton：默認值，表示是單實例物件。
        * prototype：表示是多實例物件，每次調用都創建一個實例。
        * request：servlet容器相關，每個http請求中建立一個新的實例。
        * session：servlet容器相關，session互斥，http請求中建立一個新的實例。

    3. singleton和prototype區別
        * 設置scope值是singleton的時候，加載spring配置文件時候就會創建單實例物件。
        * 設置scope值是prototype的時候，不是在加載spring配置文件時候創建物件，在調用getBean方法時候創建多實例物件。
## 17.尚硅谷_IOC容器-Bean管理（bean生命周期）
1. 生命週期
    1. 從物件創建到物件銷毀的過程

2. bean 生命週期
    1. 通過建構子創建bean實例（無參數建構子）
    2. 為bean的屬性設置值和對其他bean引用（調用set方法）
    3. 把bean實例傳遞bean後置處理器的方法postProcessBeforeInitialization
    4. 調用bean的初始化方法（需要進行配置初始化的方法）
    5. 把bean實例傳遞bean後置處理器的方法postProcessAfterInitialization
    6. bean可以使用了（物件獲取到了）
    7. 當容器關閉時候，調用bean的銷毀的方法（需要進行配置銷毀的方法）

    **※ 後置處理器配置，會為當前配置文件中的所有bean都添加後置處理器。**

3. 程式碼演示：
```java=
public class Orders {
    //無參數建構子
    public Orders() {
        System.out.println("第一步 執行無參數建構子創建bean實例");
    }
    private String oname;
    public void setOname(String oname) {
        this.oname = oname;
        System.out.println("第二步 調用set方法設置屬性值");
    }
    //創建執行的初始化的方法
    public void initMethod() {
        System.out.println("第四步 執行初始化的方法");
    }
    //創建執行的銷毀的方法
    public void destroyMethod() {
        System.out.println("第七步 執行銷毀的方法");
    }
}
```
```java=
public class MyBeanPost implements BeanPostProcessor {//創建後置處理器實現類
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("第三步 在初始化之前執行的方法");
        return bean;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("第五步 在初始化之後執行的方法");
        return bean;
    }
}
```
```xml=
<!--配置文件的bean參數配置-->
<bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">	<!--配置初始化方法和銷毀方法-->
    <property name="oname" value="手機"></property><!--這裡就是通過set方式（注入屬性）賦值-->
</bean>

<!--配置後置處理器-->
<bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
```
```java=
@Test
public void testBean3() {
    // ApplicationContext context =
    // new ClassPathXmlApplicationContext("bean4.xml");
    ClassPathXmlApplicationContext context =
        new ClassPathXmlApplicationContext("bean4.xml");
    Orders orders = context.getBean("orders", Orders.class);
    System.out.println("第六步 獲取創建 bean 實例對象");
    System.out.println(orders);
    //手動讓 bean 實例銷毀
    context.close();
}
```
## 18.尚矽谷_IOC容器-Bean管理XML方式（自動裝配）
1. 什麼是自動裝配（autowire）
    
    根據指定裝配規則（屬性名稱byName或屬性類型byType），Spring自動將匹配的屬性值進行注入。
2. 演示自動裝配過程：
```xml=
<!--
    實現自動裝配
    bean標籤屬性autowire，配置自動裝配
    autowire屬性常用兩個值：
    byName根據屬性名稱注入，注入值bean的id值和類屬性名稱一樣
    byType根據屬性類型注入
-->
<bean id="emp" class="com.zhi.spring5.autowire.Employee" autowire="byName">
<!--<property name="department" ref="department"></property>-->
</bean>
<bean id="dept" class="com.zhi.spring5.autowire.Dept"></bean>

```
```java=
public class Emp {
    //若byName，依據dept名稱；byType，依據Dept類型。但xml定義檔中的Dept不能有多個。
    private Dept dept;
    public void setDept(Dept dept) {
        this.dept = dept;
    }
}
```
## 19.尚矽谷_IOC容器-Bean管理XML方式（外部屬性文件）
方式一：直接配置資料庫資訊
1. 配置Druid（德魯伊）連接池。
2. 引入Druid（德魯伊）連接池依賴jar包。
```xml=
<!--直接配置連接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>

```
方式二：引入外部屬性文件配置資料庫連接池
1. 創建外部屬性文件，properties格式文件，寫資料庫資訊（jdbc.properties）。
```xml=
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/userDb
prop.userName=root
prop.password=root
```
2. 把外部properties屬性文件引入到spring配置文件中——引入context名稱空間。
```xml=
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"><!--引入context名稱空間-->
    
    <!--引入外部屬性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置連接池，用表達式，吃參數值-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
</beans>
```

## 20.尚矽谷_IOC容器-Bean管理註解方式（創建對象）
1. 什麼是註解
    1. 註解是代碼特殊標記，格式：@註解名稱(屬性名稱=屬性值, 屬性名稱=屬性值…)
    2. 使用註解，註解作用在類上面，方法上面，屬性上面
    3. 使用註解目的：簡化xml 配置

2. Spring 針對Bean 管理中創建對象提供註解

   下面四個註解功能是一樣的，都可以用來創建bean 實例
    1. @Component
    2. @Service
    3. @Controller
    4. @Repository

3. 基於註解方式實現對象創建

第一步引入依賴（引入spring-aop jar包）

第二步開啟組件掃描

```xml=
<!--開啟組件掃描
 1 如果掃描多個包，多個包使用逗號隔開
 2 掃描包上層目錄
-->
<context:component-scan base-package="com.atguigu"></context:component-scan>
```
第三步創建類，在類上面添加創建對象註解
```java=
//在註解裡面 value 屬性值可以省略不寫，
//默認值是類名稱，首字母小寫
//UserService -- userService
@Component(value = "userService") //註解等同於XML配置文件：<bean id="userService" class=".."/>
public class UserService {
    public void add() {
        System.out.println("service add.......");
    }
}
```
## 21.尚矽谷_IOC容器-Bean管理註解方式（組件掃描配置）
```xml=
<!--示例 1
 use-default-filters="false" 表示現在不使用默認 filter，自己配置 filter
 context:include-filter ，設置掃描哪些內容
-->
<context:component-scan base-package="com.atguigu" use-defaultfilters="false">
   <context:include-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/><!--代表只掃描Controller註解的類-->
</context:component-scan>

<!--示例 2
 下面配置掃描包所有內容
 context:exclude-filter： 設置哪些內容不進行掃描
-->
<context:component-scan base-package="com.atguigu">
   <context:exclude-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/><!--表示Controller註解的類之外一切都進行掃描-->
</context:component-scan>
```
## 22.尚矽谷_IOC容器-Bean管理註解方式（注入屬性@Autowired和Qualifier） ~ 23.尚矽谷_IOC容器-Bean管理註解方式（注入屬性@Resource和@Value）
1. 基於註解方式實現屬性注入

    1. @Autowired：根據屬性類型進行自動裝配
    
        第一步把service 和dao 對象創建，在service 和dao 類添加創建對象註解

        第二步在service 注入dao 對象，在service 類添加dao 類型屬性，在屬性上面使用註解
```java=
@Service
public class UserService {
    //定義 dao 類型屬性
    //不需要添加 set 方法
    //添加註入屬性註解
    @Autowired
    private UserDao userDao;
    public void add() {
        System.out.println("service add.......");
        userDao.add();
    }
}

//Dao實現類
@Repository
//@Repository(value = "userDaoImpl1")
public class UserDaoImpl implements UserDao {
    @Override
    public void add() {
        System.out.println("dao add.....");
    }
}
```

如果Spring配置了component scan，並且要注入的接口只有一個實現的話，那麼spring框架可以自動將interface於實現組裝起來。如果沒有配置component scan，那麼你必須在application-config.xml（或等同的配置文件）定義這個bean。

參考網址：[聊聊@Autowired註解注入,寫接口名字還是實現類的名字](http://www.codebaoku.com/it-java/it-java-229549.html)

2. @Qualifier：根據名稱進行注入，這個@Qualifier 註解的使用，和上面@Autowired 一起使用
```java=
//定義 dao 類型屬性
//不需要添加 set 方法
//添加註入屬性註解
@Autowired //根據類型進行注入
@Qualifier(value = "userDaoImpl1") //根據名稱進行注入（目的在於區別同一接口下有多個實現類，根據類型就無法選擇，從而出錯！）
private UserDao userDao;
```
3. @Resource：可以根據類型注入，也可以根據名稱注入（它屬於javax包下的註解，不推薦使用！）
```java=
//只有寫@Resource //根據類型進行注入
@Resource(name = "userDaoImpl1") //根據名稱進行注入
private UserDao userDao;
```
4. @Value：注入普通類型屬性
```java=
@Value(value = "abc")
private String name
```
## 24.尚矽谷_IOC容器-Bean管理註解方式（完全註解開發）
1. 創建配置類，替代xml 配置文件
```java=
@Configuration //作為配置類，替代 xml 配置文件
@ComponentScan(basePackages = {"com.atguigu"})
    public class SpringConfig {
}
```
※ 補充：亦可使用[@Configuration搭配@Bean的作法](https://matthung0807.blogspot.com/2019/04/spring-configuration_28.html)

2. 編寫測試類
```java=
@Test
public void testService2() {
    //加載配置類
    ApplicationContext context
        = new AnnotationConfigApplicationContext(SpringConfig.class);
    UserService userService = context.getBean("userService", UserService.class);
    System.out.println(userService);
    userService.add();
}
```
## 25.尚矽谷_AOP-基本概念
AOP 基本概念
1. 面向切面編程（方面），利用AOP 可以對業務邏輯的各個部分進行隔離，從而使得業務邏輯各部分之間的耦合度降低，提高程序的可重用性，同時提高了開發的效率。
2. 通俗描述：不通過修改源代碼方式，在主幹功能裡面添加新功能
3. 使用登錄例子說明AOP
![](https://i.imgur.com/vgI6f8m.png)
## 26.尚矽谷_AOP-底層原理
AOP 底層使用動態代理，動態代理有兩種情況：

第一種有接口情況，使用JDK 動態代理；創建接口實現類代理對象，增強類的方法
![](https://i.imgur.com/qmq94QW.png)

第二種沒有接口情況，使用CGLIB 動態代理；創建子類的代理對象，增強類的方法
![](https://i.imgur.com/81kweuy.png)

## 27.尚矽谷_AOP-底層原理（JDK動態代理實現）
1. 使用JDK 動態代理，使用Proxy 類裡面的方法創建代理對象

Class Proxy
    
    java.lang.Object
    java.lang.reflect.Proxy

調用newProxyInstance 方法，方法有三個參數：
```java=
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
```
第一參數，類加載器

第二參數，增強方法所在的類，這個類實現的接口，支持多個接口

第三參數，實現這個接口InvocationHandler，創建代理對象，寫增強的部分

2. 編寫JDK 動態代理代碼
```java=
//（1）創建接口，定義方法
public interface UserDao {
    public int add(int a,int b);
    public String update(String id);
}
```
```java=
//（2）創建接口實現類，實現方法
public class UserDaoImpl implements UserDao {
    @Override
    public int add(int a, int b) {
        return a+b;
    }
    @Override
    public String update(String id) {
        return id;
    }
}
```
```java=
//（3）使用 Proxy 類創建接口代理對象
public class JDKProxy {
    public static void main(String[] args) {
        //創建接口實現類代理對象
        Class[] interfaces = {UserDao.class};
        UserDaoImpl userDao = new UserDaoImpl(); 
        /** 第一參數，類加載器 
   	    第二參數，增強方法所在的類，這個類實現的接口，(支持多個接口)
   	    第三參數，實現這個接口 InvocationHandler，創建代理對象，寫增強的部分  */
        UserDao dao =(UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces,
   					new UserDaoProxy(userDao));
        int result = dao.add(1, 2);
        System.out.println("result:"+result);
    }
}

//創建代理對象代碼
class UserDaoProxy implements InvocationHandler {
    //1 把創建的是誰的代理對象，把誰傳遞過來
    //有參數構造傳遞
    private Object obj;
    public UserDaoProxy(Object obj) {
        this.obj = obj;
    }
    //增強的邏輯
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    //方法之前
        System.out.println("方法之前執行...."+method.getName()+" :傳遞的參數..."+ Arrays.toString(args));
        //被增強的方法執行
        Object res = method.invoke(obj, args);
        //方法之後
        System.out.println("方法之後執行...."+obj);
        return res;
    }
}
```
※ 補充：invoke中的Proxy
![](https://i.imgur.com/1whs3Cg.jpg)

## 28.尚矽谷_AOP-操作術語
1. 連接點：類裡面哪些方法可以被增強，這些方法稱為連接點
2. 切入點：實際被真正增強的方法稱為切入點
3. 通知（增強）：實際增強的邏輯部分稱為通知，且分為以下五種類型：
    1. 前置通知
    2. 後置通知
    3. 環繞通知
    4. 異常通知
    5. 最終通知

4. 切面：把通知應用到切入點的過程

※ 補充：

切面（Aspect）：一個關注點的模組化。以註解@Aspect的形式放在類上方，宣告一個切面。

連線點（Joinpoint）：在程式執行過程中某個特定的點，比如某方法呼叫的時候或者處理異常的時候都可以是連線點。

通知（Advice）：通知增強，需要完成的工作叫做通知，就是你寫的業務邏輯中需要比如事務、日誌等先定義好，然後需要的地方再去用。增強包括如下五個方面：

@Before：在切點之前執行

@After：在切點方法之後執行

@AfterReturning：切點方法返回後執行

@AfterThrowing：切點方法拋異常執行

@Around：屬於環繞增強，能控制切點執行前，執行後，用這個註解後，程式拋異常，會影響@AfterThrowing這個註解。

切點（Pointcut）：其實就是篩選出的連線點，匹配連線點的斷言，一個類中的所有方法都是連線點，但又不全需要，會篩選出某些作為連線點做為切點。

引入（Introduction）：在不改變一個現有類程式碼的情況下，為該類新增屬性和方法,可以在無需修改現有類的前提下，讓它們具有新的行為和狀態。其實就是把切面（也就是新方法屬性：通知定義的）用到目標類中去。

目標物件（Target Object）：被一個或者多個切面所通知的物件。也被稱做被通知（adviced）物件。既然Spring AOP是通過執行時代理實現的，這個物件永遠是一個被代理（proxied）物件。

AOP代理（AOP Proxy）：AOP框架建立的物件，用來實現切面契約（例如通知方法執行等等）。在Spring中，AOP代理可以是JDK動態代理或者CGLIB代理。

織入（Weaving）：把切面連線到其它的應用程式型別或者物件上，並建立一個被通知的物件。這些可以在編譯時（例如使用AspectJ編譯器），類載入時和執行時完成。Spring和其他純Java AOP框架一樣，在執行時完成織入。

## 29.尚矽谷_AOP操作-準備工作
1. Spring 框架一般都是基於AspectJ實現AOP操作，AspectJ不是Spring組成部分，獨立AOP框架，一般把AspectJ和Spirng框架一起使用，進行AOP操作。
2. 基於AspectJ 實現AOP 操作：

    1. 基於xml配置文件實現
    2. 基於註解方式實現（使用）
3. 引入相關jar包
4. 切入點表達式

    1. 切入點表達式作用：知道對哪個類裡面的哪個方法進行增強 
    2. 語法結構： execution([權限修飾符] [返回類型] [類全路徑] [方法名稱]\([參數列表]\) )
    3. 例子如下：
        * 例 1：對 com.atguigu.dao.BookDao 類裡面的 add 進行增強

		    execution(* com.atguigu.dao.BookDao.add(..))
 	    * 例 2：對 com.atguigu.dao.BookDao 類裡面的所有的方法進行增強

 	        execution(* com.atguigu.dao.BookDao.* (..))
        * 例 3：對 com.atguigu.dao 包裡面所有類，類裡面所有方法進行增強

		    execution(* com.atguigu.dao.*.* (..))

## 30.尚矽谷_AOP操作-AspectJ註解（1） ～ 31.尚矽谷_AOP操作-AspectJ註解（2）
1. 創建類，在類裡面定義方法
```java=
public class User {
   public void add() {
     System.out.println("add.......");
   }
}
```
2. 創建增強類（編寫增強邏輯）
    
    增強類裡面，創建方法，讓不同方法代表不同通知類型
```java=
//增強的類
public class UserProxy {
   public void before() {//前置通知
     System.out.println("before......");
   }
}
```

3. 進行通知的配置
```xml=
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 開啟註解掃描 -->
    <context:component-scan base-package="com.atguigu.spring5.aopanno"></context:component-scan>

    <!-- 開啟Aspect生成代理對象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```
```java=
//在增強的類上添加 @Aspect
@Component
@Aspect  //生成代理對象
public class UserProxy {}

//被增強的類
@Component
public class User {}
```
4. 配置不同類型的通知
```java=
@Component
@Aspect  //生成代理對象
public class UserProxy {
    //相同切入點抽取
    @Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void pointdemo() {

    }

    //前置通知
    //@Before註解表示作為前置通知
    @Before(value = "pointdemo()")//相同切入點抽取使用！
    public void before() {
        System.out.println("before.........");
    }

    //後置通知（返回通知）。返回值之後執行。
    @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterReturning() {
        System.out.println("afterReturning.........");
    }

    //最終通知。不論有無異常，方法之後執行。
    @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void after() {
        System.out.println("after.........");
    }

    //異常通知
    @AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing.........");
    }

    //環繞通知
    @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("環繞之前.........");

        //被增強的方法執行
        proceedingJoinPoint.proceed();
        System.out.println("環繞之後.........");
    }
}
```

※ 補充：

範例中的順序：

環繞之前...

before...

add...

環繞之後（afterReturning）...

after(最終通知)...

afterReturning...

可知，環繞的before跟after會先於一般的before跟after。

如果出異常（報錯），則after依然執行，afterReturning不會執行，環繞的afterReturning也不會執行，只會有afterThrowing。

5. 有多個增強類對同一個方法進行增強，設置增強類優先級

在增強類上面添加註解 @Order(數字類型值)，數字類型值越小優先級越高
```java=
@Component
@Aspect
@Order(1)
public class PersonProxy{ }
```

6. 完全使用註解開發

    創建配置類，不需要創建xml配置文件

```java=
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class ConfigAop{
    
}
```

## 32.尚矽谷_AOP操作-AspectJ配置文件
```xml=
<!--1、創建兩個類，增強類和被增強類，創建方法（同上一樣）-->
<!--2、在 spring 配置文件中創建兩個類對象-->
<!--創建對象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
<!--3、在 spring 配置文件中配置切入點-->
<!--配置 aop 增強-->
<aop:config>
   <!--切入點-->
   <aop:pointcut id="p" expression="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"/>
   <!--配置切面-->
   <aop:aspect ref="bookProxy">
       <!--增強作用在具體的方法上-->
       <aop:before method="before" pointcut-ref="p"/>
   </aop:aspect>
</aop:config>
```
## 33.尚矽谷_JdbcTemplate-概述和準備工作
1. JdbcTemplate概念及使用

    Spring框架對JDBC進行封裝，使用JdbcTemplate方便實現對數據庫操作

2. 準備工作

* 引入相關jar包

*  在spring配置文件配置數據庫連接池

```xml=
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
      destroy-method="close">
   <property name="url" value="jdbc:mysql:///test" />
   <property name="username" value="root" />
   <property name="password" value="root" />
   <property name="driverClassName" value="com.mysql.jdbc.Driver" />
</bean>
```
    
* 配置JdbcTemplate 對象，注入DataSource
    
```xml=
<!-- JdbcTemplate 對象 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
   <!--注入 dataSource-->
   <property name="dataSource" ref="dataSource"></property><!--set方式注入-->
</bean>
```

* 創建service類，創建dao類，在dao 注入jdbcTemplate對象
```xml=
<!-- 組件掃描 -->
<context:component-scan base-package="com.atguigu"></context:component-scan>
```
```java=
@Service
public class BookService {
   //注入 dao
   @Autowired
   private BookDao bookDao;
}

@Repository
public class BookDaoImpl implements BookDao {
   //注入 JdbcTemplate
   @Autowired
   private JdbcTemplate jdbcTemplate;
}
```
## 34.尚矽谷_JdbcTemplate操作數據庫-添加功能
1. 對應數據庫創建實體類
2. 創建service和dao
    1. 在dao進行數據庫添加操作
    2. 調用JdbcTemplate 對象裡面update 方法實現添加操作

        update(String sql, Object... args)
        
        有兩個參數：
        
        第一個參數，sql語句
        
        第二個參數，可變參數，設置sql中的變數值
```java=
@Repository
public class BookDaoImpl implements BookDao {
    //注入 JdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate;
    //添加的方法
    @Override
    public void add(Book book) {
        //1 創建 sql 語句
        String sql = "insert into t_book values(?,?,?)";
        //2 調用方法實現
        Object[] args = {book.getUserId(), book.getUsername(),book.getUstatus()};
        int update = jdbcTemplate.update(sql,args);
        System.out.println(update);
    }
}
```
### 35.尚矽谷_JdbcTemplate操作數據庫-修改和刪除功能
```java=
//1、修改
@Override
public void updateBook(Book book) {
    String sql = "update t_book set username=?,ustatus=? where user_id=?";
    Object[] args = {book.getUsername(), book.getUstatus(),book.getUserId()};
    int update = jdbcTemplate.update(sql, args);
    System.out.println(update);
}
//2、刪除
@Override
public void delete(String id) {
    String sql = "delete from t_book where user_id=?";
    int update = jdbcTemplate.update(sql, id);
    System.out.println(update);
}
//使用JdbcTemplate 模板所實現的 “增刪改” 都是調用了同一個 “update” 方法
```
