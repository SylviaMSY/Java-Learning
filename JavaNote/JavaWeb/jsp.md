JSP：服务端语言

常见状态码

200：响应正常

300/301：页面重定向（跳转）

404：资源不存在

403：权限不足

500：服务器内部错误（代码有误）



#### JSP的执行流程

第一次访问：服务端将jsp翻译成java（servlet文件），再将java编译成class文件。

第二次访问：直接访问class文件

jsp和servlet可以相互转换



WEB-INF中的文件，无法通过客户端直接访问，只能通过请求转发

1、统一字符集编码

- 编码分类：
  - jsp文件的编码（jsp文件中的pageEncoding属性）：jsp->java
  - 浏览器读取jsp文件的编码（jsp文件中content属性）：一般设置成一样，推荐使用UTF-8
- 设置方式
  - 将整个eclipse中的文件设置成统一编码
  - 设置某个项目
  - 设置单独文件



## JSP页面元素及request请求

1. 脚本Scriptlet

   ``` jsp
   <%! 全局变量、定义方法  %>
   <%  局部变量、java语句  %>
   <%= 输出表达式  %> //等价out.print。可以直接解析html代码
   ```

2. 指令：page

   ``` jsp
   <%@ page language="java" contentType="text/html;charset=UTF-8"
       pageEncoding="UTF-8" import="java.util.Date" %>
   ```

3. 注释

   - html注释：`<!--  -->`
   - java注释：`//`、`/*  */`
   - jsp注释：`<%-- --%>`

## JSP9大内置对象（不需要new）

#### out：输出对象，向客户端输出内容

pageContext：

#### request：请求对象，向服务端发送的请求信息

1. 提供的方法

   ``` java
   String getParameter(String name) //根据请求的字段名key（input标签的name属性），返回字段值value
   String[] getParameterValues(String name) //根据请求的字段名key，返回多个值values
   void setCharacterEncoding("编码个税utf-8") //设置请求编码（post方式）
   String getRequestDispatcher("b.jsp").forward(request,response) //请求转发的方式跳转页面
   String getServerContext() //获取项目的ServerContext对象
   ```

2. 示例代码

   ``` jsp
   <%-- 1. register.jsp 注册页面 --%>
   <html>
   <head>
   <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
   <title></title>
   </head>
       <body>
           <form action="show.jsp" method="get"> //默认get方式提交
               用户名：<input type="text" name="uname" /><br/>
               密码：<input type="password" name="upwd"/><br/>
               年龄：<input type="text" name="uage"/><br/>
               爱好：
               <input type="checkbox" name="uhobbies" value="足球"/>足球
               <input type="checkbox" name="uhobbies" value="蓝球"/>篮球
               <input type="checkbox" name="uhobbies" value="乒乓球"/>乒乓球<br/>
               <input type="submit" value="注册">
           </form>
       </body>
   </html>
   
   <%-- 2. show.jsp 展示页面 --%>
   <body>
       <%
       //设置编码
       request.setCharacterEncoding("utf-8");//post方式
       String name = request.getParameter("uname");
       int age = Integer.parseInt(request.getParameter("uage"));
       String pwd = request.getParameter("upwd");
       String[] hobbies = request.getParameterValues("uhobbies");
       %>
       注册成功，信息如下：<br/>
       姓名：<%=name %>
       年龄：<%=age %>
       密码：<%=pwd %>
       爱好：<br/>
       <%
       if(!hobbies==0){//非空判断，不然页面会报错
           for(String hobby:hobbies){
               out.print(hobby+"&nbsp;");
           }
       }
       %>
   </body>
   ```



#### response：响应对象

1. 提供的方法

   ``` java
   void addCookie(Cookie cookie) //服务端向客户端增加cookie对象
   void sendRedirect(String location) throws IOException //重定向，页面跳转的方式之一
   void setContentType(String type) //设置服务端响应的编码
   ```

2. 示例代码

   ``` jsp
   <%-- 1.login.jsp 登录页面 --%>
   <body>
       <form action="check.jsp" method="post"> 
           用户名：<input type="text" name="uname" /><br/>
           密码：<input type="password" name="upwd"/><br/>
       </form>
   </body>
   
   <%-- 2.check.jsp 校验页面 --%>
   <body>
       <%
       request.setCharacterEncoding("utf-8");
       String name = request.getParameter("uname");
       String pwd = request.getParameter("upwd");
       if(name.equals("zs"))&&pwd.equals("abc")){//假设用户名密码
           //页面跳转：重定向方式（地址栏改变，数据会丢失）
           response.sendRedirect("success.jsp");
           //页面跳转：请求转发方式（可以取到数据，地址栏没有变）
           request.getRequestDispatcher("success.jsp").forward(request,response);
       }else{
           //登陆失败
           out.print("用户名或密码错误")
       }
       %>
   </body>
   
   <%-- 3. success.jsp 成功页面 --%>
   <body>
       登录成功<br/>
       欢迎：<%=%>
       <%
       	String name = request.getParameter("uname");
       	out.print(name);
       %>
   </body>
   ```

3. 请求转发与重定向

   |                        | 请求转发：request.getRequestDispatcher().forward() | 重定向：response.sendRedirect() |
   | ---------------------- | -------------------------------------------------- | ------------------------------- |
   | 客户端请求次数         | 1                                                  | 2                               |
   | 地址栏是否改变         | 不变                                               | 变                              |
   | 跳转发生的位置         | 服务端                                             | 客户端                          |
   | 第一次请求数据是否保留 | 保留                                               | 不保留                          |

   

#### session：会话（服务端对象，内置对象）

1. cookie
   - （客户端对象，不是内置对象，是服务端产生发送给客户端保存）
   - 提高访问服务端的效率，但安全性较差
   - key=value：键值对形式

2. 服务端发送cookie给客户端

   - 创建cookie对象：response.addCookie(Cookie cookie)

   - 页面跳转（转发，重定向）

   - 客户端获取cookie：request.getCookies()（只能一次获取所有cookie对象）

     ``` jsp
     <body>
         <%
         	//服务端创建cookie
         	Cookie cookie1 = new Cookie("name","zs");
         	Cookie cookie2 = new Cookie("pwd","abc");
         	response.addCookie(cookie1);
         	response.addCookie(cookie2);
         
         	//页面跳转到客户端
         	response.sendRedirect("result.jsp");
         %>
     </body>
     
     <body>
         <%
         	//客户端获取cookie
         	Cookie[] cookies = request.getCookies();
         	for(Cookie cookie:cookies){
                 out.print(cookie);
             }
         %>  
     </body>
     ```

3. session执行机制

#### application：

