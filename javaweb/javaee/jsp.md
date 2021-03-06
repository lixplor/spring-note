# JSP

* JSP, Java Server Pages, 是一种动态网页开发技术
* 使用JSP标签在HTML网页中插入Java代码
* JSP是一种Java Servlet, 主要用于实现Java web应用程序的用户界面部分. 除了解释阶段外, JSP网页几乎可以被当成一个普通的Servlet对待

## 运行环境

* JDK
* 支持JSP的Web服务器, 也叫做容器, 如Tomcat, Apache


## JSP结构

* Web服务器通过JSP引擎(容器)处理JSP页面
* 容器截获对JSP页面的请求

![图示](http://www.runoob.com/wp-content/uploads/2014/01/jsp-arch.jpg)


## JSP原理

Web服务器使用JSP创建网页的步骤:
* 浏览器发送HTTP请求给Web服务器
* Web服务器识别出这是JSP网页的请求, 将该请求传递给JSP引擎. 通过URL或.jsp文件来完成
* JSP引擎从磁盘中载入JSP文件, 然后将它们转换为Servlet. 这种转换只是简单地将所有模板文本改用`println()`语句, 并且将所有的JSP元素转化为Java代码
* JSP引擎将Servlet编译为class文件, 将原始请求传递给Servlet引擎
* Web服务器的某组件将会调用Servlet引擎, 然后载入并执行Servlet类. 执行过程中, Servlet产生HTML格式的输出并将其内嵌与HTTP response中上交给Web服务器
* Web服务器以静态HTML网页的形式将HTTP response返回到浏览器中

![流程图](http://www.runoob.com/wp-content/uploads/2014/01/jsp-processing.jpg)


## JSP生命周期

JSP的生命周期和Servlet非常相似:
* 编译: Servlet容器编译Servlet源文件, 生成Servlet类
    - 当浏览器请求JSP页面时, JSP引擎会首先去检查是否需要编译这个文件. 如果这个文件没有被编译过, 或者在上次编译后背更改过, 则编译这个JSP文件
    - 编译的3个步骤:
        - 解析JSP文件
        - 将JSP文件转为Servlet
        - 编译Servlet
* 初始化: 加载与JSP对应的Servlet类, 创建其实例, 并调用其初始化方法
    - 容器载入JSP文件后, 会先调用`jspInit()`方法.
    - 初始化只执行一次
* 执行: 调用JSP对应的Servlet实例的服务方法
    - 处理请求相关的交互行为, 直到被销毁
    - JSP网页初始化完毕后, 会调用`_jspService()`方法, 该方法需要一个`HttpServletRequest`对象和一个`HttpServletResponse`对象作为参数.
    - `_jspService()`方法在每个request中被调用一次并且负责产生与之相对应的response, 并且它还负责产生所有7个HTTP方法的响应, 如GET, POST等
* 销毁: 调用与JSP对应的Servlet实例的销毁方法, 然后销毁Servlet实例
    - `jspDestroy()`是销毁的方法

![JSP生命周期](http://www.runoob.com/wp-content/uploads/2014/01/jsp_life_cycle.jpg)


## JSP语法

* `<%-- ... --%>`: JSP注释, 只在JSP源码中有, 在生成的Java类和HTML页面中没有
* `<% ... %>`: 代码片段. 会作为service()方法的局部成员
    - 示例: `<% int i= 0; Circle a = new Circle(2.0); %>`
* `<%! ... %>`: 声明. 会作为类的成员
    - 示例: `<%! int i= 0; Circle a = new Circle(2.0); %>`
* `<%= ... %>`: 表达式. 会作为out.print()的输出对象
    - 示例: `<%= i %>`, 显示i的值; `<%= new Date().toLocaleString() %>`, 显示当前时间
* `<%@ ... %>`: 指令, 用于设置JSP页面属性
    - 格式: `<%@ 指令 属性="属性值" %>`
    - 3种指令:
        - `<%@ page ... %>`: 定义页面依赖属性
            - 属性:
                - `buffer`: 指定out对象使用缓冲区的大小. 默认8K
                - `autoFlush`: 控制out对象的缓存区是否自动刷新. 默认true
                - `contentType`: 指定浏览器显示当前JSP页面的MIME类型和字符编码
                - `errorPage`: 指定当JSP页面发生异常时需要转向的错误处理页面
                - `isErrorPage`: 指定当前页面是否可以作为另一个JSP页面的错误处理页面. 默认false
                - `extends`: 指定JSP的Servlet从哪一个类继承. 默认继承HttpJspBase, 如要修改必须是HttpServlet或其子类
                - `import`: 导入要使用的Java类
                - `info`: 定义JSP页面的描述信息
                - `isThreadSafe`: 指定对JSP页面的访问是否为线程安全
                - `language`: 定义JSP页面所用的脚本语言. 默认是java
                - `pageEncoding`: 设置文件保存到磁盘以及生成Servlet文件的字符编码
                - `session`: 指定JSP页面是否可以直接使用session对象. 默认true
                - `isELIgnored`: 指定是否忽略EL表达式. 默认false
                - `isScriptingEnabled`: 确定脚本元素能否被使用
        - `<%@ include ... %>`: 包含其他文件
            - `file`: 文件相对路径url
        - `<%@ taglib ... %>`: 引入标签库的定义
            - `uri`: 标签库的URI路径
            - `prefix`: 标签库的前缀, 如`<s:scope>`

### 中文编码

```html
<!--
添加在文件头部
language="java"                            声明当前JSP页面的脚本语言为java
contentType="text/html; charset="UTF-8"    声明浏览器打开页面的字符集使用utf-8
pageEncoding="UTF-8"                       声明该JSP文件保存到磁盘时的字符集使用utf-8
-->
<%@ page language="java" contentType="text/html; charset="UTF-8" pageEncoding="UTF-8" %>
```

### JSP动作标签

* 使用xml语法结构控制Servlet引擎
* `<jsp:行为名称 属性="属性值" />`
* 预定义行为标签:
    - `<jsp:include page="被包含的资源地址" />`: 用于在当前页面中包含静态或动态资源
    - `<jsp:useBean/>`: 寻找和初始化一个JavaBean组件
    - `<jsp:setProperty/>`: 设置JavaBean组件的值
    - `<jsp:getProperty/>`: 将JavaBean组件的值插入到output中
    - `<jsp:forward page="转发页面地址"/>`: 将页面请求转发. 从一个JSP文件向另一个文件传递一个包含用户请求的request对象
        - `<jsp:param value="键" name="值">`: 转发时传递的参数键值对
    - `<jsp:plugin/>`: 用于在生成的HTML页面中包含Applet和JavaBean对象
    - `<jsp:element/>`: 动态创建一 个XML元素
    - `<jsp:attribute/>`: 定义动态创建的XML元素的属性
    - `<jsp:body/>`: 定义动态创建的XML元素的主体
    - `<jsp:text/>`: 封装模板数据
* 指令和动作标签的区别
    - 指令在编译时生效, 动作标签在请求处理阶段起作用

### 静态包含和动态包含

* 静态包含: 使用`<%@ include file="" %>`指令
* 动态包含: 使用`<jsp:include page=""/>`动作标签
* 两者区别:
    - 静态包含会将被包含的JSP代码复制到当前JSP生成的Servlet类中, 最终都是一个类
    - 动态包含会将被包含的JSP代码分别生成各自的Servlet类, 最终是多个类

### 控制流语句

* 条件判断
    - `if...else...`
    - `switch...case`
* 循环
    - `for`
    - `while`
    - `do...while`

#### if...else

```html
<% if(...) { %>
    ...
<% } else { %>
    ...
<% } %>

<% if(day == today) { %>
    <p>今天</p>
<% } else { %>
    <p>不是今天</p>
<% } %>
```

#### switch...case

```html
<%
switch(...) {
    case ...:
        ...
        break;
    default:
        ...
}
%>

<%
switch(day) {
    case 0:
        out.println("星期日");
        break;
    default:
        out.println("不知道");
}
%>
```

#### for

```html
<% for(...;...;...) { %>
    ...
<% } %>

<% for(int i = 0; i < 10; i++) { %>
    <li>这是<%= i %></li>
<% } %>
```

#### while

```html
<% while(...) { %>
    ...
<% } %>

<% while(a < 10) { %>
    <li>dsfsda</li>
<% } %>
```


## JSP的9个隐含对象

* `request`: HttpServletRequest类的实例
* `response`: HttpServletResponse类的实例
* `out`: JspWriter类的实例. 输出的流实际会通过Servlet中的`PrintWriter esponse.getWriter()`统一进行输出
* `session`: HttpSession类的实例
* `application`: ServletContext类的实例
* `config`: ServletConfig类的实例
* `pageContext`: PageContext类的实例, 可以获取JSP页面的其他隐含对象, 也可以操作4个域对象
* `page`: `this`, 就是当前JSP的Servlet的引用
* `Exception`: Exception类的实例, 代表发生错误的JSP页面中对应的异常对象


## JSP的4个域对象

参见servlet.md

## EL表达式

* 作用: 简化JSP代码, 减少`<%%>`
* 语法: `${ EL表达式 }`
* 功能
    - 从JSP的4个域对象中获取数据: 当没有该值时返回空字符串`""`而不会返回null
        - `${ pageScope.键 }`
        - `${ requestScope.键 }`
        - `${ sessionScope.键 }`
        - `${ applicationScope.键 }`
        - `$ { 键 }`: 类似于`findAttribute()`, 在四个域对象中寻找
    - 获取值的值
        - `[]`操作符: 可以操作数组, List集合, Map集合
            - `${ 键[索引] }`: 如果通过键获取的值是一个数组或List集合, 则可以直接使用索引获取值
            - `${ 键[Map的键] }`: 如果通过键获取的值是一个Map集合, 且Map的键中含有`.`, 则不能使用点操作符, 会引起歧义, 这种情况可以使用`[]`获取值
        - `.`操作符: 可以操作Map集合, 对象
            - `${ 键.Map的键 }`: 如果通过键获取的值是一个Map集合, 则可以使用`.`获取键对应的值
            - `${ 键.对象的属性 }`: 如果通过键获取的值是一个对象, 则可以使用`.`获取属性值
    - 执行运算
        - 算数运算
            - `${ i + j }`
            - 如果有不存在的值参与运算, 也不会引发错误
        - 比较运算
            - `${ i < j }`
            - `${ i lt j }`
                - `lt`: `<`
                - `gt`: `>`
                - `le`: `<=`
                - `ge`: `>=`
                - `eq`: `=`
            - `${ a < b? 1 : 0 }`: 三元运算符
            - `${ empty 变量 }`: 判断变量是否为null, 结果为布尔值
        - 逻辑运算
            - `${ a < 0 && b > 0 }`
    - 操作Web开发的常用对象(11个)
        - 4个域对象
            - `pageScope`
            - `requestScope`
            - `sessionScope`
            - `applicationScope`
        - 2个操作请求参数的对象
            - `param`: 一个键对应一个值
                - `${ param.参数的键 }`: 根据参数的键获取值
            - `paramValues`: 一个键对应多个值
                - `${ paramValues.参数的键[索引] }`: 根据参数的键获取多个值的数组, 并通过索引获取值
        - 2个操作请求头的对象
            - `header`: 一个键对应一个值
                - `${ header.头的键 }`: 根据头的键获取值
            - `headerValues`: 一个键对应多个值
                - `${ headerValues.头的键[索引] }`: 根据头的键获取多个值的数组, 并通过索引获取值
        - `initParam`: 获取全局初始化参数
            - `${ initParam.键 }`
        - `cookie`: Cookie
            - `${ cookie.键 }`
        - `pageContext`: pageContext对象
            - `${ pageContext.其他域对象 }`: 获取其他域对象
    - 调用Java的方法
* pageScope和pageContext的区别
    - pageScope只是代表域, 在JSP中只能用来获取域中的值
    - pageContext除了以上功能, 还可以获取其他域对象


## JSTL

* JSTL: JSP Standard Tag Library, JSP标准标签库
* 版本:
    - 1.0: 不支持EL表达式
    - 1.1, 1.2: 支持EL表达式
* 标签库中的5类标签
    - core: 核心标签
        - `<c:set var="键" value="值" scope="域"></c:set>`: 将键值对设置到域中
        - `<c:out value="值" default="默认值" escapeXml="是否转义XML或HTML标签字符"></c:out>`: 输出值
        - `<c:if test="EL关系表达式">条件成立时的代码</c:if>`: 判断
        - `<c:forEach></c:forEach>`: 遍历数组, List, Map
            - 获取索引: `<c:forEach var="i" begin="开始值" end="结束值" step="每次递增量" varStatus="遍历状态对象变量名"></c:forEach>`
                - 可以使用`varStatus`对象的2个属性:
                    - `count`: 序号, 从1开始
                    - `index`: 索引, 从0开始
            - 遍历数组或List: `<c:forEach var="遍历出的元素变量" items="被遍历的数组或集合"></c:forEach>`
            - 遍历Map: `<c:forEach var="entry" items="被遍历的Map"></c:forEach>`, 然后通过`entry.key`获取键, `entry.value`获取值
    - fn: 函数标签, 包含EL函数库. 可以在EL表达式中直接使用函数, 而不用写成标签
        - `${ fn:contains("字符串", "子字符串") }`: 判断字符串中是否包含子字符串
        - `${ fn:length("字符串") }`: 获取字符串的长度
        - `${ fn:toLowerCase("字符串") }`: 将字符串转为小写字母
        - `${ fn:split("被拆的字符串", "标记") }`: 切分字符串
    - fmt: 国际化标签
    - xml: xml标签
    - sql: sql标签
* 使用JSTL:
    - 导入以下依赖包:
        - `jstl.jar`
        - `standard.jar`
    - 在JSP页面中引入JSTL标签库
        - 示例:
            - 引入核心库, `<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>`
            - 引入函数库, `<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>`
    - 在JSP标签中使用JSTL的标签
        - `<标签前缀:标签 .../>`
