本周阅读**Database-System-Concepts-7th-Edition**:PART FOUR BIG DATA ANALYTICS,知识点总结如下：  
**Chapter 10 Big Data**(p467-516)    
>日期：4.22

## 9.1 应用程序和用户界面
### 应用程序交互数据库方式
多数用户通过应用程序而非查询语言与数据库交互。应用程序前端提供用户界面，后端连接数据库。它接收用户输入（通常通过表单界面），根据输入向数据库录入或提取信息，再将结果展示给用户。比如大学注册系统，先验证用户身份（用户名和密码），再从数据库提取信息展示，还提供注册课程和查询信息等功能。

### 应用程序架构发展
 - **早期架构问题**：早期应用程序如航空预订系统，在大型机上运行，用户通过终端交互。个人电脑发展带来图形用户界面（GUI）的数据库应用程序，采用客户端 - 服务器架构。但这种架构存在弊端，一是用户机器可直接访问数据库，有安全风险；二是应用程序或数据库更新时，需更新每台客户端机器上的应用副本。
 - **解决方法**：
    - **基于Web浏览器的前端**：Web浏览器提供通用前端，使用HTML标准，支持信息格式化展示和表单界面创建，与操作系统和浏览器无关，几乎每台计算机都安装浏览器，基于Web的应用程序可从任何联网计算机访问，无需在客户端安装特定软件。复杂用户界面常通过JavaScript构建，JavaScript程序可在安全模式下运行，无需在用户计算机显式安装软件。浏览器请求发送到Web服务器，服务器执行应用程序处理请求，后端应用程序创建技术有Java servlet、JSP、ASP、PHP、Python等。
    - **设备端应用程序**：应用程序安装在移动设备等终端设备上，通过API与后端应用通信，不直接访问数据库。后端提供用户认证等服务，确保用户只能访问授权服务。此方式用于移动应用，一是为适配移动设备小屏幕定制显示，二是便于在设备连接高速网络时下载或更新较大的应用代码。

## 9.2 Web基础
### 9.2.1 统一资源定位符（URL）
 - **定义**：URL是Web上可访问文档的全球唯一名称。例如 `http://www.acm.org/sigmod` ，“http”表示通过超文本传输协议（HTTP）访问文档，“https”表示使用安全版本的HTTP协议；第二部分是有Web服务器的机器名称；其余部分是机器上文件的路径名或文档的唯一标识符。
 - **功能扩展**：URL可包含Web服务器上程序的标识符及程序参数。如 `https://www.google.com/search?q=silberschatz` ，表示服务器执行 `search` 程序，参数为 `q=silberschatz` ，程序返回HTML文档给服务器，再发送到前端。

### 9.2.2 超文本标记语言（HTML）
 - **表格表示**：HTML用标签描述网页内容结构。如用 `<html>` 标签包裹整个页面，`<body>` 标签包裹页面主体内容，`<table>` 标签定义表格，`<tr>` 标签定义表格行，`<th>` 标签定义表头单元格，`<td>` 标签定义普通单元格。
 - **表单创建**：通过 `<form>` 标签创建表单，可设置 `action` 属性指定表单提交时调用的URL（相对页面URL），`method` 属性指定提交方式（如 `get` ）。`<select>` 标签创建下拉菜单，`<input>` 标签创建输入框（`type="text"` 为文本输入框，`type="submit"` 为提交按钮 ）。`get` 方法将用户输入值编码为URL一部分发送到Web服务器。

### 9.2.2 超文本标记语言（HTML）补充
- **文件选择**：使用 `<input type="file", name="xyz">` 标签允许用户选择文件，并将文件名保存在表单属性 `xyz` 中。
- **输入限制**：HTML5 对多种输入类型提供输入限制（约束），如最小值、最大值、正则表达式格式匹配等。例如 `<input type="number" name="start" min="0" max="55" step="5" value="0">` ，允许用户选择 0、5、10、15 等直至 55 的值，默认值为 0 。
- **表单提交方法**：
    - **get方法**：将用户输入值编码为URL一部分发送到Web服务器。如 `https://www.google.com/search?q=silberschatz` ，`q` 为参数名，`silberschatz` 为参数值。
    - **post方法**：向Web服务器发送URL请求，并将参数值作为HTTP协议交换的一部分发送，而不是编码在URL中。
- **HTML编辑工具**：除了用纯文本编辑器创建HTML代码，还有许多编辑器支持通过图形界面直接创建HTML文本，可从菜单选择插入表单、菜单、表格等结构，无需手动输入代码。
- **样式表**：HTML支持样式表，可改变HTML格式化结构的默认显示定义以及页面背景颜色等其他显示属性。级联样式表（CSS）标准允许同一样式表用于多个HTML文档，使网站所有页面具有独特且统一的外观。
- **HTML5输入类型**：2014年发布的HTML5标准提供多种表单输入类型，如日期选择 `<input type="date" name="abc">` 和时间选择 `<input type="time" name="xyz">` ，浏览器通常会显示图形化的日期或时间选择器，输入值保存在表单属性中，还可使用 `min` 和 `max` 属性指定可选值的最小值和最大值。 

### 9.2.3 Web服务器和会话
- **Web服务器**：运行在服务器机器上的程序，接受来自Web浏览器的请求，并以HTML文档形式返回结果。浏览器和Web服务器通过HTTP通信。Web服务器除了简单传输文档外，还能执行用户提供参数的程序，并将结果以HTML文档返回。通用网关接口（CGI）标准定义了Web服务器与应用程序的通信方式，应用程序通常通过ODBC、JDBC或其他协议与数据库服务器通信以获取或存储数据。
- **Web应用架构**：
    - **三层架构**：包含Web服务器、应用服务器和数据库服务器。使用多层服务器会增加系统开销，CGI接口为每个请求启动新进程，开销更大。
    - **两层架构**：将Web服务器和应用服务器合并为单个服务器，减少系统开销，是目前大多数Web应用采用的架构。
- **HTTP连接特性**：HTTP是无连接协议，客户端和Web服务器之间没有连续连接。Web服务器收到请求时临时创建连接发送请求并接收响应，之后连接可能关闭，下一个请求通过新连接发送。这是因为多数计算机对同时连接数有限制，无连接协议可在请求满足后立即断开连接，为其他请求腾出连接资源。
- **会话管理**：多数Web应用需要会话信息实现有意义的用户交互，如限制信息访问需对用户进行认证，且会话中后续交互无需重新认证。为在连接关闭情况下实现会话，需在客户端存储额外信息并在会话的每个请求中返回，服务器用此信息识别请求是否属于用户会话，同时服务器也需维护会话相关额外信息。
    - **Cookie机制**：通常在客户端以Cookie形式维护额外信息，Cookie是包含识别信息和关联名称的小文本。例如google.com可能设置名为 `prefs` 的Cookie，编码用户设置的偏好（如首选语言、每页显示结果数）。应用程序可生成会话标识符（通常是未使用的随机数），通过名为（如）`sessionid` 的Cookie发送给客户端，服务器也本地存储会话标识符。当请求到来时，应用服务器向客户端请求 `sessionid` 的Cookie，根据其值判断请求是否属于当前会话。对于安全性要求高的应用，通常在验证用户（如提交有效用户名和密码）后设置Cookie；对于安全性要求不高的应用（如新闻网站），Cookie可永久存储在浏览器和服务器，用于识别用户后续访问，无需重新输入识别信息。对于高安全要求应用，服务器可在超时或用户注销（如点击注销按钮提交注销表单使当前会话失效）时使会话无效，即从应用服务器的活动会话列表中删除会话标识符。

### 9.3 Servlets
- **Servlet定义**：Java servlet规范定义了Web/应用服务器与应用程序之间通信的应用程序编程接口。Java中的 `HttpServlet` 类实现了servlet API规范，用于实现特定功能的servlet类定义为该类的子类。通常，servlet指实现servlet接口的Java程序（和类）。
- **Servlet加载与任务**：servlet代码在服务器启动时，或服务器收到执行特定servlet的远程HTTP请求时加载到Web/应用服务器中。servlet的任务是处理此类请求，可能涉及访问数据库检索必要信息，并动态生成要返回给客户端浏览器的HTML页面。
- **Servlet示例**：servlet常用于生成对HTTP请求的动态响应。它们可以访问通过HTML表单提供的输入，应用“业务逻辑”决定提供什么响应，然后生成要发送回浏览器的HTML输出。例如 `PersonQueryServlet` 用于处理图9.3中表单（`action="PersonQuery"` ）的请求，Web/应用服务器需配置使用该servlet处理 `PersonQuery` 请求 ，在代码中通过 `@WebServlet("PersonQuery")` 注解指定 。在 `doGet` 方法中，获取表单参数（如 `personetype` 和 `name` ），根据参数值执行相应逻辑（如查询学生或教师信息，通过JDBC与数据库通信 ），并将结果转换为HTML格式输出返回给浏览器。  

### 9.3.2 Servlet会话
- **浏览器与服务器交互特性**：浏览器和Web/应用服务器之间的交互是无状态的，即每次浏览器向服务器发出请求时，都需连接服务器、请求信息，然后断开连接。
- **会话跟踪机制**：
    - **Cookie局限性**：Cookie可用于识别请求是否来自同一浏览器会话，但它是一种底层机制，程序员需要更好的抽象来处理会话。
    - **Servlet API会话跟踪**：servlet API提供了跟踪会话和存储相关信息的方法。通过调用 `HttpServletRequest` 类的 `getSession(false)` 方法检索与发送请求的浏览器对应的 `HttpSession` 对象。若参数为 `true` ，表示如果请求是新请求，则必须创建新的会话对象。调用 `getSession()` 方法时，服务器首先要求客户端返回指定名称的Cookie。若客户端没有该名称的Cookie，或返回值与正在进行的会话不匹配，则该请求不属于正在进行的会话，`getSession()` 将返回 `null` ，servlet可将用户定向到登录页面。
- **用户认证与会话创建**：登录页面可让用户提供用户名和密码，对应的servlet可验证密码是否匹配（如通过用户名从数据库检索密码并进行比对 ）。若用户通过认证，登录servlet将执行 `getSession(true)` ，这将返回一个新的会话对象。服务器会在内部执行以下任务：在客户端浏览器设置名为（如）`sessionId` 的Cookie，其关联值为会话标识符；创建新的会话对象，并将会话标识符值与会话对象关联。
- **会话参数存储与检索**：servlet代码可在 `HttpSession` 对象中存储和查找（属性名，值）对，以在会话中的多个请求之间维护状态。例如，用户认证且会话对象创建后，登录servlet可通过执行 `session.setAttribute("userid", userid)` 将用户的 `userid` 存储为会话参数；后续可通过 `session.getAttribute("userid")` 检索会话参数，若属性未设置，该函数将返回 `null` ，表明客户端用户未通过认证。

### 9.3.3 Servlet生命周期
- **实例创建与初始化**：servlet的生命周期由部署它的Web/应用服务器控制。当客户端请求特定servlet时，服务器首先检查servlet实例是否存在。若不存在，服务器将servlet类加载到Java虚拟机（JVM）中，并创建servlet类的实例，同时调用 `init()` 方法初始化servlet实例，每个servlet实例在加载时仅初始化一次。
- **请求处理**：确保servlet实例存在后，服务器调用servlet的 `service` 方法，将请求对象和响应对象作为参数传递。默认情况下，服务器创建新线程来执行 `service` 方法，因此对servlet的多个请求可并行执行，无需等待先前请求完成，`service` 方法会根据情况调用 `doGet` 或 `doPost` 方法。 
- **销毁**：当不再需要servlet时，可通过调用 `destroy()` 方法关闭它。服务器可设置为在超时时间内没有对servlet的请求时自动关闭servlet，超时时间是可根据应用适当设置的服务器参数。

### 9.3.4 应用服务器
- **内置支持Servlet的应用服务器**：许多应用服务器提供对servlet的内置支持，其中流行的有Apache Jakarta项目的Tomcat Server，其他支持servlet的应用服务器包括Glassfish、JBoss、BEA Weblogic Application Server、Oracle Application Server和IBM的WebSphere Application Server。
- **开发方式**：开发servlet应用的最佳方式是使用如Eclipse或NetBeans等集成开发环境（IDE），它们内置Tomcat或Glassfish服务器。
- **提供的服务**：应用服务器除了基本的servlet支持外，通常还提供各种有用的服务，如允许部署或停止应用程序，提供监控应用服务器状态（包括性能统计信息）的功能。许多应用服务器还支持Java 2 Enterprise Edition（J2EE）平台，为处理对象、跨多个应用服务器的并行处理等各种任务提供支持和API。

### 9.4 替代的服务器端框架
#### 9.4.1 服务器端脚本
- **优势**：使用Java或C等编程语言编写简单的Web应用程序耗时，需要大量代码行且要求程序员熟悉语言细节。服务器端脚本提供了一种更简单的创建应用程序的方法，脚本语言提供可嵌入HTML文档的结构。
- **工作原理**：在服务器端脚本中，在交付网页之前，服务器执行嵌入在页面HTML内容中的脚本。每个脚本执行时可生成添加到页面的文本（甚至可删除页面内容），脚本的源代码从页面中删除，因此客户端可能甚至不知道页面最初包含代码。执行的脚本还可能包含针对数据库执行的SQL代码。许多这样的语言及其库和工具共同构成了Web应用程序开发框架。
- **常用脚本框架**：广泛使用的脚本框架包括Java Server Pages（JSP）、微软的ASP.NET、PHP和Ruby on Rails 。这些框架允许用Java、C#、VBScript和Ruby等语言编写的代码嵌入HTML页面或从HTML页面调用。

#### 9.4.1.1 Java Server Pages
- **功能**：Java Server Pages（JSP）是一种脚本语言，允许HTML程序员将静态HTML与动态生成的HTML混合。对于许多动态网页，大部分内容是静态的，动态内容（如基于表单参数生成的内容）通常是页面的一小部分。编写servlet代码创建此类页面会导致大量HTML被编码为Java字符串，而JSP允许将Java代码嵌入静态HTML中，嵌入的Java代码生成页面的动态部分。JSP脚本实际上会被翻译成servlet代码，然后进行编译，应用程序员无需编写大量Java代码来创建servlet。
- **代码示例**：JSP页面中的Java代码用 `<%... %>` 与周围的HTML代码区分开来。例如在图9.9的JSP代码中，使用 `request.getParameter()` 获取参数 `name` 的值 。当浏览器请求JSP页面时，应用服务器从页面生成HTML输出并发送到浏览器，JSP页面的HTML部分按原样输出，嵌入 `<%... %>` 中的Java代码在HTML输出中被其打印到对象 `out` 的文本替换。

### 9.4.1.2 PHP
- **数组与函数**：在PHP中，`$_REQUEST` 数组包含请求参数，该数组通过参数名进行索引，PHP数组可以用任意字符串索引，而不仅是数字。`isset` 函数用于检查数组元素是否已初始化，`echo` 函数用于将参数打印到输出的HTML中，“.” 操作符用于连接两个字符串。
- **文件处理**：配置合适的Web服务器会将以“.php” 结尾的文件解释为PHP文件。当请求此类文件时，Web服务器会以类似于处理JSP文件的方式对其进行处理，并将生成的HTML返回给浏览器。
- **数据库访问库**：PHP语言有许多可用的库，包括使用ODBC（类似于Java中的JDBC ）进行数据库访问的库。
- **代码示例**：以下PHP代码实现了与图9.9中JSP代码相同的功能。
```html
<html>
<head> <title> Hello </title> </head>
<body>
<?php if (!isset($_REQUEST['name'])) 
   { echo 'Hello World'; }
   else { echo 'Hello, '. $_REQUEST['name']; }
?>
</body>
</html>
```

### 9.4.2 Web应用框架
- **提供的功能**：
    - 支持HTML和HTTP特性（包括会话）的函数库。
    - 模板脚本系统。
    - 控制器：将用户交互事件（如表单提交）映射到处理该事件的适当函数，还管理认证和会话，部分框架还提供管理授权的工具。 
    - 以（相对）声明性的方式指定带有用户输入验证约束的表单，系统据此生成HTML和Javascript/Ajax代码来实现该表单。 
    - 具有对象 - 关系映射的面向对象模型，用于将数据存储在关系数据库中。
- **常用框架**：有大量基于不同语言的此类框架，一些广泛使用的框架包括用于Python语言的Django框架、支持Ruby编程语言Rails框架的Ruby on Rails ，以及基于Java/JSP的Apache Struts、Swing、Tapestry和WebObjects等。许多框架还便于创建简单的CRUD（创建、读取、更新和删除）Web接口，通过从对象模型或数据库生成代码来支持对对象/元组的操作，生成的代码可编辑以构建更复杂的Web接口。

### 9.4.3 Django框架
- **视图功能**：Django中的视图是与Java中的servlet等效的函数。Django需要一个映射，通常在 `urls.py` 文件中指定，将URL映射到Django视图。当Django应用服务器收到HTTP请求时，它使用URL映射来决定调用哪个视图函数。
- **代码示例**：以下是实现人员查询任务的Django视图代码示例。
```python
from django.http import HttpResponse
from django.db import connection

def result_set_to_html(headers, cursor):
    html = "<table border=1>"
    html += "<tr>"
    for header in headers:
        html += "<th>" + header + "</th>"
    html += "</tr>"
    for row in cursor.fetchall():
        html += "<tr>"
        for col in row:
            html += "<td>" + col + "</td>"
        html += "</tr>"
    html += "</table>"
    return html

def person_query_view(request):
    if "username" not in request.session:
        return login_view(request)
    persontype = request.GET.get("persontype")
    personname = request.GET.get("personname")
    if persontype == "student":
        query_tmpl = "select id, name, dept_name from student where name=%s"
    else:
        query_tmpl = "select id, name, dept_name from instructor where name=%s"
    with connection.cursor() as cursor:
        cursor.execute(query_tmpl, [personname])
        headers = ["ID", "Name", "Department Name"]
        return HttpResponse(result_set_to_html(headers, cursor))
```
- **功能支持**：Django还支持创建HTML表单、验证表单中输入的数据、简化认证检查的注解，以及创建HTML页面的模板（与JSP页面有些类似），此外还支持对象 - 关系映射系统。

### 9.5 客户端代码与Web服务
#### 9.5.1 JavaScript
- **用途**：JavaScript是目前使用最广泛的客户端脚本语言，用于多种任务，包括验证用户输入、构建灵活的用户界面以及与Web服务交互。
- **输入验证**：用JavaScript编写的函数可用于对用户输入执行错误检查（验证），例如检查日期字符串格式是否正确，或输入值（如年龄）是否在适当范围内。这些检查在数据发送到Web服务器之前就在浏览器中进行。HTML5也可在输入标签中指定许多验证约束，如 `<input type="number" name="credits" size="2" min="1" max="15">` 确保 “credits” 参数的输入是1到15之间的数字，对于无法使用HTML5特性执行的更复杂验证，最好使用JavaScript。
- **代码示例**：以下是使用JavaScript验证表单输入的示例，该表单接受开始日期和结束日期，验证函数确保开始日期不大于结束日期。
```html
<html>
<head>
<script type="text/javascript">
function validate() {
    var startdate = new Date(document.getElementById("start").value);
    var enddate = new Date(document.getElementById("end").value);
    if(startdate > enddate) {
        alert("Start date is > end date");
        return false;
    }
}
</script>
</head>

<body>
<form action="submitDates" onsubmit="return validate()">
    Start Date: <input type="date" id="start"><br />
    End Date : <input type="date" id="end"><br />
    <input type="submit" value="Submit">
</form>
</body>
</html>
```

>日期：4.24

### 9.5.1.3 与Web服务交互
- **技术应用**：如今，JavaScript广泛用于创建动态网页，利用被统称为Ajax的多种技术。用JavaScript编写的程序可以与Web服务器异步通信（即在后台进行，不阻塞用户与Web浏览器的交互 ），并获取数据进行显示。JavaScript对象表示法（JSON）是最广泛使用的数据传输格式，不过XML等其他格式也会被使用。
- **自动完成功能示例**：以许多Web应用实现的自动完成功能为例，当用户在文本框中输入值时，系统会为正在输入的值提供补全建议。像jQuery这样的库通过将函数与文本框关联来支持自动完成功能，该函数获取文本框中的部分输入，连接到Web后端获取可能的补全内容，并将其作为自动完成的建议显示。以下是使用JavaScript和Ajax实现自动完成以及数据表格展示的代码示例：
```html
<html>
<head>
<script src="https://code.jquery.com/jquery-3.3.1.js"> </script>
<script src="https://cdn.datatables.net/1.10.19/js/jquery.dataTables.min.js"></script>
<script src="https://code.jquery.com/ui/1.12.1/jquery-ui.min.js"></script>
<script src="https://cdn.datatables.net/1.10.19/js/jquery.dataTables.min.js"></script>
<link rel="stylesheet" href="https://code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css" />
<link rel="stylesheet" href="https://cdn.datatables.net/1.10.19/css/jquery.dataTables.min.css"/>
<script>
    var myTable;
    $(document).ready(function() {
        $("#name").autocomplete({ source: "/autocomplete_name" });
        myTable = $("#personTable").DataTable({
            columns: [{data:"id"}, {data:"name"}, {data:"dept_name"}]
        });
    });
    function loadTableAsync() {
        var params = {persontype:$("#persontype").val(), name:$("#name").val()};
        var url = "/person_query_ajax?" + jQuery.param(params);
        myTable.ajax.url(url).load();
    }
</script>
</head>
<body>
Search for:
<select id="persontype">
    <option value="student" selected>Student </option>
    <option value="instructor"> Instructor </option>
</select>
Name: <input type=text size=20 id="name">
<button onclick="loadTableAsync()"> Show details </button>
<table id="personTable" border="1">
    <thead>
        <tr> <th>ID</th> <th>Name</th> <th>Dept. Name</th> </tr>
    </thead>
</table>
</body>
</html>
```
- **功能说明**：上述代码中，`$("#name").autocomplete({ source: "/autocomplete_name" });` 将自动完成功能与id为 “name” 的文本框关联，`source` 属性指定了用于获取自动完成值的Web服务。`myTable.ajax.url(url).load();` 异步从Web服务获取JSON数据并填充到HTML表格中。

### 9.5.2 Web服务
- **定义与原理**：Web服务是一种可通过Web调用的应用程序组件，其功能类似于应用程序编程接口。Web服务请求使用HTTP协议发送，在应用服务器上执行，结果返回给调用程序。
- **实现方式 - REST**：一种较简单的实现方式是表述性状态转移（REST），Web服务函数调用通过对应用服务器上URL的标准HTTP请求来执行，参数作为标准HTTP请求参数发送。应用服务器执行请求（可能涉及更新服务器上的数据库 ），生成并编码结果，最常用的结果编码是JSON表示，不过XML也会被使用。请求者解析返回的页面以访问返回的数据。许多RESTful Web服务的请求者是在Web浏览器中运行的JavaScript代码，例如在网页地图界面滚动时，JavaScript代码可使用RESTful接口获取需新显示的地图部分并展示在屏幕上。
- **使用场景与限制**：Web服务的使用方式多样，有些未公开文档，仅特定应用内部使用；有些接口有文档记录，可供任何应用使用，使用条件可能包括无限制使用、要求用户登录或要求用户/应用开发者付费等。如今有大量RESTful Web服务可用，前端应用常使用一个或多个此类服务来执行后端活动，如基于Web的邮件系统、社交媒体网页、地图服务等，移动应用也常使用Web服务来获取和更新数据。Web服务在后端也应用广泛，如基于Web的存储系统（如Amazon S3、Google Cloud Storage、Microsoft Azure ）提供用于存储和检索数据的Web服务API，还有文本转语音、语音识别、视觉等Web服务API，可让开发者轻松构建包含语音和图像识别功能的应用。
- **复杂实现方式**：一种更复杂且较少使用的方式（有时称为 “大Web服务” ）使用XML对参数和结果进行编码，使用特殊语言对Web API进行正式定义，并在HTTP协议之上构建协议层。

### 9.5.3 离线操作
- **应用需求**：许多应用希望支持客户端与应用服务器断开连接时的某些操作，例如学生在笔记本电脑断网时填写申请表，待重新联网后保存；邮件客户端作为Web应用，用户在断网时撰写邮件，联网后发送。构建此类应用需要客户端机器的本地存储。
- **HTML5支持**：HTML5标准支持本地存储，可使用JavaScript访问。以下代码检查浏览器是否支持本地存储：
```javascript
if (typeof(Storage)!== "undefined") { 
    // browser supports local storage
   ...
}
```
若浏览器支持，可使用相关函数对给定键进行存储、加载或删除值的操作。 

### 9.5.1.2 响应式用户界面
- **原理**：JavaScript最重要的优势之一是能够在浏览器中创建高度响应式的用户界面，关键在于利用JavaScript动态修改正在显示的HTML代码。浏览器将HTML代码解析为基于文档对象模型（DOM）标准的内存树结构，JavaScript代码可修改该树结构来执行特定操作。例如，用户可能需要一个表格来输入多项内容，表格可能有默认大小，但如果需要更多行，用户可以点击标有（例如）“添加项目” 的按钮，该按钮可设置为调用一个JavaScript函数，通过在表格中添加一行来修改DOM树。
- **浏览器差异与解决方案**：虽然JavaScript语言已标准化，但浏览器之间仍存在差异，特别是在DOM模型的细节方面。因此，在一个浏览器上工作的JavaScript代码可能在另一个浏览器上无法工作。为避免此类问题，最好使用JavaScript库，如jQuery库，它允许以与浏览器无关的方式编写代码。在内部，库中的函数可以检测正在使用的浏览器，并向浏览器发送适当生成的JavaScript。
- **库的功能**：像jQuery这样的JavaScript库提供了许多UI元素，如菜单、选项卡、滑块等小部件，以及自动完成等功能，可使用库函数创建和执行这些元素和功能。HTML5标准支持许多丰富用户交互的功能，包括拖放、地理定位（在用户允许的情况下提供用户位置 ）、基于位置的数据/界面定制，还支持服务器端事件（SSE），允许后端在某些事件发生时通知前端。

### 9.5.4 移动应用平台
- **主流平台及特点**：如今，移动应用（简称app）应用广泛，是大量用户的主要用户界面。目前最常用的两个移动平台是Android和iOS 。这些平台都提供了一种为小型触摸屏设备构建具有图形用户界面应用的方法，图形用户界面提供了各种标准GUI功能，如菜单、列表、按钮、复选框、进度条等，还具备显示文本、图像和视频的能力。
- **移动应用优势**：移动应用可以下载、存储并在之后使用，用户可在连接高速网络时下载应用，然后在低速网络下使用。相比之下，Web应用可能在使用时才下载，在低速网络或数据传输费用高昂时会产生大量数据传输。此外，移动应用能更好地针对小型设备进行调整，其用户界面在小型设备上表现更佳。移动应用还可编译为机器代码，功耗比Web应用低。更重要的是，与早期Web应用不同，移动应用可在本地存储数据，支持离线使用，并且拥有完善的授权模型，在用户授权后可使用位置、摄像头、联系人等信息和设备功能。
- **跨平台开发框架**：然而，为Android平台编写的代码只能在该平台运行，不能在iOS平台运行，反之亦然。因此，开发者通常需为每个应用编写两次代码，分别针对Android和iOS ，除非完全放弃其中一个平台。为解决此问题，出现了一些跨平台开发框架。基于JavaScript的React Native框架（由Facebook开发）和基于Dart语言的Flutter框架（由Google开发）旨在支持跨平台开发。这两个框架都允许大部分应用代码在Android和iOS之间通用，但在框架的平台无关部分不支持某些功能时，也可为底层平台定制特定功能。
- **渐进式Web应用（PWA）**：随着高速移动网络的普及，使用移动应用而非Web应用的一些优势（如下载备用）不再那么重要。一种结合了移动应用和Web应用优点的新型Web应用——渐进式Web应用（PWA）的使用越来越广泛。PWA使用JavaScript和HTML5构建，针对移动设备进行了优化。PWA的关键支持特性包括：
    - HTML5对本地数据存储的支持，允许应用在设备离线时使用。
    - 对JavaScript代码编译的支持，编译通常针对符合特定语法的代码进行，一般采用即时编译方式（即在代码需要执行或已多次执行时进行编译 ），通过仅使用允许编译的JavaScript特性编写Web应用中占用CPU资源较多的部分，可确保代码在移动设备上高效执行。
    - 使用HTML5服务工作线程（service workers），允许脚本在浏览器后台独立于网页运行，可用于执行本地存储与Web服务之间的后台同步操作，或接收/推送来自后端服务的通知。
    - HTML5在用户授权后允许应用获取设备位置，使PWA能够使用位置信息。PWA有望得到更广泛的应用，取代移动应用的许多（但不是全部）使用场景。

### 9.6 应用架构
- **架构分层**：为处理复杂性，大型应用通常分为多个层：
    - **表示层或用户界面层**：处理用户交互。一个应用可能有该层的多个不同版本，对应不同类型的接口，如Web浏览器和手机用户界面（屏幕尺寸差异大 ）。在许多实现中，该层基于模型 - 视图 - 控制器（MVC）架构在概念上进一步分层。模型对应业务逻辑层；视图定义数据的呈现方式，单个底层模型可根据访问应用的特定软件/设备有不同视图；控制器接收事件（用户操作），对模型执行操作，并将视图返回给用户。MVC架构在许多Web应用框架中使用。
    - **业务逻辑层**：提供数据和对数据操作的高级视图，包含业务规则和工作流。例如，在大学管理应用中，业务逻辑层可能提供学生、教师、课程等实体的抽象，以及录取学生、为学生注册课程等操作，确保满足业务规则（如学生只有完成先修课程并支付学费才能注册课程 ）。工作流描述涉及多个参与者的特定任务的处理方式（如学生申请入学的流程 ），并处理错误情况（如未在截止日期前审批时通知主管 ）。
    - **数据访问层**：提供业务逻辑层与底层数据库之间的接口。许多应用使用面向对象语言编写业务逻辑层并采用面向对象的数据模型，而底层数据库是关系数据库，此时数据访问层还需提供从业务逻辑使用的面向对象数据模型到数据库支持的关系模型的映射。
- **请求处理流程**：以Web浏览器请求为例，应用服务器接收请求后，控制器向模型发送请求，模型使用业务逻辑处理请求（可能涉及更新模型中的对象 ），然后创建结果对象。模型通过数据访问层从数据库更新或检索信息，将结果对象发送到视图模块，视图模块创建结果的HTML视图并在Web浏览器上显示，视图可根据设备特性（如屏幕大小 ）进行定制。如今，视图层越来越多地由在客户端运行的代码实现。

### 9.6.2 数据访问层与对象 - 关系映射
- **映射需求**：在最简单的情况下，若业务逻辑层与数据库使用相同的数据模型，数据访问层只需隐藏与数据库交互的细节。但当业务逻辑层使用面向对象编程语言编写时，自然会将数据建模为对象并调用对象的方法。早期实现中，程序员需编写代码从数据库获取数据创建对象，以及将更新后的对象存储回数据库，这种手动转换繁琐且容易出错。
- **解决方案**：一种解决方案是开发能原生存储对象及对象间关系，并允许像访问内存对象一样访问数据库对象的数据库系统（即面向对象数据库），但由于技术和商业原因，面向对象数据库未取得商业成功。另一种替代方法是使用传统关系数据库存储数据，同时自动化数据与按需创建的内存对象之间的映射（因内存通常不足以存储数据库中的所有数据 ），以及将更新后的对象作为关系存储回数据库的反向映射。已开发出多种系统来实现此类对象 - 关系映射，如Hibernate和Django ORM 。

### 9.6.2.1 Hibernate ORM
- **功能与原理**：Hibernate系统广泛用于将Java对象映射到关系，提供了Java持久化API（JPA）的实现。在Hibernate中，每个Java类到一个或多个关系的映射在映射文件中指定。例如，可指定名为Student的Java类映射到student关系，Java属性ID映射到student.ID属性等。数据库相关信息（如运行主机、连接用户名和密码 ）在属性文件中指定。程序需打开会话以建立与数据库的连接，会话建立后，通过调用session.save(stud)可将Java中创建的Student对象stud存储在数据库中，Hibernate代码会生成将相应数据存储在student关系所需的SQL命令。
- **关系映射支持**：在E-R模型中，实体自然对应Java等面向对象语言中的对象，但关系往往并非如此。Hibernate支持将关系映射为与对象关联的集合，例如，student和section之间的takes关系可通过将一组section与每个student关联，以及将一组student与每个section关联来建模。指定适当映射后，Hibernate会从数据库关系takes自动填充这些集合，提交时对集合的更新会反映回数据库关系。
- **代码示例**：
```java
@Entity public class Student {
    @Id String ID;
    String name;
    String department;
    int tot_cred;
}
```
上述代码定义了一个与student关系对应的Java类，精确来说，类属性应声明为private并提供getter/setter方法来访问属性（此处省略细节 ）。Student类属性到student关系属性的映射可在映射文件（XML格式 ）中指定，或更方便地通过Java代码注释指定。@Entity注释表示该类映射到数据库关系（默认关系名和属性名与类名和属性名相同，可使用@Table和@Column注释覆盖 ），@Id注释指定ID为主键属性。以下代码片段创建一个Student对象并将其保存到数据库：
```java
Session session = getSessionFactory().openSession();
Transaction txn = session.beginTransaction();
Student stud = new Student("12328", "John Smith", "Comp. Sci.", 0);
session.save(stud);
txn.commit();
session.close();
```

### 9.6.2.1 Hibernate ORM（续）
- **对象检索**：对象可以通过主键或查询进行检索，示例代码如下：
```java
Session session = getSessionFactory().openSession();
Transaction txn = session.beginTransaction();
// 通过标识符检索学生对象
Student stud1 = session.get(Student.class, "12328");
// 打印学生信息
... 
List students = session.createQuery("from Student as s order by s.ID asc").list();
for (Iterator iter = students.iterator(); iter.hasNext(); ) {
    Student stud = (Student) iter.next();
    // 打印学生信息
   ... 
}
txn.commit();
session.close();
```
通过`session.get()`方法，传入对象的类和主键可以检索单个对象。检索到的对象可以在内存中更新，当正在进行的Hibernate会话的事务提交时，Hibernate会自动通过对数据库中的关系进行相应更新来保存更新后的对象。上述代码片段还展示了Hibernate的HQL查询语言，它基于SQL设计，允许在查询中直接使用对象。HQL查询由Hibernate自动转换为SQL并执行，结果转换为Student对象列表，`for`循环遍历这些对象。
- **功能优势与直接数据库访问**：这些特性帮助程序员获得高级的数据模型，而无需关注关系存储的细节。不过，Hibernate和其他对象 - 关系映射系统一样，也允许使用SQL对存储在数据库中的关系编写查询，这种绕过对象模型的直接数据库访问对于编写复杂查询很有用。

### 9.6.2.2 Django ORM
- **模型定义**：为Python语言开发了多个对象关系映射（ORM）组件，Django框架的ORM组件是最受欢迎的之一，另一个流行的Python ORM是SQLAlchemy。图9.15展示了Django中Student和Instructor的模型定义，student和instructor的所有字段都在Student和Instructor类中定义，并指定了适当的类型。此外，advisor关系被建模为Student和Instructor之间的多对多关系，在Instructor类中通过advises属性访问，它存储一组对Student对象的引用，从Student到Instructor的反向关系自动创建，Student类中的反向关系属性名为advisors，存储一组对Instructor对象的引用。
```python
from django.db import models

class Student(models.Model):
    id = models.CharField(primary_key=True, max_length=5)
    name = models.CharField(max_length=20)
    dept_name = models.CharField(max_length=20)
    tot_cred = models.DecimalField(max_digits=3, decimal_places=0)

class Instructor(models.Model):
    id = models.CharField(primary_key=True, max_length=5)
    name = models.CharField(max_length=20)
    dept_name = models.CharField(max_length=20)
    salary = models.DecimalField(max_digits=8, decimal_places=2)
    advisees = models.ManyToManyField(Student, related_name="advisors")
```
- **数据库对象访问与迁移工具**：图9.16展示的Django视图person_query_model说明了如何直接从Python语言访问数据库对象而不使用SQL 。表达式`Student.objects.filter()`返回满足指定过滤条件的所有学生对象（在此例中是具有给定姓名的学生 ），学生姓名及其导师姓名会被打印出来。表达式`Student.advisors.all()`返回给定学生的导师列表，导师姓名通过`get_names()`函数检索并返回。教师的情况类似，会打印教师姓名及其指导学生的姓名。Django提供了一个名为migrate的工具，可根据给定模型创建数据库关系，模型可以有版本号。当对具有新的版本号的模型调用migrate，而数据库中已有较早版本号时，migrate工具还会生成将现有数据从旧数据库模式迁移到新数据库模式的SQL代码，也可以从现有数据库模式创建Django模型。
```python
from models import Student, Instructor

def get_names(persons):
    res = ""
    for p in persons:
        res += p.name + ", "
    return res.rstrip(", ")

def person_query_model(request):
    persontype = request.GET.get('persontype')
    personname = request.GET.get('personname')
    html = ""

    if persontype =='student':
        students = Student.objects.filter(name=personname)
        for student in students:
            advisors = student.advisors.all()
            html = html + "Advisee: " + student.name + "<br>Advisors: " + get_names(advisors) + "<br> \n"
    else:
        instructors = Instructor.objects.filter(name=personname)
        for instructor in instructors:
            advisees = instructor.advisees.all()
            html = html + "Advisor: " + instructor.name + "<br>Advisees: " + get_names(advisees) + "<br> \n"
    return HttpResponse(html)
```

### 9.7 应用性能
- **性能优化挑战**：网站可能被全球数百万人访问，对于最受欢迎的网站，每秒请求数可达数千甚至更多。确保请求以低响应时间得到处理是网站开发者面临的主要挑战。为此，应用开发者尝试使用缓存等技术加速单个请求的处理，并通过使用多个应用服务器利用并行处理。
### 9.7.1 通过缓存减少开销
- **连接池技术**：假设处理每个用户请求的应用代码通过JDBC连接到数据库，创建新的JDBC连接可能需要几毫秒，所以如果要支持非常高的事务率，为每个用户请求打开新连接不是好办法。连接池方法用于减少这种开销，其工作原理如下：连接池管理器（通常是应用服务器的一部分 ）创建一个打开的ODBC/JDBC连接池（即一组连接 ）。代码（通常是Servlet）不是每次请求都打开到数据库的新连接，而是从连接池请求连接，并在处理完成后将连接返回给池。如果请求连接时池中没有未使用的连接，则打开到数据库的新连接（注意不超过数据库系统可同时支持的最大连接数 ）。如果有许多打开的连接一段时间未使用，连接池管理器可能关闭一些打开的数据库连接。许多应用服务器和较新的ODBC/JDBC驱动程序都提供内置的连接池管理器。创建连接池的细节因应用服务器或JDBC驱动程序而异，但大多数实现都需要使用JDBC连接详细信息（如机器、端口、数据库、用户ID和密码 ）以及与连接池相关的其他参数创建一个DataSource对象，对DataSource对象调用`getConnection()`方法从连接池获取连接，关闭连接会将其返回池。
- **查询结果缓存**：某些请求可能会向数据库重新提交完全相同的查询。通过缓存查询结果并重用它们（只要数据库中查询结果未改变 ），可以大大减少与数据库通信的成本。一些Web服务器支持这种查询结果缓存，否则也可以在应用代码中显式实现缓存。
- **网页缓存**：通过缓存作为对请求响应发送的最终网页，可进一步降低成本。如果新请求的参数与先前请求完全相同，且请求不执行任何更新，并且结果网页在缓存中，则可以重用该页面，避免重新计算页面的成本。缓存也可以在网页片段级别进行，然后将片段组合创建完整的网页。缓存的查询结果和缓存的网页是物化视图的形式。如果底层数据库数据发生变化，必须丢弃、重新计算或增量更新缓存结果，一些数据库系统（如Microsoft SQL Server ）提供了一种机制，允许应用服务器向数据库注册查询，并在查询结果发生变化时从数据库获取通知，这种通知机制可确保应用服务器上缓存的查询结果是最新的。
- **主流内存缓存系统**：有几个广泛使用的内存缓存系统，较流行的是memcached和Redis 。这两个系统都允许应用存储带有关联键的数据，并为指定键检索数据，它们充当哈希映射数据结构，允许将数据存储在主内存中，同时也提供对不常用数据的缓存逐出功能。例如，使用memcached时，可以使用`memcached.add(key, data)`存储数据，使用`memcached.fetch(key)`获取数据。应用在从关系r中获取具有指定键（如key1）的用户数据时，不是直接发出数据库查询，而是首先通过发出`fetch("r:"+key1)`检查所需数据是否已缓存（这里将键附加到关系名，以区分可能存储在同一memcached实例中的不同关系的数据 ）。如果`fetch`返回null，则发出数据库查询，从数据库获取的数据副本存储在memcached中，然后将数据返回给用户。如果`fetch`找到请求的数据，则可以在不访问数据库的情况下使用它，从而实现更快的访问。客户端可以连接到多个memcached实例，这些实例可能在不同机器上运行，客户端代码决定将哪些数据存储在哪个实例上。通过在多个机器上划分数据存储，应用可以利用所有机器上可用的总主内存。memcached不支持自动使缓存数据失效，但应用可以跟踪数据库更改，并对受数据库更新或删除影响的键值发出更新（使用`memcached.set(key, newvalue)`）或删除（使用`memcached.delete(key)`）操作。Redis提供非常相似的功能，memcached和Redis都提供多种语言的API。

### 9.7.2 并行处理
- **负载处理方法**：处理高负载的常用方法是使用大量并行运行的应用服务器，每个服务器处理一部分请求。可以使用Web服务器或网络路由器将每个客户端请求路由到其中一个应用服务器。来自特定客户端会话的所有请求必须发送到同一个应用服务器，因为服务器维护客户端会话的状态，例如，通过将来自特定IP地址的所有请求路由到同一个应用服务器可确保此属性。然而，底层数据库由所有应用服务器共享，因此用户可以看到一致的数据库视图。
- **避免数据库瓶颈**：虽然上述架构可确保应用服务器不会成为瓶颈，但无法防止数据库成为瓶颈，因为只有一个数据库服务器。为避免数据库过载，应用设计者通常使用缓存技术减少对数据库的请求数量。此外，当数据库需要处理大量数据或非常大的查询负载时，会使用并行数据库系统（在第21 - 23章中描述 ）。通过Web服务API访问的并行数据存储系统在需要扩展到大量用户的应用中也很流行。

### 9.8 应用安全
应用安全需要处理SQL授权之外的多种安全威胁和问题，应用开发者必须确保应用对用户进行身份验证，并保证用户只能执行授权任务。即使数据库系统本身是安全的，编写不良的应用代码也可能导致应用安全受到损害。本节先介绍可能让黑客绕过应用身份验证和授权检查的安全漏洞及防范方法，再描述安全身份验证和细粒度授权技术，以及可帮助从未经授权访问和错误更新中恢复的审计跟踪，最后讨论数据隐私问题。

#### 9.8.1 SQL注入
- **攻击原理**：在SQL注入攻击中，攻击者设法让应用执行其创建的SQL查询。例如，应用根据用户输入构建SQL查询，若代码类似`String query = "select * from student where name like '%" + name + "%' "`（其中name是包含用户输入字符串的变量 ），恶意攻击者可输入如`'; <some SQL statement>; -- %'`的字符串（`<some SQL statement>`为攻击者期望的任意SQL语句 ），最终执行的查询变为`select * from student where name like '%'; <some SQL statement>; -- %'`，攻击者插入的引号闭合字符串，分号终止查询，后续文本被解释为第二条SQL查询，从而绕过应用代码中的安全措施，对数据库执行任意操作造成严重破坏。
- **防范措施**：为避免此类攻击，最好使用预编译语句执行SQL查询。设置预编译查询的参数时，JDBC会自动添加转义字符，防止用户提供的引号终止字符串。也可在将输入字符串与SQL查询连接之前，对输入字符串应用添加转义字符的函数。另外，对于基于表单指定的选择条件和排序属性动态创建查询的应用，如`String query = "select * from takes order by " + orderAttribute`，为避免SQL注入，应用应确保`orderAttribute`变量的值是允许的值（如属性名 ），再将其附加到查询中。

#### 9.8.2 跨站脚本攻击（XSS）和跨站请求伪造（XSRF/CSRF）
- **跨站脚本攻击（XSS）**：允许用户输入文本（如评论或姓名 ）并存储，随后展示给其他用户的网站，易受跨站脚本攻击。攻击者输入JavaScript或Flash等客户端脚本语言编写的代码，而非有效姓名或评论。当其他用户查看输入文本时，浏览器执行脚本，可能将私有cookie信息发送给攻击者，或在用户登录的其他Web服务器上执行操作。例如，用户登录银行账户时脚本执行，可能将与银行账户登录相关的cookie信息发送给攻击者，攻击者利用该信息连接银行Web服务器，或访问银行网站执行转账操作。
- **跨站请求伪造（XSRF/CSRF）**：是XSS的一种特殊情况，例如`<img src="https://mybank.com/transfermoney?amount=1000&toaccount=14523">`，假设该URL接受指定参数并执行转账，就可能被利用进行攻击。
- **防范措施**：
    - **防止网站被用于发起攻击**：最简单的技术是禁止用户输入文本中包含任何HTML标签，有检测或去除此类标签的函数，可防止HTML标签及其中可能包含的脚本展示给其他用户。在某些需要HTML格式化的情况下，可使用解析文本并允许有限HTML结构但禁止其他危险结构的函数，但需谨慎设计，因为图像包含等看似无害的操作，若图像显示软件存在可被利用的漏洞，也可能很危险。
    - **保护网站免受其他网站发起的攻击**：
        - HTTP协议允许服务器检查页面访问的`referer`（即用户点击链接以发起页面访问的页面URL ），检查`referer`是否有效（如是否是同一网站的页面 ），可防止源自用户访问的其他网页的XSS攻击。
        - 除使用cookie识别会话外，还可将会话限制为最初进行身份验证的IP地址，这样即使攻击者获取cookie，也可能无法从不同计算机登录。
        - 绝不使用GET方法执行任何更新，防止如`<img src...>`形式的攻击，实际上HTTP标准规定GET方法不应执行任何更新。
        - 使用Django等Web应用框架时，确保使用框架提供的XSRF/CSRF保护机制。

#### 9.8.3 密码泄露
应用开发者面临的另一个问题是在应用代码中以明文形式存储密码，如JSP脚本常包含明文密码。若此类脚本存储在Web服务器可访问的目录中，外部用户可能访问脚本源代码并获取应用使用的数据库账户密码。为避免此问题，许多应用服务器提供机制以加密形式存储密码，服务器在将密码传递给数据库之前进行解密。但如果解密密钥也易暴露，此方法就不完全有效。另外，许多数据库系统允许将数据库访问限制在给定的一组互联网地址（通常是运行应用服务器的机器 ），来自其他地址的连接尝试将被拒绝，这样即使恶意用户获取数据库密码，若无法登录应用服务器，也无法造成损害。

#### 9.8.4 应用级身份验证
- **身份验证概念**：身份验证是验证连接到应用的人或软件身份的任务。最简单的形式是使用秘密密码，用户连接应用时需提供。但密码容易被破解，如被猜测，或在未加密传输时被网络嗅探。对于关键应用（如网上银行账户 ），需要更可靠的方案，加密是更可靠身份验证方案的基础。
- **双因素身份验证**：许多应用使用双因素身份验证，使用两个独立因素（即信息或过程片段 ）识别用户，且这两个因素不应有共同漏洞。大多数双因素身份验证方案中，密码作为第一个因素。常用的第二个因素包括：
    - 智能卡或通过USB接口连接的其他加密设备，可用于基于加密技术的身份验证。
    - 一次性密码设备，每分钟生成一个新的伪随机数，用户在身份验证时需输入设备显示的数字和密码，应用服务器可生成与用户设备相同的伪随机数序列，并在身份验证时验证数字是否匹配，此方案要求设备和服务器的时钟紧密同步。
    - 向用户注册的手机发送包含（随机生成的）一次性密码的短信，用户需输入该一次性密码和常规密码进行身份验证。
- **中间人攻击及防范**：即使采用双因素身份验证，用户仍可能遭受中间人攻击，即用户尝试连接应用时被重定向到伪造网站，该网站接受用户密码（包括第二因素密码 ）并立即用于向原始应用进行身份验证。HTTPS协议用于向用户验证网站身份（防止用户连接到伪造网站 ），还能加密数据防止中间人攻击。
- **单点登录及相关协议**：
    - 当用户访问多个网站时，分别在每个网站进行身份验证很麻烦，且每个网站可能使用不同密码。有些系统允许用户向一个中央身份验证服务进行身份验证，其他网站和应用通过该中央服务验证用户身份，用户可用同一密码访问多个网站。LDAP协议广泛用于为单个组织内的应用实现此类中央身份验证点，组织运行包含名称和密码信息的LDAP服务器，应用使用LDAP服务器验证用户身份。中央身份验证服务除验证用户外，还可向应用提供用户信息（如姓名、电子邮件和地址 ），避免在每个应用中单独输入这些信息，LDAP和Microsoft的Active Directories等目录系统可用于此任务。
    - 单点登录系统允许用户一次性通过身份验证，多个应用可进一步验证用户身份，而无需重新进行身份验证。即用户在一个网站登录后，使用同一单点登录服务的其他网站无需再次输入用户名和密码。Kerberos等网络身份验证协议长期使用此类单点登录机制，现在Web应用也有相关实现。
    - 安全断言标记语言（SAML）是在不同安全域之间交换身份验证和授权信息以提供跨组织单点登录的协议。例如，某应用需向耶鲁大学的所有学生提供访问权限，耶鲁大学可设置基于Web的身份验证服务，用户连接应用时，应用将用户重定向到耶鲁大学的身份验证服务，该服务验证用户身份后告知应用用户身份信息。

#### 9.8.5 应用级授权
- **SQL授权模型的局限性**：虽然SQL标准支持基于角色的灵活授权系统，但在典型应用中管理用户授权时作用有限。例如，若想让学生只能查看自己的成绩，SQL无法满足，原因主要有两点：
    - **缺乏终端用户信息**：随着Web发展，数据库访问主要来自Web应用服务器，终端用户在数据库中通常没有单独标识符，可能只有对应应用服务器所有用户的单个标识符，所以SQL授权规范在此场景无法使用。不过应用服务器可对终端用户进行身份验证，然后将身份验证信息传递给数据库，这里假设`syscontext.user_id()`函数返回执行查询的应用用户标识符。
    - **缺乏细粒度授权**：若要授权学生仅查看自己的成绩，授权需精确到单个元组级别，但当前SQL标准只允许对整个关系、视图或关系与视图的指定属性进行授权，无法实现这种细粒度授权。
- **解决细粒度授权的尝试**：
    - **创建单个视图**：为每个学生在`takes`关系上创建一个只显示其成绩的视图，理论可行，但实际操作中为每个学生创建视图极其繁琐，不具可行性。
    - **创建通用视图**：创建如下视图：
```sql
create view studentTakes as
select *
from takes
where takes.ID = syscontext.user_id()
```
用户被授予对该视图的访问权限，而非底层`takes`关系。但这样学生执行的查询需基于`studentTakes`视图，教师执行的查询可能需用不同视图，增加了应用开发的复杂性。
- **应用级授权的问题**：授权任务常完全在应用中执行，绕过SQL授权功能。在应用层面，用户被授权访问特定接口，还可能进一步限制为仅查看或更新某些数据项。虽然应用级授权给开发者很大灵活性，但也存在问题：
    - 检查授权的代码与应用其他代码混杂。
    - 通过应用代码实现授权，而非在SQL中声明式指定，难以确保无漏洞。可能因疏忽，应用程序未检查授权，使未经授权用户访问机密数据。检查所有应用程序是否进行必要授权检查需通读大量应用服务器代码，在大型系统中任务艰巨，实际上许多实际应用中都发现了安全漏洞。
- **数据库级细粒度授权**：相比之下，若数据库直接支持细粒度授权，授权策略可在SQL级别指定和执行，范围更小。即使部分应用接口意外遗漏必要授权检查，SQL级授权也能防止未经授权操作执行。一些数据库系统提供行级授权机制，如Oracle虚拟专用数据库（VPD）允许系统管理员将函数与关系关联，函数返回的谓词会添加到使用该关系的任何查询中（对不同操作的关系可定义不同函数 ）。例如，对于`takes`关系的函数可返回谓词`ID = sys_context.user_id()`，该谓词会添加到使用`takes`关系的每个查询的`where`子句中，这样每个学生只能看到自己所选课程的元组。但添加谓词可能改变查询含义，如用户查询所有课程平均成绩，实际得到的是自己成绩的平均。PostgreSQL和Microsoft SQL Server也提供类似Oracle VPD的行级授权支持。 

#### 9.8.6 审计跟踪
- **审计跟踪的定义与作用**：审计跟踪是对应用数据所有更改（插入、删除和更新 ）的日志记录，包含执行更改的用户及更改时间等信息。当应用安全被破坏，或有错误更新时，审计跟踪可帮助查明发生的事情及可能的执行者，还有助于修复安全漏洞或错误更新造成的损害。例如，发现学生成绩不正确，可通过审计日志确定成绩何时及如何更新，以及执行更新的用户，学校还可通过审计跟踪追溯该用户的所有更新，查找其他不正确或欺诈性更新并纠正。审计跟踪也可检测用户账户被入侵的安全漏洞，如用户每次登录时告知其近期从该登录发起的所有更新，若发现非自己执行的更新，可能账户已被入侵。
- **创建审计跟踪的方式**：可通过在关系更新上定义适当触发器（使用标识用户名和时间的系统定义变量 ）创建数据库级审计跟踪，但许多数据库系统提供更方便的内置机制，创建审计跟踪的具体细节因数据库系统而异，需参考相应系统手册。数据库级审计跟踪对应用通常不够，因其通常无法跟踪应用终端用户，且更新记录处于较低级别（关系元组更新 ），而非业务逻辑的更高级别。所以应用通常创建更高级别的审计跟踪，记录执行的操作、执行者、时间及请求来源IP地址等信息。
- **保护审计跟踪**：相关问题是保护审计跟踪不被修改或删除。一种解决方法是将审计跟踪复制到入侵者无法访问的另一台机器，记录生成时立即复制。更可靠的方法是使用区块链技术（第26章介绍 ），区块链技术在多台机器存储日志，并使用哈希机制，使入侵者难以在不被察觉的情况下修改或删除数据。

#### 9.8.7 隐私
- **隐私问题的背景**：在个人数据大量在线的时代，人们越来越关注数据隐私。例如，多数人希望个人医疗数据保密，不被公开，但医疗数据又需提供给治疗患者的医生和急救医疗技术人员。许多国家有隐私相关法律，规定数据何时及向谁披露，违反隐私法在部分国家可能面临刑事处罚，访问此类私人数据的应用必须严格遵守隐私法。
- **隐私数据的应用与保护**：另一方面，聚合私人数据在检测药物副作用、疫情传播等任务中可发挥重要作用。如何在不损害个人隐私前提下，让研究人员获取这些数据是实际问题。例如，医院提供患者出生日期和邮政编码，这两项信息结合外部数据库信息可能识别患者身份，破坏隐私。解决方法可以是提供出生年份而非具体日期，加上地址信息，可能为研究人员提供足够识别多数个体的信息。又如，网站常收集地址、电话、电子邮件和信用卡信息等个人数据用于交易，但客户可能不希望信息被其他组织获取，或希望部分信息（如信用卡号码 ）在一段时间后删除，防止安全漏洞时信息落入未经授权者手中。许多网站允许客户指定隐私偏好，网站必须尊重这些偏好。

### 9.9 加密及其应用
加密是将数据转换为不可读形式的过程，除非应用解密的逆过程。加密算法使用加密密钥进行加密，需要解密密钥（根据使用的加密算法，可能与加密密钥相同 ）进行解密。
- **加密的用途**：加密最早用于传输消息，发送方和预期接收方知道秘密密钥，即使消息被敌人截获，因敌人不知密钥无法解密理解消息。如今加密广泛用于保护传输中的数据（如互联网和手机网络上的数据传输 ），也用于执行身份验证等其他任务。在数据库环境中，加密用于安全存储数据，防止未经授权用户获取数据（如包含数据的笔记本电脑被盗，无解密密钥无法访问数据 ）。许多数据库存储敏感客户信息（如信用卡号码、姓名、指纹、签名和身份证号码 ），犯罪分子获取这些信息可用于非法活动，所以存储此类敏感数据的应用必须采取措施保护，许多国家和州法律要求存储敏感信息的数据库以加密形式存储，企业若不保护数据可能承担数据盗窃的刑事责任，因此加密是存储敏感信息数据库的重要组成部分。
- **加密技术**：数据加密技术众多，简单加密技术可能无法提供足够安全，未经授权用户易破解。例如，将每个字符替换为字母表中下一个字符的加密方式很弱，如“Perryridge”加密后变为“Qfsszsjehf” 。

### 9.9 加密及其应用
#### 9.9.1 加密技术
- **高级加密标准（AES）**：是一种对称密钥加密算法，2000年被美国政府采用为加密标准，现广泛使用。基于Rijndael算法，每次对128位数据块操作，密钥长度可为128、192或256位。算法通过一系列步骤打乱数据块中的位，这些步骤在解密时可逆向操作，还会与从加密密钥派生的128位“轮密钥”进行异或操作，每个加密的数据块都会从加密密钥生成新的轮密钥。解密时，再次从加密密钥生成轮密钥，逆向加密过程恢复原始数据。早期的1977年采用的数据加密标准（DES）曾广泛使用。对称密钥加密方案需通过安全机制向授权用户提供加密密钥，这是其主要弱点，因为方案的安全性取决于密钥传输机制的安全性。
- **公钥加密**：是避免对称密钥加密技术部分问题的替代方案。基于公钥和私钥两个密钥，每个用户$U_i$有公钥$E_i$和私钥$D_i$，公钥公开，私钥仅用户自己知道。若用户$U_1$想存储加密数据，用公钥$E_1$加密，解密需私钥$D_1$。若$U_1$想与$U_2$共享数据，用$U_2$的公钥$E_2$加密，因只有$U_2$知道如何解密，信息可安全传输。公钥加密可行的前提是存在难以从公钥推导出私钥的加密方案，其基于以下条件：
    - 存在测试一个数是否为素数的高效算法。
    - 目前不存在找到一个数的素因子的高效算法。
在此方案中，数据视为整数集合，通过计算两个大素数$P_1$和$P_2$的乘积创建公钥，私钥由$(P_1, P_2)$对组成。仅知道乘积$P_1P_2$无法成功使用解密算法，需要$P_1$和$P_2$的单独值。选择足够大（超过100位 ）的$P_1$和$P_2$，可使分解$P_1P_2$的成本极高。公钥加密虽安全，但计算成本高。常用于安全通信的混合方案是：随机生成对称加密密钥（如基于AES ），用公钥加密方案安全交换，再用该密钥对后续传输的数据进行对称密钥加密。
- **防止字典攻击**：加密小值（如标识符或名称 ）时，可能面临字典攻击，尤其是加密密钥公开时。例如出生日期字段加密后，攻击者可能尝试加密每个可能的出生日期，直到找到与加密值匹配的。即使加密密钥不公开，也可利用数据分布的统计信息推断加密值代表的内容（如年龄或地址 ），如数据库中18岁最常见，出现最频繁的加密年龄值可能代表18岁。在加密前向值末尾添加额外随机位（解密后移除 ）可阻止字典攻击，这些额外位在AES中称为初始化向量，在其他情境中称为盐值位。

#### 9.9.2 数据库中的加密支持
许多文件系统和数据库系统支持数据加密，可保护数据不被能访问数据但无解密密钥的人获取。在数据库环境中，加密可在不同级别进行：
- **磁盘块级加密**：最低级别是使用数据库系统软件可用的密钥加密包含数据库数据的磁盘块。从磁盘检索块时，先解密再正常使用。这种加密可防止能访问磁盘内容但无加密密钥的攻击者。
- **属性级加密**：更高级别是将关系的指定（或所有 ）属性以加密形式存储，每个属性用不同加密密钥。许多数据库支持指定属性及整个关系或数据库中所有关系的加密。指定属性加密可最小化解密开销，允许应用仅加密包含敏感值（如信用卡号码 ）的属性，还需使用额外随机位防止字典攻击。但数据库通常不允许对主键和外键属性加密，且不支持在加密属性上建立索引。访问加密数据需解密密钥，所有加密数据可用单个主加密密钥，属性级加密时不同属性可用不同密钥，不同属性的解密密钥可存储在文件或关系（常称为“钱包” ）中，访问加密属性的数据库连接需提供主密钥，否则无法访问加密数据，主密钥可存储在应用程序（通常在不同计算机上 ）或由数据库用户记住并在连接数据库时提供。数据库级加密相对时间和空间开销较低，无需修改应用程序，如保护笔记本电脑数据库数据不被盗取可使用这种加密，同样，没有解密密钥，访问数据库备份磁带的人也无法访问备份中的数据。
- **应用级加密**：替代在数据库中进行加密的方法是在数据发送到数据库之前进行加密，应用在将数据发送到数据库前加密，检索时解密。这种数据加密方法需要对应用进行大量修改，与数据库系统内的加密不同。

#### 9.9.3 加密与身份验证
基于密码的身份验证在操作系统和数据库系统中广泛使用，但存在一些缺点，尤其是在网络环境中。若窃听者能“嗅探”网络上传输的数据，可能获取传输中的密码。   

### 9.10 总结
- **应用架构发展**：自20世纪60年代起，以数据库为后端与用户交互的应用程序就已存在，期间应用架构不断演进。如今多数应用以Web浏览器为前端，数据库为后端，中间通过应用服务器连接。
- **Web技术基础**：
    - HTML可定义结合超链接和表单功能的界面。Web浏览器通过HTTP协议与Web服务器通信，Web服务器能将请求传递给应用程序，并将结果返回给浏览器。
    - Web服务器执行应用程序以实现所需功能。Servlet是广泛使用的机制，用于编写作为Web服务器进程一部分运行的应用程序，以减少开销。还有许多服务器端脚本语言，由Web服务器解释，提供应用程序功能。
    - 存在多种客户端脚本语言，其中JavaScript应用最为广泛，在浏览器端提供更丰富的用户交互。
- **复杂应用架构**：复杂应用通常采用多层架构，包括实现业务逻辑的模型、控制器，以及用于显示结果的视图机制。还可能包含实现对象 - 关系映射的数据访问层。许多应用实现并使用Web服务，允许通过HTTP调用函数。
- **性能优化技术**：采用多种形式的缓存技术（如查询结果缓存和连接池 ）以及并行处理等技术来提高应用性能。
- **应用安全要点**：应用开发者必须密切关注安全问题，防范SQL注入攻击和跨站脚本攻击等。
- **授权机制局限与发展**：SQL授权机制粒度较粗，对于处理大量用户的应用价值有限。如今应用程序在数据库系统之外实现细粒度的元组级授权，以处理大量应用用户。虽已开发出提供元组级访问控制和处理大量应用用户的数据库扩展，但尚未成为标准。
- **数据隐私保护**：保护数据隐私是数据库应用的重要任务，许多国家对保护信用卡信息、医疗数据等特定类型的数据有法律要求。
- **加密的重要作用**：
    - 加密在保护信息以及用户和网站身份验证方面起着关键作用。对称密钥加密和公钥加密是两种不同但广泛使用的加密方法。在许多国家和州，对存储在数据库中的某些敏感数据进行加密是法律要求。
    - 加密在用户对应用程序的身份验证、网站对用户的身份验证以及数字签名方面也起着关键作用。
- **数字证书认证**：数字证书可用于用户身份验证。用户需向网站提交包含其公钥的数字证书，网站验证证书是否由受信任的机构签名。然后用户的公钥可用于挑战 - 响应系统，以确保用户拥有对应的私钥，从而对用户进行身份验证。

>日期：4.26

### 课后习题选做
### 9.13
**HTML代码**
```html
<!DOCTYPE html>
<html>

<body>
    <form action="SimpleServlet" method="post">
        <label for="n">Enter a value n:</label>
        <input type="number" id="n" name="n" required>
        <input type="submit" value="Submit">
    </form>
</body>

</html>
```
**Servlet代码（Java）**
```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class SimpleServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String nStr = request.getParameter("n");
        int n = Integer.parseInt(nStr);

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < n; i++) {
            result.append("*");
        }

        out.println("<html><body>");
        out.println("Result: " + result.toString());
        out.println("</body></html>");
    }
}
```
需在`web.xml`（或使用Servlet 3.0+的注解配置 ）中配置Servlet映射。

### 9.14
**HTML代码**
```html
<!DOCTYPE html>
<html>

<body>
    <form action="CountServlet" method="post">
        <label for="n">Enter a number n:</label>
        <input type="number" id="n" name="n" required>
        <input type="submit" value="Submit">
    </form>
</body>

</html>
```
**Servlet代码（Java）**
```java
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class CountServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String nStr = request.getParameter("n");
        int n = Integer.parseInt(nStr);

        Connection conn = null;
        PreparedStatement pstmtInsert = null;
        PreparedStatement pstmtSelect = null;
        ResultSet rs = null;

        try {
            // 数据库连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/yourdatabase", "username", "password");
            // 插入新记录
            String insertSql = "INSERT INTO submission_count (number) VALUES (?)";
            pstmtInsert = conn.prepareStatement(insertSql);
            pstmtInsert.setInt(1, n);
            pstmtInsert.executeUpdate();

            // 查询记录次数
            String selectSql = "SELECT COUNT(*) as count FROM submission_count WHERE number =?";
            pstmtSelect = conn.prepareStatement(selectSql);
            pstmtSelect.setInt(1, n);
            rs = pstmtSelect.executeQuery();

            if (rs.next()) {
                int count = rs.getInt("count");
                out.println("<html><body>");
                out.println("The value " + n + " has been submitted " + count + " times.");
                out.println("</body></html>");
            }
        } catch (SQLException e) {
            e.printStackTrace();
            out.println("<html><body>Error occurred: " + e.getMessage() + "</body></html>");
        } finally {
            try {
                if (rs != null) rs.close();
                if (pstmtInsert != null) pstmtInsert.close();
                if (pstmtSelect != null) pstmtSelect.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```
需创建`submission_count`表存储提交的数字，表结构如`CREATE TABLE submission_count (id INT AUTO_INCREMENT PRIMARY KEY, number INT)`，并配置Servlet映射。

### 9.15
**Servlet代码（Java）**
```java
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

public class AuthenticationServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String username = request.getParameter("username");
        String password = request.getParameter("password");

        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            // 数据库连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/yourdatabase", "username", "password");
            // 查询用户
            String sql = "SELECT user_id FROM users WHERE username =? AND password =?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, username);
            pstmt.setString(2, password);
            rs = pstmt.executeQuery();

            if (rs.next()) {
                HttpSession session = request.getSession();
                String userid = rs.getString("user_id");
                session.setAttribute("userid", userid);
                out.println("<html><body>");
                out.println("Authentication successful. Welcome, user!");
                out.println("</body></html>");
            } else {
                out.println("<html><body>");
                out.println("Authentication failed. Please check your username and password.");
                out.println("</body></html>");
            }
        } catch (SQLException e) {
            e.printStackTrace();
            out.println("<html><body>Error occurred: " + e.getMessage() + "</body></html>");
        } finally {
            try {
                if (rs != null) rs.close();
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```
需创建`users`表存储用户名、密码和用户ID等信息，表结构如`CREATE TABLE users (user_id INT PRIMARY KEY, username VARCHAR(50), password VARCHAR(50))`，并配置Servlet映射。

### 9.16
**SQL注入攻击定义与原理**
SQL注入攻击是攻击者通过在应用输入中插入恶意SQL语句，使应用执行非预期的SQL查询。例如，应用根据用户输入构建SQL查询，若代码类似`String query = "select * from student where name like '%" + name + "%' "`（`name`为用户输入 ），攻击者输入`'; DROP TABLE student; --`，最终执行的查询变为`select * from student where name like '%'; DROP TABLE student; -- %'` 。单引号闭合字符串，分号终止当前查询，`--`注释掉后面内容，从而导致`DROP TABLE student`语句被执行，删除`student`表。

**防范措施**
- 使用预编译语句（Prepared Statements）。如在JDBC中，`PreparedStatement`会自动对用户输入进行转义，防止特殊字符被误当作SQL语法的一部分。示例：
```java
String sql = "select * from student where name like?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, name);
ResultSet rs = pstmt.executeQuery();
```
- 对用户输入进行严格校验和过滤，只允许符合预期格式的数据，阻止非法字符和恶意语句。

### 9.17
```pseudocode
// 创建连接池函数
function createConnectionPool(databaseConnectionString, databaseUserName, databasePassword)
    // 初始化连接池
    connectionPool = []
    // 假设最大连接数为10
    maxConnections = 10
    for i from 1 to maxConnections
        connection = createDatabaseConnection(databaseConnectionString, databaseUserName, databasePassword)
        add connection to connectionPool
    return connectionPool
end function

// 请求连接函数
function requestConnection(connectionPool)
    if connectionPool is empty
        // 等待连接释放或抛出异常
        waitOrThrowException()
    else
        connection = removeFirstElement(connectionPool)
        return connection
    end if
end function

// 释放连接函数
function releaseConnection(connectionPool, connection)
    add connection to connectionPool
end function

// 关闭连接池函数
function closeConnectionPool(connectionPool)
    for each connection in connectionPool
        close connection
    end for
    clear connectionPool
end function
```

### 9.18
- **CRUD**：是创建（Create）、读取（Read）、更新（Update）和删除（Delete）的缩写，是对数据进行的基本操作。在数据库操作中，`CREATE`用于插入新记录，`READ`（通常对应`SELECT`语句 ）用于查询数据，`UPDATE`用于修改现有记录，`DELETE`用于删除记录。在应用开发中，这是处理数据的常见操作集合。
- **REST**：即表述性状态转移（Representational State Transfer），是一种设计Web服务的架构风格。它基于HTTP协议，使用标准的HTTP方法（如GET、POST、PUT、DELETE ）对应CRUD操作。RESTful API以资源为中心，每个资源有唯一的URI标识，强调无状态性，即客户端每次请求都包含足够信息让服务器处理，不依赖之前请求的状态，可提高系统的可扩展性和可靠性。 

### 9.19
**可揭示使用Ajax的网站特征**
- **无页面刷新更新内容**：在浏览网站时，某些区域内容更新但整个页面未重新加载，比如社交媒体动态更新、电商网站商品列表的局部更新等。
- **异步加载内容**：例如下拉菜单选项、图片懒加载等情况，当鼠标悬停下拉菜单时，选项数据异步加载显示；图片在滚动到特定位置时才加载，这些可能是使用了Ajax技术。

**三个使用Ajax的网站示例（需实际验证 ）**：
- GitHub：其代码仓库的文件浏览、提交历史查看等功能有动态更新且无页面刷新。
- Twitter：推文加载、点赞评论操作后的实时更新等。
- Airbnb：房源搜索结果展示、筛选条件应用后的内容更新。

### 9.20
- **a. XSS攻击定义**：跨站脚本攻击（Cross - Site Scripting，XSS）是攻击者在允许用户输入文本（如评论、姓名等 ）并展示给其他用户的网站中，输入客户端脚本语言（如JavaScript、Flash ）编写的代码。当其他用户查看这些输入文本时，浏览器会执行脚本，可能导致如发送用户私有cookie信息给攻击者、在用户登录的其他Web服务器上执行操作等危害。
- **b. referer字段检测XSS攻击原理**：`referer`字段是HTTP请求头的一部分，记录了用户访问当前页面的来源页面URL。在XSS攻击中，若攻击者通过外部链接（如恶意网站链接 ）诱导用户访问并触发攻击，可通过检查`referer`字段来判断请求来源是否正常。如果`referer`指向可疑或非预期的外部站点，可能存在XSS攻击风险。但这种检测方法并非万无一失，因为攻击者也可能通过其他手段绕过`referer`检查。 

### 9.21
**多因素身份验证定义**：多因素身份验证（Multifactor Authentication，MFA）是使用两个或多个独立因素来识别用户身份的方法。这些因素通常分为三类：用户知道的（如密码 ）、用户拥有的（如智能卡、手机 ）、用户本身的（如指纹、面部识别 ） 。

**防范密码被盗原理**：传统基于密码的身份验证方式，若密码被盗，攻击者可轻易登录账户。而多因素身份验证增加了额外的安全层，即使密码被盗，攻击者还需获取用户拥有的设备（如手机接收验证码 ）或满足其他生物特征等因素，才能成功登录，从而大大提高了账户安全性，降低了密码被盗用带来的风险。 

### 9.22
- **a.**
假设存在`instructors`表有`ID`字段（教师ID ），`takes`表有`course_id`、`sec_id`、`semester`、`year`、`ID`（学生ID ）等字段，`teaches`表有`course_id`、`sec_id`、`semester`、`year`、`instructor_ID`（教师ID ）等字段。
```sql
SELECT *
FROM takes
WHERE (course_id, sec_id, semester, year) IN (
    SELECT course_id, sec_id, semester, year
    FROM teaches
    WHERE instructor_ID = user_id()
);
```
这里`user_id()`假设是获取当前教师用户ID的函数。

- **b.**
原始查询：
```sql
SELECT * FROM takes;
```
添加谓词后的查询：
```sql
SELECT *
FROM takes
WHERE (course_id, sec_id, semester, year) IN (
    SELECT course_id, sec_id, semester, year
    FROM teaches
    WHERE instructor_ID = user_id()
);
```
当原始查询返回所有`takes`表记录，添加谓词后只返回当前教师教授课程对应的`takes`记录，是原始结果子集。

- **c.**
原始查询：
```sql
SELECT * FROM takes WHERE grade IS NOT NULL;
```
添加谓词后的查询：
```sql
SELECT *
FROM takes
WHERE (course_id, sec_id, semester, year) IN (
    SELECT course_id, sec_id, semester, year
    FROM teaches
    WHERE instructor_ID = user_id()
) AND grade IS NOT NULL;
```
假设存在某学生选修的课程记录（不在当前教师教授课程内 ）成绩不为空，原始查询会返回该记录，添加谓词后不会返回，而对于当前教师教授课程内成绩不为空的记录会返回，可能出现添加谓词后结果包含原始结果没有的元组（当有新学生选了当前教师课程且成绩不为空时 ）。

### 9.23
- **保护数据安全**：加密存储在数据库中的数据，即使数据被非法获取（如数据库被盗取 ），没有解密密钥，攻击者也无法读取数据内容，防止敏感信息（如信用卡号、个人身份信息 ）泄露，保护用户隐私和企业数据资产。
- **满足合规要求**：许多国家和地区对特定类型数据（如医疗数据、金融数据 ）有法律规定，要求进行加密存储。加密数据可帮助企业满足这些合规要求，避免法律风险和罚款。 

### 9.24
- **a.**
**`takes_trail`关系模式**：
```sql
CREATE TABLE takes_trail (
    id INT AUTO_INCREMENT PRIMARY KEY,
    operation VARCHAR(10), -- 操作类型，如'INSERT' 'UPDATE' 'DELETE'
    user_id VARCHAR(50),
    timestamp TIMESTAMP,
    old_value TEXT, -- 旧值，以JSON等格式存储方便记录复杂数据
    new_value TEXT
);
```
**插入触发器**：
```sql
DELIMITER //
CREATE TRIGGER takes_insert_trigger
AFTER INSERT ON takes
FOR EACH ROW
BEGIN
    INSERT INTO takes_trail (operation, user_id, timestamp, old_value, new_value)
    VALUES ('INSERT', user_id(), NOW(), NULL, CONCAT(NEW.course_id, ',', NEW.sec_id, ',', NEW.semester, ',', NEW.year, ',', NEW.ID));
END //
DELIMITER ;
```









