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
        懶加載：加載配置文件的時候不會創建物件，在獲取（使用）物件時才創建。
    2. **ApplicationContext**：BeanFactory接口的子接口，提供更多更強大的功能，一般由開發人員進行使用。
        啟動加載：加載配置文件時就會把在配置文件的物件進行創建。
        ApplicationContext接口有實現類：
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
          //有參數構造
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
    3. p名稱空間注入（瞭解即可）
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
            <!--注入userDao對象
                name屬性：類裡面的屬性名稱
                ref屬性：創建userDao對象的bean標籤id值-->
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
            //員工屬於某一個部門，使用對象形式表示
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
        2. 第二種寫法
        執行結果會是技術部。
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
    

