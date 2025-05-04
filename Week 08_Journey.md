本周阅读**Database-System-Concepts-7th-Edition**:PART THREE APPLICATION DESIGN AND DEVELOPMENT,知识点总结如下：  
**Chapter 9 Application Development**(p403-462)  
>日期：4.15
 
## 应用程序与用户界面（9.1）
- 大多数用户通过应用程序间接与数据库交互，而非直接使用查询语言。常见的用户与数据库交互的应用程序，前端提供用户界面，后端与数据库接口。例如大学注册系统，用户先通过用户名和密码进行身份验证，应用程序从数据库提取相关信息并展示，还提供注册课程和查询信息等接口。
- 现代以用户为中心的应用程序关键在于良好的用户界面，基于数据库的应用程序常见用户界面类型为网页和移动应用界面。

## 网页基础（9.2）
### 统一资源定位符（URL）
- URL是网页上可访问文档的全球唯一名称。例如 `http://www.acm.org/sigmod` ，其中“http”表示通过超文本传输协议（HTTP ，用于传输HTML文档的协议 ）访问文档，“https”是HTTP的安全版本，是如今更推荐的模式；第二部分是拥有Web服务器的机器名称；其余部分是机器上文件的路径名或文档的唯一标识符。
- URL还可包含Web服务器上程序的标识符及传递给程序的参数，如 `https://www.google.com/search?q=silberschatz` ，服务器收到此类URL请求时，会使用给定参数执行程序，程序返回HTML文档给服务器，再由服务器发送回前端。

### 超文本标记语言（HTML）
- HTML用于格式化信息显示和创建基于表单的界面。每个HTML页面需包含在 `<html>` 标签内，页面主体在 `<body>` 标签内。
- 例如表格由 `<table>` 标签定义，行由 `<tr>` 标签定义，表头单元格由 `<th>` 标签定义，普通单元格由 `<td>` 标签定义 。
- HTML表单可让用户输入信息，如 `<form action="PersonQuery" method=get>` 定义表单，其中 `action` 属性指定表单提交时数据发送的URL（相对页面URL ），`method=get` 表示使用GET方法将用户输入值编码为URL的一部分发送到Web服务器。表单包含如 `<select>` （下拉菜单选择 ）、`<input>` （文本输入等 ）等标签。

### 网页服务器与会话（9.2.3）
- **网页服务器**：运行在服务器机器上的程序，接受来自网页浏览器的请求，并以HTML文档的形式返回结果。浏览器和网页服务器通过HTTP通信。网页服务器除了简单传输文档外，还能执行用户提供参数的程序，并将结果以HTML文档返回。常见网关接口（CGI）标准定义了网页服务器与应用程序的通信方式，应用程序通常通过ODBC、JDBC或其他协议与数据库服务器通信。
- **三层架构**：由网页服务器、应用服务器和数据库服务器组成，但使用多层服务器会增加系统开销，CGI接口为每个请求启动新进程，开销更大。
- **两层架构**：如今多数Web应用采用，将网页服务器和应用服务器合并为单个服务器。
- **无连接特性**：客户端和网页服务器之间没有持续连接，服务器收到请求时临时创建连接，请求满足后连接可关闭，下一个请求通过新连接。这是因为多数计算机能同时容纳的连接数有限，无连接协议可让连接在请求满足后断开，为其他请求腾出连接资源。
- **会话实现**：多数Web应用需要会话信息实现有意义的用户交互，如限制信息访问需对用户进行身份验证，且会话中后续交互无需重新验证。为在连接关闭情况下实现会话，需在客户端存储额外信息（通常以cookie形式 ），每次请求时返回给服务器，服务器用此信息识别请求是否属于用户会话。例如，应用可能生成会话标识符（通常是未使用的随机数 ），并发送名为sessionid的cookie包含该标识符，服务器也在本地存储会话标识符。应用可根据安全需求决定cookie存储策略，对安全性要求不高的应用（如新闻网站 ），cookie可永久存储在浏览器和服务器；对安全性要求高的应用，服务器可在超时或用户注销时使会话失效（从活动会话列表中删除会话标识符 ）。

### Servlet（9.3）
- **定义**：Java servlet规范定义了网页/应用服务器与应用程序通信的应用程序编程接口。Java中的HttpServlet类实现了servlet API规范，用于实现特定功能的servlet类定义为该类的子类。
- **代码加载与任务**：servlet代码在服务器启动时，或服务器收到执行特定servlet的远程HTTP请求时加载到网页/应用服务器。servlet的任务是处理请求，可能涉及访问数据库检索信息，并动态生成HTML页面返回给客户端浏览器。
- **示例**：以PersonQueryServlet为例，处理图9.3中表单请求（表单指定“action="PersonQuery"” ）。通过注解@WebServlet("PersonQuery")告知网页/应用服务器该servlet处理PersonQuery请求。表单指定使用HTTP get机制传输参数，所以调用servlet的doGet()方法。每个servlet请求在新线程中执行，可并行处理多个请求。doGet()方法通过request.getParameter()提取参数值（如persontype和name ），用于对数据库运行查询，查询结果以HTML格式输出到HttpServletResponse对象返回给请求者，例如调用Util::resultSetToHTML(resultset, header, out)函数将查询结果转换为HTML格式输出 。

### HTML相关补充
- **HTML5表单输入类型**：包括日期和时间选择（`<input type="date" name="abc">` 、`<input type="time" name="xyz">` ，浏览器通常显示图形化日期或时间选择器，输入值存储在表单属性中，可通过min和max属性指定可选的最小和最大值 ）等。
- **HTML编辑器**：除用纯文本编辑器创建HTML代码外，还有图形界面编辑器，可从菜单选择插入表单、菜单、表格等结构，无需手动输入代码。
- **HTML样式表**：HTML支持样式表（如层叠样式表CSS标准 ），可改变HTML格式化结构的默认显示定义及其他显示属性（如页面背景颜色 ），同一样式表可用于多个HTML文档，使网站页面具有统一风格。

### Servlet相关
#### Servlet会话管理（9.3.2）
- **无状态交互**：浏览器与网页/应用服务器的交互是无状态的，每次浏览器向服务器请求信息后就断开连接。Cookies可用于识别请求是否来自同一会话，但它是底层机制，程序员需要更好的抽象来处理会话。
- **会话跟踪方法**：Servlet API提供了`HttpServletRequest`类的`getSession()`方法来跟踪会话并存储相关信息。`getSession(false)`用于检索与发送请求的浏览器对应的`HttpSession`对象；若参数为`true`，则在请求是新请求时创建新的会话对象。调用该方法时，服务器先要求客户端返回指定名称的cookie，若客户端没有该cookie或值与会话不匹配，请求不属于当前会话，`getSession()`返回`null` ，Servlet可引导用户到登录页面。
- **会话对象操作**：登录页面的Servlet可验证用户密码，若验证通过，执行`getSession(true)`创建新会话对象。服务器内部会设置包含会话标识符的cookie（如sessionId ），创建新会话对象并关联标识符。Servlet代码可在`HttpSession`对象中存储和查找（属性名，值 ）对，以维护会话内多个请求的状态。例如，用户认证后，登录Servlet可通过`session.setAttribute("userid", userid)`将用户id存储为会话参数；后续请求中，可通过`session.getAttribute("userid")`检索该参数，若返回`null` ，表示用户未认证。

#### Servlet生命周期（9.3.3）
- **实例创建与初始化**：Servlet的生命周期由部署它的网页/应用服务器控制。当客户端请求特定Servlet时，服务器先检查Servlet实例是否存在，不存在则将Servlet类加载到Java虚拟机（JVM）并创建实例，同时调用`init()`方法初始化，每个实例仅在加载时初始化一次。
- **请求处理**：确保Servlet实例存在后，服务器调用其`service`方法，传入请求对象和响应对象作为参数。默认情况下，服务器创建新线程执行`service`方法，可并行处理多个请求，`service`方法会根据请求类型调用`doGet`或`doPost`等方法。
- **关闭**：当不再需要Servlet时，可通过调用`destroy()`方法关闭。服务器可设置在一段时间内无请求时自动关闭Servlet，超时时间是可配置的服务器参数。

#### 应用服务器（9.3.4）
- **内置支持**：许多应用服务器提供对Servlet的内置支持，如Apache Jakarta项目的Tomcat Server，其他支持Servlet的应用服务器包括Glassfish、JBoss、BEA Weblogic Application Server、Oracle Application Server和IBM的WebSphere Application Server等。
- **开发方式**：开发Servlet应用的最佳方式是使用集成开发环境（IDE），如Eclipse或NetBeans，它们内置Tomcat或Glassfish服务器。
- **服务功能**：应用服务器除基本的Servlet支持外，还提供多种有用服务，如允许应用部署或停止，提供监控应用服务器状态（包括性能统计信息 ）的功能。许多应用服务器还支持Java 2 Enterprise Edition（J2EE）平台，为处理对象、跨多个应用服务器并行处理等任务提供支持和API。

### 替代服务器端框架（9.4）
#### 服务器端脚本（9.4.1）
- **优势**：使用Java或C等编程语言编写简单Web应用耗时且代码量多，需要熟悉语言细节的程序员。服务器端脚本提供了更简便的方法，脚本语言的结构可嵌入HTML文档。在交付网页前，服务器执行嵌入在页面HTML内容中的脚本，执行后的脚本代码从页面移除，客户端可能不知道页面原本有代码，脚本还可包含对数据库执行的SQL代码，许多脚本语言及其库和工具构成了Web应用开发框架。
- **常用框架**：
    - **Java Server Pages（JSP）**：允许HTML程序员将静态HTML与动态生成的HTML混合。对于许多动态网页，大部分内容是静态的，动态内容（如基于表单参数生成 ）通常占比小。编写Servlet代码创建此类页面需将大量HTML编码为Java字符串，而JSP允许将Java代码嵌入静态HTML中生成动态部分。JSP脚本会转换为Servlet代码并编译，减轻了应用程序员编写Servlet Java代码的负担。JSP页面中的Java代码用`<%... %>`区分，例如`<% if (request.getParameter("name") == null) { out.println("Hello World"); } else { out.println("Hello, " + request.getParameter("name")); } %>` 。浏览器请求JSP页面时，应用服务器生成HTML输出发送给浏览器，JSP页面的HTML部分原样输出，嵌入的Java代码被其打印到`out`对象的文本替换。JSP还支持标签库概念，标签类似HTML标签，但在服务器端解释并替换为生成的HTML代码，提供定义变量和控制流的标准标签集，以及基于JavaScript的表达式语言（在服务器端解释 ），标签集可扩展，有支持大数据集分页显示和简化日期时间显示解析的标签库。
    - **PHP**：广泛用于服务器端脚本的语言，PHP代码可与HTML混合，`<?php`表示PHP代码开始，`?>`表示结束。例如`<?php if (!isset($_REQUEST['name'])) { echo 'Hello World'; } else { echo 'Hello, '. $_REQUEST['name']; }?>` ，功能与上述JSP代码类似。

### 服务器端相关
#### 服务器端框架特性（9.4.2）
- **功能库**：支持HTML和HTTP特性（包括会话 ）的函数库。
- **模板脚本系统**：用于构建网页内容。
- **控制器**：将用户交互事件（如表单提交 ）映射到处理该事件的适当函数，同时管理认证和会话，部分框架还提供管理授权的工具。
- **表单声明方式**：以（相对）声明性的方式指定带有用户输入验证约束的表单，系统据此生成HTML和JavaScript/Ajax代码来实现表单。
- **面向对象模型**：带有对象关系映射，用于将数据存储在关系数据库中。这些框架以集成方式提供构建Web应用所需的各种功能，通过声明性规范生成表单和透明管理数据访问，减少Web应用程序员的编码量。

#### Django框架（9.4.3）
- **视图功能**：Django中的视图是与Java中Servlet等效的函数，Django要求通过urls.py文件指定URL到Django视图的映射。当Django应用服务器收到HTTP请求时，使用该URL映射决定调用哪个视图函数。
- **示例代码功能**：以person_query_view视图为例，首先检查会话变量username判断用户是否登录，未设置则重定向到登录屏幕；已登录则连接数据库获取用户信息。数据库连接细节在settings.py配置文件中指定，通过游标（类似JDBC语句 ）执行查询，查询结果通过result_set_to_html函数迭代处理并以HTML格式输出返回。Django还支持创建HTML表单、验证表单数据、简化认证检查注释、创建HTML页面模板以及对象关系映射系统等功能。

### 客户端代码与Web服务（9.5）
#### JavaScript（9.5.1）
- **输入验证（9.5.1.1）**：JavaScript函数可对用户输入进行错误检查（验证 ），如日期字符串格式是否正确、输入值（如年龄 ）是否在合适范围等。在数据发送到Web服务器前，在浏览器端进行这些检查。HTML5可在输入标签中指定许多验证约束，如`<input type="number" name="credits" size="2" min="1" max="15">`确保输入参数“credits”是1到15之间的数字；更复杂的验证则需使用JavaScript，例如在HTML文档头部声明验证函数，在表单提交时调用，检查开始日期是否大于结束日期，验证失败显示警告框，成功则提交表单到服务器。
- **响应式用户界面（9.5.1.2）**：JavaScript可在浏览器内创建高度响应式用户界面，关键在于通过JavaScript动态修改显示的HTML代码。浏览器将HTML解析为内存中的树状结构（文档对象模型DOM ），JavaScript可修改该树状结构执行特定操作，如用户需输入多行数据时，可通过点击按钮调用JavaScript函数向表格添加行。由于不同浏览器的DOM模型存在差异，为避免问题，最好使用JavaScript库（如JQuery ）编写与浏览器无关的代码，库函数可检测使用的浏览器并生成相应JavaScript代码。JQuery等库提供菜单、标签、滑块等UI元素和自动完成等功能。HTML5标准支持丰富用户交互功能，如拖放、地理定位（经用户许可提供位置 ）、根据位置定制数据/界面，还支持服务器发送事件（SSE ）让后端在某些事件发生时通知前端。

### JavaScript与Ajax应用（9.5.1.3）
- **异步通信**：JavaScript广泛用于创建动态网页，借助Ajax技术，JavaScript程序可与Web服务器异步通信（在后台进行，不阻塞用户与浏览器的交互 ），获取数据并展示。
- **数据格式**：JavaScript对象表示法（JSON）是最常用的数据传输格式，不过XML等格式也会使用。应用服务器端的代码负责向JavaScript代码发送数据，由后者在浏览器上渲染数据。
- **自动完成功能示例**：以自动完成框为例，系统根据用户输入提供建议补全内容，对从大量值中选择的场景很有用。如使用jQuery库实现自动完成功能，通过`$("#name").autocomplete({ source: "/autocomplete_name" });`将自动完成函数与id为“name”的文本框关联，指定获取补全建议的Web服务地址。假设存在`/autocomplete_name`的Servlet，接收用户输入的部分内容参数`term`，返回匹配的学生/教师名称JSON数组。
- **数据展示示例**：通过jQuery的DataTables插件展示表格数据。当点击“Show details”按钮，`loadTableAsync()`函数创建URL调用`person_query_ajax` Servlet（假设其返回指定名称的学生或教师的ID、姓名和部门名称，编码为JSON ），并使用`myTable.ajax.url(url).load()`以异步方式填充表格行，即函数立即返回，数据获取后填充表格。

### Web服务（9.5.2）
- **定义与原理**：Web服务是可通过Web调用的应用组件，类似应用程序接口。Web服务请求使用HTTP协议发送，在应用服务器执行，结果返回调用程序。
- **实现方式**：
    - **REST方式**：较简单的表现层状态转移（REST）方式，通过标准HTTP请求调用Web服务函数，参数作为标准HTTP请求参数发送。应用服务器执行请求（可能涉及更新服务器数据库 ），生成并编码结果，最常用JSON格式返回（也会用XML ），请求方解析返回页面获取数据。在RESTful Web服务应用中，请求方常为浏览器中运行的JavaScript代码，用函数调用结果更新浏览器屏幕，如网页地图界面滚动时，JavaScript代码通过RESTful接口获取需新显示的地图部分并展示。
    - **复杂方式**：更复杂且较少使用的方式，参数和结果使用XML编码，用特殊语言明确定义Web API，在HTTP协议之上构建协议层。
- **应用场景**：RESTful Web服务应用广泛，前端应用常使用一个或多个此类服务执行后端活动，如基于Web的邮件系统、社交媒体网页、地图服务等用JavaScript渲染并通过后端Web服务获取和更新数据；移动应用也用Web服务获取和更新后端数据。Web服务在后端也越来越多地用于利用其他后端系统功能，如基于Web的存储系统（如Amazon S3、Google Cloud Storage、Microsoft Azure ）提供Web服务API用于存储和检索数据，支持大量数据存储和高并发操作，还有文本转语音、语音识别、视觉等Web服务API，让开发者能轻松构建包含语音和图像识别功能的应用。

### 离线操作（9.5.3）
- **需求场景**：许多应用希望支持客户端与应用服务器断开连接时的部分操作，如学生在笔记本电脑断网时填写申请表，联网后保存；邮件客户端作为Web应用，用户断网时撰写邮件，联网后发送。构建此类应用需客户端机器的本地存储。
- **HTML5本地存储支持**：HTML5标准支持本地存储，可通过JavaScript访问。用`if (typeof(Storage) !== "undefined")`检查浏览器是否支持本地存储，若支持，可使用`localStorage.setItem(key, value)`存储数据、`localStorage.getItem(key)`获取数据、`localStorage.deleteItem(key)`删除数据。为避免过度存储，浏览器通常限制网站最多存储一定量数据，默认最大值一般为5兆字节。该接口仅支持键值对存储/检索，检索需提供键，否则需扫描所有键值对。
- **IndexedDB**：HTML5支持IndexedDB，允许存储带多属性索引的JSON对象，支持模式版本，开发者可提供代码将数据从一个模式版本迁移到下一个版本，满足应用存储多属性索引元组、基于任意属性值高效访问数据的需求。

### 移动应用平台（9.5.4）
- **平台概述**：移动应用（Mobile apps）应用广泛，是众多用户的主要用户界面，目前最常用的移动平台是Android和iOS 。它们都提供构建图形用户界面（GUI）应用的方式，GUI具备菜单、列表、按钮等标准功能，还能展示文本、图像和视频。
- **移动应用优势**：可下载存储后使用，用户能在高速网络下载，在低速网络使用；相比Web应用，更适配小屏幕设备，可编译为机器代码，功耗更低；能本地存储数据，支持离线使用；有完善授权模型，经用户授权可使用位置、相机等设备功能。
- **跨平台开发挑战与框架**：Android和iOS平台不兼容，开发者需为两平台分别编码，为解决此问题，出现了React Native（基于JavaScript ）和Flutter（基于Dart语言 ）等框架，支持跨平台开发，使大部分应用代码可通用，部分功能可针对不同平台定制。

### 渐进式Web应用（PWA）
- **特点与优势**：结合移动应用和Web应用优点，使用JavaScript和HTML5开发，专为移动设备定制。关键特性包括HTML5支持本地数据存储，实现离线使用；支持JavaScript代码编译（限制符合特定语法 ），通常即时编译，确保在移动设备上CPU和能源高效执行；利用HTML5服务工作线程（service workers）在浏览器后台运行脚本，实现本地存储与Web服务的后台同步操作，或接收/推送后端服务通知；可获取设备位置（经用户授权 ），使用位置信息，应用前景广泛，可能取代部分移动应用使用场景。

>日期：4.17

### 应用架构（9.6）
- **分层架构**：大型应用常分为几层，以应对复杂性。
    - **表示层/用户界面层**：处理用户交互，同一应用可能有多个版本，对应不同界面（如Web浏览器、手机界面 ）。很多实现中，该层基于模型 - 视图 - 控制器（MVC）架构进一步细分。模型对应业务逻辑层；视图定义数据展示方式，同一模型可因访问应用的软件/设备不同有不同视图；控制器接收事件（用户操作 ），对模型执行操作并返回视图给用户，MVC架构在许多Web应用框架中应用。
    - **业务逻辑层**：提供数据和数据操作的高级视图，实现业务规则（如学生选课前需完成先修课程并缴纳学费 ），包含工作流（描述涉及多个参与者任务的处理方式，如大学申请流程及错误处理 ）。
    - **数据访问层**：在业务逻辑层和底层数据库间提供接口。很多应用业务逻辑层用面向对象语言编码和数据模型，底层是关系数据库，此时数据访问层提供从业务逻辑的面向对象数据模型到数据库关系模型的映射。
- **业务逻辑层（9.6.1）**：以大学管理应用为例，提供学生、教师等实体抽象及招生、选课等操作，确保满足业务规则，包含工作流及工作流管理中的错误处理。
- **数据访问层与对象关系映射（9.6.2）**：
    - **简单场景与问题**：业务逻辑层与数据库数据模型相同时，数据访问层隐藏数据库接口细节；业务逻辑层用面向对象语言时，自然将数据建模为对象，但早期需手动编写代码在数据库与对象间转换，繁琐易错。虽曾有面向对象数据库，但因技术和商业原因未获商业成功。
    - **对象关系映射（ORM）方案**：使用传统关系数据库存储数据，自动化数据与内存对象的映射及反向映射，开发出多种对象关系映射系统，如Hibernate和Django ORM 。
    - **Hibernate ORM（9.6.2.1）**：广泛用于Java对象到关系的映射，实现Java持久化API（JPA），在映射文件指定Java类到关系的映射，如Student类到student关系的映射，数据库信息在属性文件指定。通过打开会话连接数据库，可存储、检索Java对象，支持映射对象间关系（如student和section的takes关系 ），提供基于SQL设计的HQL查询语言，也允许直接用SQL编写复杂查询。
    - **Django ORM（9.6.2.2）**：Python语言中流行的ORM，Django框架的ORM组件是其中之一（另一个流行的是SQLAlchemy ），定义模型类（如Student和Instructor ）及字段类型，可建模多对多关系（如advisor关系 ）。

### Django ORM 相关
- **模型定义**：在Django中可定义模型类，如`Student`和`Instructor` 。`Student`类包含`id`（主键，字符类型，最大长度5 ）、`name`（字符类型，最大长度20 ）、`dept_name`（字符类型，最大长度20 ）、`tot_cred`（十进制类型，最大位数3，小数位数0 ）等字段；`Instructor`类包含`id`（主键，字符类型，最大长度5 ）、`name`（字符类型，最大长度20 ）、`dept_name`（字符类型，最大长度20 ）、`salary`（十进制类型，最大位数8，小数位数2 ）、`advisees`（多对多字段，关联`Student`模型，反向关系名为`advisors` ）等字段。
- **模型使用示例**：以`person_query_model`视图函数为例，可通过`Student.objects.filter(name=personname)`获取指定姓名的学生对象，通过`student.advisors.all()`获取学生的所有导师对象；对`Instructor`模型操作类似，可通过`Instructor.objects.filter(name=personname)`获取指定姓名的教师对象，通过`instructor.advisees.all()`获取教师指导的所有学生对象，展示了从Python语言直接访问数据库对象，无需使用SQL 。
- **数据库迁移工具**：Django提供`migrate`工具，可根据模型创建数据库关系。当模型版本更新，数据库已有旧版本时，该工具能生成SQL代码将现有数据从旧数据库模式迁移到新数据库模式，也可从现有数据库模式创建Django模型。

### 应用性能优化（9.7）
- **缓存减少开销（9.7.1）**：
    - **连接池技术**：为降低每次处理用户请求时创建新JDBC连接的开销（创建新连接可能需几毫秒 ），使用连接池（connection pooling）方法。连接池管理器（通常是应用服务器一部分 ）创建一组打开的ODBC/JDBC连接。处理用户请求的代码（如Servlet ）从连接池请求连接，处理完后归还。连接池无闲置连接时可新建连接（注意不超过数据库系统支持的最大并发连接数 ），长时间未使用的连接可由管理器关闭。很多应用服务器和新的ODBC/JDBC驱动程序提供内置连接池管理器，创建连接池通常需使用数据库连接细节（如机器、端口、数据库、用户ID和密码等 ）创建`DataSource`对象，通过`getConnection()`方法从连接池获取连接，关闭连接时将其归还池。
    - **查询结果缓存**：某些请求可能重复提交相同查询到数据库，缓存早期查询结果并重用可大幅降低与数据库的通信成本，只要数据库中查询结果未变。一些Web服务器支持查询结果缓存，应用代码也可实现。
    - **网页缓存**：缓存响应请求的最终网页可进一步降低成本。若新请求参数与之前请求完全相同，且结果网页在缓存中，可重用网页避免重新计算。也可在网页片段级别缓存，再组装成完整网页。缓存的查询结果和网页属于物化视图形式，数据库数据变化时，需丢弃、重新计算或增量更新缓存结果。部分数据库系统（如Microsoft SQL Server ）为应用服务器提供注册查询并在查询结果变化时获取通知的方式，确保应用服务器缓存的查询结果是最新的。
    - **内存缓存系统**：常用内存缓存系统有`memcached`和`Redis` ，它们允许应用以键值对形式存储和检索数据，类似哈希映射数据结构，可将数据存储在主内存中，并提供对不常用数据的缓存逐出功能。以`memcached`为例，可使用`memcached.add(key, data)`存储数据，`memcached.fetch(key)`获取数据。应用获取数据时先检查缓存，缓存未命中则查询数据库并将数据存入缓存。客户端可连接多个`memcached`实例（可能运行在不同机器上 ），决定数据存储位置由客户端代码负责。`memcached`不支持缓存数据自动失效，应用需跟踪数据库变化并对受影响键值进行更新（`memcached_set(key, newvalue)` ）或删除（`memcached_delete(key)` ）操作，`Redis`功能类似，二者都提供多种语言的API。
- **并行处理（9.7.2）**：处理高负载常用方法是使用大量并行运行的应用服务器，每个处理部分请求。Web服务器或网络路由器将客户端请求路由到其中一个应用服务器，同一客户端会话的所有请求必须发送到同一应用服务器（因服务器维护客户端会话状态 ，如将来自同一IP地址的请求路由到同一服务器 ），底层数据库由所有应用服务器共享，保证用户看到一致的数据库视图。此架构虽能避免应用服务器成为瓶颈，但无法防止数据库成为瓶颈。为避免数据库过载，应用设计者常使用缓存技术减少对数据库的请求，处理大量数据或高查询负载时使用并行数据库系统（相关内容在第21 - 23章 ），通过Web服务API访问的并行数据存储系统在面向大量用户的应用中也很受欢迎。

### 应用安全（9.8）
- **SQL注入（9.8.1）**：SQL注入攻击中，攻击者让应用执行其创建的SQL查询。例如应用将用户输入直接与SQL查询拼接提交到数据库就存在安全漏洞。如`String query = "select * from student where name like '%" + name + "%' "` ，攻击者可输入类似`'; <some SQL statement>; -- '`的字符串，使应用执行包含恶意SQL语句的查询，绕过应用代码的安全措施对数据库进行任意操作。避免此类攻击最好使用预编译语句（prepared statements ）执行SQL查询，设置预编译查询参数时，JDBC会自动添加转义字符，防止用户提供的引号终止字符串。

### 跨站脚本攻击（XSS）与跨站请求伪造（XSRF/CSRF）（9.8.2）
- **攻击原理**：
    - **XSS攻击**：允许用户输入文本（如评论、姓名 ）并展示给其他用户的网站易受XSS攻击。恶意用户输入客户端脚本语言（如JavaScript、Flash ）代码代替有效名称或评论，浏览器执行该脚本，可发送私人Cookie信息给恶意用户，或在用户已登录的其他网站执行操作。
    - **XSRF/CSRF攻击**：通过类似`<img src="https://mybank.com/transfermoney?amount=1000&toaccount=14523">`的代码，在无需脚本的情况下，利用网站URL接受特定参数执行操作（如转账 ）的特性发起攻击。
- **防护措施**：
    - **防止网站被用于发起攻击**：最简单方法是禁止用户输入文本中包含任何HTML标签，可使用检测或去除HTML标签的函数，防止脚本展示给其他用户；在需要HTML格式化的情况下，使用解析文本并允许有限HTML结构、禁止危险结构的函数，但要注意图像包含等潜在风险。
    - **保护网站免受其他网站发起的攻击**：
        - 利用HTTP协议检查页面访问的`referer`（即用户点击链接进入当前页面的来源页面URL ），确保来源URL是同一网站页面，防止来自其他页面的XSS攻击。
        - 除使用Cookie识别会话外，将会话限制在最初认证的IP地址，使恶意用户即使获取Cookie也无法从不同计算机登录。
        - 避免使用GET方法执行任何更新操作，遵循HTTP标准中GET方法不应执行更新的规定，防止类似`<img src...>`的攻击。
        - 使用Django等Web应用框架时，利用框架提供的XSRF/CSRF保护机制。

### 密码泄露（9.8.3）
- **问题**：应用开发者常面临在应用代码中以明文存储密码的问题，如JSP脚本常包含明文密码，若脚本存储在Web服务器可访问目录，外部用户可能获取脚本源代码及数据库账户密码。
- **解决方法**：
    - 许多应用服务器提供机制以加密形式存储密码，服务器在将密码传递给数据库前解密，但解密密钥若暴露则此方法失效。
    - 部分数据库系统将数据库访问限制在给定的一组互联网地址（通常是运行应用服务器的机器 ），拒绝其他地址的连接尝试，这样恶意用户即使获取数据库密码，未登录应用服务器也无法造成损害。

### 应用级认证（9.8.4）
- **认证定义**：认证是验证连接到应用的人或软件身份的任务。
- **认证方式**：
    - **简单密码认证**：用户连接应用时需提供秘密密码，但密码易被猜测或在网络传输中被嗅探（未加密时 ），对于关键应用（如网上银行账户 ）需更可靠方案，加密是更可靠认证方案的基础。
    - **双因素认证**：使用两个独立因素识别用户，且两个因素不应有共同漏洞。常见方案中密码作为第一因素，第二因素包括：
        - **智能卡或加密设备**：通过USB接口连接，基于加密技术进行认证。
        - **一次性密码设备**：每分钟生成新伪随机数，用户认证时需输入设备显示数字和密码，应用服务器生成相同序列伪随机数并验证，此方案要求设备和服务器时钟同步。
        - **短信验证码**：用户登录时向其注册手机号发送随机生成的一次性密码，用户需输入该密码和常规密码进行认证。
    - **中间人攻击防范**：双因素认证也可能受中间人攻击，用户被引导至伪造网站，输入密码后被用于认证原始应用。HTTPS协议用于向用户认证网站（防止连接到伪造网站 ），并加密数据防止中间人攻击。
    - **集中认证服务**：用户访问多个网站时，单独认证繁琐，集中认证服务允许用户向一个中心认证服务认证，其他网站和应用通过该服务认证用户，可使用同一密码访问多个站点。LDAP协议常用于实现组织内的集中认证点，除认证外，还可向应用提供用户信息（如姓名、邮箱、地址 ），避免在每个应用中单独输入。
    - **单点登录系统**：进一步允许用户一次性认证，多个应用通过认证服务验证用户身份，无需重新认证。相关认证协议如Kerberos，现已有Web应用实现。
    - **跨组织认证协议**：
        - **SAML（安全断言标记语言）**：用于不同安全域间交换认证和授权信息，实现跨组织单点登录。例如应用需向某大学学生提供访问权限，可将用户引导至大学认证服务，认证后应用获取用户身份及相关信息，用户密码等认证因素不暴露给应用，应用需信任大学认证服务。
        - **OpenID协议**：跨组织单点登录的替代方案，工作方式与SAML类似。
        - **OAuth协议**：允许用户通过共享授权令牌授权访问某些资源。

### 应用级授权（9.8.5）
- **SQL授权模型局限性**：SQL标准支持基于角色的灵活授权系统，但在典型应用管理用户授权中作用有限。例如，希望学生只能查看自己成绩，SQL无法实现，原因如下：
    - **缺乏终端用户信息**：随着Web发展，数据库访问主要来自Web应用服务器，终端用户在数据库通常无单独标识符，应用服务器所有用户可能对应数据库单个用户标识符，导致无法在SQL中指定上述场景的授权。
    - **缺乏细粒度授权**：SQL标准仅允许对整个关系、视图或关系/视图指定属性进行授权，若要实现学生只能查看自己成绩（需在单个元组级别授权 ）无法做到。虽可尝试为每个学生创建仅显示其成绩的视图，但为每个学生创建视图不切实际。

### 应用级授权（续）
- **数据库支持细粒度授权的优势**：若数据库直接支持细粒度授权，授权策略可在SQL级别指定和实施，可减少授权检查漏洞，即使部分应用接口遗漏授权检查，SQL级授权也能防止未授权操作执行。
- **行级授权机制**：部分数据库系统提供行级授权机制，如Oracle虚拟专用数据库（VPD），系统管理员可将函数与关系关联，函数返回谓词并添加到使用该关系的任何查询中。例如，对于`takes`关系，函数可返回`ID = sys_context.user_id()` ，该谓词添加到每个使用`takes`关系的查询的`where`子句中，使每个学生只能看到自己所选课程的元组。但添加谓词可能改变查询含义，如求所有课程平均成绩的查询会变成求该学生自己成绩的平均。PostgreSQL和Microsoft SQL Server也提供类似功能的行级授权支持 。
- **应用级授权的问题**：应用级授权常完全在应用中执行，绕过SQL授权功能。在应用中进行授权虽灵活，但存在问题：检查授权的代码与应用其他代码混合；通过应用代码实现授权难以确保无漏洞，可能因疏忽使应用程序未检查授权，让未授权用户访问机密数据；在大型系统中，检查所有应用程序是否进行必要授权检查任务艰巨，应用“攻击面”大，保护应用安全难度增加，实际中也发现过安全漏洞。

### 审计追踪（9.8.6）
- **定义与作用**：审计追踪是对应用数据所有更改（插入、删除、更新 ）的日志记录，包括执行更改的用户及更改时间等信息。应用安全受侵或有错误更新时，审计追踪可帮助查明发生的事情、确定执行操作的人，以及修复安全漏洞或错误更新造成的损害。例如学生成绩错误时，可通过审计日志确定成绩更新时间、方式及执行更新的用户，还可追踪该用户的其他更新以发现错误或欺诈性更新并纠正。
- **检测安全漏洞**：审计追踪还可检测用户账户被入侵的安全漏洞。例如用户每次登录时，可告知其近期审计追踪中该登录后进行的所有更新，若用户发现非自己执行的更新，可能账户已被入侵。
- **创建方式**：可通过在关系更新上定义适当触发器（使用识别用户名和时间的系统定义变量 ）创建数据库级审计追踪，但许多数据库系统提供更方便的内置机制。不同数据库系统创建审计追踪的细节不同，需参考数据库系统手册。数据库级审计追踪对应用通常不足，因其通常无法追踪应用终端用户，且更新记录处于关系元组的低级别，而非业务逻辑的高级别，所以应用通常创建更高级别的审计追踪，记录执行的操作、执行者、时间及请求来源IP地址等信息。
- **保护审计追踪**：相关问题是保护审计追踪本身不被破坏或删除。一种解决方法是将审计追踪复制到入侵者无法访问的其他机器，记录生成时立即复制；更可靠的方法是使用区块链技术（第26章介绍 ），将日志存储在多台机器上并使用哈希机制，使入侵者难以在不被察觉的情况下修改或删除数据。

### 隐私（9.8.7）
- **隐私问题背景**：个人数据在网上日益增多，人们越来越担心数据隐私，如个人医疗数据希望保密但又需提供给治疗医生和急救医疗人员。许多国家有隐私法，规定数据可披露的情况和对象，违反隐私法会面临刑事处罚，应用访问私人数据需谨慎设计以遵守法律。
- **聚合私人数据的应用与挑战**：聚合私人数据在检测药物副作用、流行病传播等任务中起重要作用，但需在不损害个人隐私的前提下提供给研究人员。例如医院提供患者姓名、出生日期和邮政编码，这些信息可能用于唯一识别患者，损害隐私。解决方法可以是提供出生年份而非具体日期及地址，为研究人员提供足够信息又不唯一识别个人。
- **网站数据收集与隐私偏好**：网站常收集地址、电话、邮箱、信用卡信息等个人数据用于交易，但客户可能不希望信息被其他组织获取，或希望部分信息（如信用卡号码 ）在一段时间后删除，防止安全漏洞时信息落入未授权者手中。许多网站允许客户指定隐私偏好，网站需确保尊重这些偏好。

### 加密及其应用（9.9）
- **加密定义与用途**：加密是将数据转换为不可读形式的过程，需解密过程还原。加密算法使用加密密钥加密，根据算法不同，可能需相同或不同的解密密钥解密。早期加密用于消息传输，如今广泛用于保护网络传输数据、存储敏感客户信息（如信用卡号码、姓名、指纹等 ），防止身份盗窃。许多国家和州法律要求存储敏感信息的数据库以加密形式存储，企业若不保护数据在数据被盗时可能承担刑事责任，加密是存储敏感信息应用的关键组成部分。
- **加密技术（9.9.1）**：
    - **简单加密技术缺陷**：简单加密技术可能无法提供足够安全性，例如用字母表中后一个字符替换每个字符的加密方式，未授权用户可能通过统计字符相对频率猜出加密方式。
    - **良好加密技术特性**：良好的加密技术应具备以下特性：授权用户加密和解密数据相对简单；不依赖算法保密性，而是依赖加密密钥；入侵者极难确定解密密钥，非对称加密中即使知道公钥也极难推断私钥 。
    - **对称密钥加密**：高级加密标准（AES）是对称密钥加密算法，2000年被美国政府采用，基于Rijndael算法，每次对128位数据块操作，密钥长度可为128、192或256位，通过一系列步骤打乱数据块中的位并与从加密密钥派生的“轮密钥”进行异或操作，解密时重新生成轮密钥并反转加密过程恢复原始数据。早期的数据加密标准（DES）曾被广泛使用。对称密钥加密方案要求通过安全机制向授权用户提供加密密钥，这是其主要弱点，安全性取决于密钥传输机制的安全性。
    - **公钥加密（非对称密钥加密）**：公钥加密是替代方案，基于公钥和私钥。每个用户  $U_i$ 有公钥 $E_i$ 和私钥 $D_i$  ，公钥公开，私钥仅用户知晓。用户 $U_1$ 用公钥 $E_1$ 加密数据，解密需私钥 $D_1$  。由于公钥公开，该方案可安全交换信息，如用户 $U_1$ 想与 $U_2$ 共享数据，可用 $U_2$ 的公钥 $E_2$ 加密， $U_2$ 用私钥 $D_2$ 解密 。

### 公钥加密（续）
- **公钥加密原理条件**：公钥加密要可行，需满足以下条件：存在高效算法测试一个数是否为素数；目前没有已知的高效算法来找出一个数的素因数。在该方案中，数据被视为整数集合。通过计算两个大素数 $P_1$ 和 $P_2$ 的乘积来创建公钥，私钥由这对素数 $(P_1, P_2)$ 组成。仅知道乘积 $P_1P_2$ 无法成功使用解密算法，必须知道 $P_1$ 和 $P_2$ 的值。由于公钥是 $P_1P_2$ ，未授权用户试图分解 $P_1P_2$ 来窃取数据，选择足够大（超过100位）的 $P_1$ 和 $P_2$ 可使分解成本极高（即使在最快的计算机上也需数年计算时间）。公钥加密细节及该技术特性的数学证明在文献注释中有参考。
- **混合加密方案**：公钥加密虽安全但计算成本高，广泛用于安全通信的混合方案如下：随机生成一个对称加密密钥（如基于AES），并使用公钥加密方案以安全方式交换该密钥，随后对传输的数据使用该对称密钥进行加密。
- **防范字典攻击**：加密小数值（如标识符或名称）时，因可能存在字典攻击（攻击者尝试加密每个可能值来匹配加密值）而变得复杂。即使加密密钥未公开，数据分布的统计信息也可能被用于推断加密值。防范字典攻击的方法是在加密前向值的末尾添加额外随机位（解密后移除），在AES中这些额外位称为初始化向量，在其他场景中称为盐值位，可有效防范字典攻击。

### 数据库中的加密支持（9.9.2）
- **加密级别**：
    - **磁盘块级加密**：许多文件系统和数据库系统支持数据加密，在数据库中，最低级别是对包含数据库数据的磁盘块进行加密，使用数据库系统软件可用的密钥。从磁盘检索块时，先解密再正常使用。这种磁盘块级加密可防范能访问磁盘内容但无法获取加密密钥的攻击者。
    - **属性级加密**：更高一级，关系的指定（或所有）属性可存储为加密形式，每个属性可能有不同加密密钥。如今许多数据库支持指定属性级、整个关系级或数据库中所有关系级的加密。指定属性加密通过仅加密包含敏感数据（如信用卡号码）的属性来最小化解密开销。加密还需添加额外随机位防止字典攻击。但数据库通常不允许对主键和外键属性加密，也不支持对加密属性建立索引。
- **密钥管理**：访问加密数据需解密密钥，可使用单个主加密密钥加密所有数据；在属性级加密中，不同属性使用不同加密密钥，不同属性的解密密钥可存储在文件或关系（常称为“钱包”）中，该文件或关系本身用主密钥加密。数据库连接需要访问加密属性时必须提供主密钥，否则无法访问加密数据。主密钥可存储在应用程序（通常在不同计算机上）或由数据库用户记住，在连接数据库时提供。
- **数据库级加密优势与替代方案**：数据库级加密的优点是时间和空间开销相对较低，且无需修改应用程序。例如，可保护笔记本电脑中的数据库数据不被盗取，备份数据库的人若无解密密钥也无法访问备份中的数据。替代方案是在数据发送到数据库之前进行加密，应用程序需在发送数据到数据库前加密，检索数据时解密。这种方法与数据库系统内加密不同，需要对应用程序进行大量修改。

### 加密与认证（9.9.3）
- **基于密码认证的缺陷与改进方案**：操作系统和数据库系统广泛使用基于密码的认证，但在网络环境下存在缺陷，窃听者可能嗅探网络传输中的密码。更安全的方案是挑战 - 响应系统，数据库系统向用户发送挑战字符串，用户使用秘密密码作为加密密钥对其加密并返回结果，数据库系统用相同秘密密码解密并与原始挑战字符串对比，验证用户真实性，确保密码不在网络中传输。
- **公钥在挑战 - 响应系统中的应用**：公钥系统也可用于挑战 - 响应系统，数据库系统使用用户公钥加密挑战字符串并发送给用户，用户用私钥解密并返回结果，数据库系统检查响应。该方案优点是不在数据库中存储秘密密码，避免系统管理员看到密码。但存储用户私钥存在风险，若计算机被攻破，私钥可能泄露给攻击者。智能卡可解决此问题，私钥存储在嵌入式芯片中，智能卡操作系统保证私钥不会被读取，但允许使用私钥对发送到卡的数据进行加密或解密。
- **数字签名（9.9.3.1）**：公钥加密的另一个有趣应用是数字签名，用于验证数据真实性，在电子环境中扮演物理文档上签名的角色。使用私钥对数据“签名”（即加密），签名后的数据可公开。任何人都可使用公钥解密数据来验证签名，但没有私钥无法生成签名数据，从而验证数据创建者身份。此外，数字签名还能确保不可否认性，即数据创建者事后声称未创建数据时（类似于声称未签署支票），可证明其创建了数据（除非私钥泄露给他人）。
- **数字证书（9.9.3.2）**：认证通常是双向过程，交互实体需相互认证，客户端与网站通信时也需防止恶意网站伪装。用户要确保与真实网站交互需获取网站公钥，但从网站获取公钥存在恶意网站提供虚假公钥的风险。数字证书系统可解决此问题，公钥由认证机构签名，认证机构的公钥广为人知，如根认证机构的公钥存储在标准网页浏览器中，可验证证书真实性。为减轻根认证机构创建证书的负担，采用多级系统，有一个或多个根认证机构及下级认证机构树，非根认证机构的数字证书由其父级颁发。认证机构A颁发的数字证书包含公钥 $K_A$ 和加密文本E，使用 $K_A$ 可解码E，E包含证书颁发对象名称及其公钥 $K_c$  ，若A不是根认证机构，E还包含其父级颁发给A的数字证书。验证证书时，使用 $K_A$ 解密E获取对象名称，若A不是公钥已知的根认证机构，递归使用E中包含的数字证书验证 $K_A$  ，直至到达根认证机构。验证证书可建立特定网站的认证链，提供网站名称和经过认证的公钥。数字证书广泛用于向用户认证网站，防止恶意网站伪装。在HTTPS协议（HTTP协议的安全版本）中，网站向浏览器提供数字证书，用户接受证书后，浏览器使用提供的公钥加密数据。恶意网站虽能获取证书但无私钥，无法解密浏览器发送的数据，只有拥有相应私钥的真实网站才能解密。由于公钥/私钥加密和解密成本高于对称私钥，HTTPS在认证后创建一次性对称密钥，用于会话剩余时间的数据加密。数字证书也可用于用户认证，用户向网站提交包含公钥的数字证书，网站验证证书由受信任机构签名，然后在挑战 - 响应系统中使用用户公钥确保用户拥有相应私钥，从而认证用户。

### 总结（9.10）
- **应用架构发展**：自20世纪60年代起，使用数据库作为后端并与用户交互的应用程序就已存在，应用架构在此期间不断演变。如今，大多数应用程序使用网页浏览器作为前端，数据库作为后端，中间有应用服务器。
- **Web技术基础**：HTML可定义结合超链接和表单功能的界面，网页浏览器通过HTTP协议与Web服务器通信，Web服务器可将请求传递给应用程序并将结果返回给浏览器。
- **Web服务器与应用程序**：Web服务器执行应用程序以实现所需功能，Servlet是广泛使用的编写应用程序的机制，作为Web服务器进程的一部分运行以减少开销，还有许多服务器端脚本语言由Web服务器解释并提供应用程序功能。
- **客户端脚本语言**：有多种客户端脚本语言，JavaScript应用最为广泛，可在浏览器端提供更丰富的用户交互。
- **复杂应用架构**：复杂应用通常采用多层架构，包括实现业务逻辑的模型、控制器和用于显示结果的视图机制，还可能包括实现对象 - 关系映射的数据访问层。许多应用实现并使用Web服务，允许通过HTTP调用函数。
- **性能优化技术**：使用缓存（包括查询结果缓存和连接池）、并行处理等技术来提高应用性能。
- **应用安全注意事项**：应用开发人员必须密切关注安全问题，防范SQL注入攻击和跨站脚本攻击等。

>日期：4.19

### 课后习题选做  
### 9.13
**HTML代码**
```html
<!DOCTYPE html>
<html>

<body>
    <form action="SimpleServlet" method="post">
        <label for="n">输入一个值 n:</label><br>
        <input type="number" id="n" name="n" required><br>
        <input type="submit" value="提交">
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

        int n = Integer.parseInt(request.getParameter("n"));
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < n; i++) {
            result.append("*");
        }

        out.println("<html><body>");
        out.println("结果: " + result.toString());
        out.println("</body></html>");
    }
}
```
### 9.14
**HTML代码**
```html
<!DOCTYPE html>
<html>

<body>
    <form action="CountServlet" method="post">
        <label for="n">输入一个数字 n:</label><br>
        <input type="number" id="n" name="n" required><br>
        <input type="submit" value="提交">
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
    private static final String DB_URL = "jdbc:mysql://localhost:3306/your_database";
    private static final String DB_USER = "your_username";
    private static final String DB_PASSWORD = "your_password";

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        int n = Integer.parseInt(request.getParameter("n"));
        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD)) {
            // 检查该值之前提交的次数
            String checkQuery = "SELECT count_value FROM submission_counts WHERE value =?";
            try (PreparedStatement checkStmt = conn.prepareStatement(checkQuery)) {
                checkStmt.setInt(1, n);
                ResultSet rs = checkStmt.executeQuery();
                int count = 0;
                if (rs.next()) {
                    count = rs.getInt("count_value");
                }
                // 更新提交次数
                String updateQuery;
                if (count > 0) {
                    updateQuery = "UPDATE submission_counts SET count_value =? WHERE value =?";
                } else {
                    updateQuery = "INSERT INTO submission_counts (value, count_value) VALUES (?, 1)";
                }
                try (PreparedStatement updateStmt = conn.prepareStatement(updateQuery)) {
                    if (count > 0) {
                        updateStmt.setInt(1, count + 1);
                        updateStmt.setInt(2, n);
                    } else {
                        updateStmt.setInt(1, n);
                    }
                    updateStmt.executeUpdate();
                }
                out.println("<html><body>");
                out.println("值 " + n + " 之前已被提交 " + (count + 1) + " 次。");
                out.println("</body></html>");
            }
        } catch (SQLException e) {
            e.printStackTrace();
            out.println("<html><body>数据库操作出错。</body></html>");
        }
    }
}
```
假设数据库中有表`submission_counts`，结构为`(value INT, count_value INT)`。

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
    private static final String DB_URL = "jdbc:mysql://localhost:3306/your_database";
    private static final String DB_USER = "your_username";
    private static final String DB_PASSWORD = "your_password";

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String username = request.getParameter("username");
        String password = request.getParameter("password");

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD)) {
            String query = "SELECT user_id FROM users WHERE username =? AND password =?";
            try (PreparedStatement stmt = conn.prepareStatement(query)) {
                stmt.setString(1, username);
                stmt.setString(2, password);
                ResultSet rs = stmt.executeQuery();
                if (rs.next()) {
                    HttpSession session = request.getSession();
                    session.setAttribute("userid", rs.getInt("user_id"));
                    out.println("<html><body>认证成功。</body></html>");
                } else {
                    out.println("<html><body>认证失败。</body></html>");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
            out.println("<html><body>数据库操作出错。</body></html>");
        }
    }
}
```
假设数据库中有表`users`，结构为`(user_id INT, username VARCHAR, password VARCHAR)`。

### 9.16
**SQL注入攻击解释**
SQL注入攻击是攻击者通过在应用程序的输入字段（如表单输入、URL参数等）中插入恶意SQL代码，从而修改或执行非预期的SQL查询。例如，在登录表单的用户名或密码输入框中输入恶意SQL语句，若应用程序未对输入进行正确处理，可能导致攻击者绕过认证（如输入`' OR '1'='1`作为密码），或者获取、修改、删除数据库中的敏感数据。

**防范措施**
- **输入验证**：对用户输入进行严格验证，确保输入符合预期格式，拒绝非法字符和恶意代码。例如，对于数字输入，只允许数字字符；对于文本输入，限制长度并过滤特殊SQL字符（如单引号、分号等）。
- **使用预编译语句**：在执行SQL查询时，使用预编译语句（如Java中的`PreparedStatement`）。预编译语句会将SQL语句和参数分开处理，参数会被自动转义，防止恶意代码被当作SQL指令执行。
- **最小权限原则**：数据库用户应仅拥有执行必要操作的权限，避免给应用程序使用的数据库账户赋予过高权限，减少攻击造成的损失。

### 9.17
```
// 创建连接池函数
function createPool(databaseConnectionString, databaseUserName, databasePassword) {
    pool = [];
    // 这里可以根据需要设置连接池的大小等参数
    for (i = 0; i < poolSize; i++) {
        connection = createDatabaseConnection(databaseConnectionString, databaseUserName, databasePassword);
        pool.push(connection);
    }
    return pool;
}

// 从连接池请求连接函数
function requestConnection(pool) {
    if (pool.length > 0) {
        return pool.pop();
    } else {
        // 可以选择等待连接可用或者抛出异常
        waitForConnection(); 
        return requestConnection(pool);
    }
}

// 将连接释放回连接池函数
function releaseConnection(pool, connection) {
    pool.push(connection);
}

// 关闭连接池函数
function closePool(pool) {
    for (connection in pool) {
        closeDatabaseConnection(connection);
    }
    pool = [];
}
```
### 9.18
- **CRUD**：是Create（创建）、Read（读取）、Update（更新）和Delete（删除）的缩写，是对数据库中数据进行的基本操作。在应用程序开发中，常涉及对数据库记录的增、删、改、查操作，这些操作是数据管理的基础。
- **REST**：即Representational State Transfer（表述性状态转移），是一种软件架构风格。RESTful API使用HTTP协议的标准方法（如GET、POST、PUT、DELETE ）来对应CRUD操作，以资源为中心，每个资源有唯一的URI（统一资源标识符），强调无状态性（即每个请求都包含足够信息来理解和处理，不依赖之前请求的状态），易于实现和扩展，在Web服务开发中广泛应用。

### 9.19
**判断网站使用Ajax的特征**
- **无页面刷新更新内容**：当用户进行某些操作（如点击按钮、滚动页面等）时，页面部分内容更新但整体页面不刷新，例如在电商网站上切换商品分类时，商品列表动态更新但页面没有重新加载。
- **自动填充或实时搜索提示**：在搜索框中输入内容时，会实时显示相关的搜索建议或自动填充内容，且这个过程没有触发页面的完整刷新。

**使用Ajax的网站示例（需实际验证）**
- Google搜索：输入搜索关键词时，会实时显示相关的搜索建议。
- GitHub：在仓库页面切换分支或标签时，文件列表会动态更新而无需页面刷新。
- Twitter：加载更多推文时，页面底部内容动态更新而不刷新整个页面。

### 9.20
**a. XSS攻击定义**
XSS（Cross-Site Scripting，跨站脚本攻击）是攻击者在网页中注入恶意脚本（如JavaScript），当用户浏览该网页时，恶意脚本在用户浏览器中执行，可能窃取用户的Cookie、会话信息，或在用户已登录的网站上执行非授权操作。例如，攻击者在网站的评论区、输入框等允许用户输入的地方插入恶意脚本，其他用户查看这些内容时脚本就会被执行。

**b. referer字段检测XSS攻击原理**
HTTP协议中的`referer`字段记录了用户访问当前页面的来源页面URL。在XSS攻击中，如果攻击者是通过在其他网站嵌入恶意脚本，诱导用户访问该恶意页面从而发起对目标网站的攻击，那么目标网站在处理请求时可以检查`referer`字段。如果`referer`指向的不是目标网站的合法页面，而是一个可疑的外部网站，就可能存在XSS攻击风险。通过这种方式可以检测到部分由外部恶意网站发起的XSS攻击。

### 9.21
**多因素认证定义**
多因素认证（Multifactor Authentication，MFA）是一种使用两个或多个独立因素来验证用户身份的方法。这些因素通常分为三类：用户知道的东西（如密码）、用户拥有的东西（如手机、智能卡）和用户本身的特征（如指纹、虹膜等生物特征 ）。

**防范密码被盗用原理**
仅使用密码认证时，一旦密码被盗，攻击者就能轻易冒充用户。而多因素认证增加了额外的验证因素，即使密码被盗，攻击者没有其他认证因素（如手机验证码、生物特征等）也无法通过认证，从而大大提高了账户的安全性，降低了因密码被盗而导致账户被非法访问的风险。

### 9.22
假设存在`courses`表（包含`course_id`等字段）、`instructors`表（包含`instructor_id`等字段）和`takes`表（包含`course_id`、`instructor_id`等相关字段 ）。
**a. 生成谓词**
```sql
SELECT *
FROM takes
WHERE instructor_id = (SELECT instructor_id
                       FROM instructors
                       WHERE user_id = sys_context.user_id());
```
**b. 查询示例（获取教师所授课程的学生信息，原始查询为获取所有学生信息）**
原始查询：
```sql
SELECT *
FROM students;
```
添加谓词后的查询：
```sql
SELECT *
FROM students
WHERE course_id IN (SELECT course_id
                    FROM takes
                    WHERE instructor_id = (SELECT instructor_id
                                           FROM instructors
                                           WHERE user_id = sys_context.user_id()));
```
**c. 查询示例（获取所有课程信息，添加谓词后只获取教师所授课程信息）**
原始查询：
```sql
SELECT *
FROM courses;
```
添加谓词后的查询：
```sql
SELECT *
FROM courses
WHERE course_id IN (SELECT course_id
                    FROM takes
                    WHERE instructor_id = (SELECT instructor_id
                                           FROM instructors
                                           WHERE user_id = sys_context.user_id()));
```

### 9.23
- **数据安全保护**：加密存储在数据库中的数据可以防止数据在被未授权访问时被直接读取。例如，即使攻击者能够获取数据库文件或通过漏洞访问数据库，没有解密密钥也无法理解加密后的数据内容，从而保护了敏感信息（如用户密码、信用卡信息等）不被泄露和滥用。
 - **符合法规要求**：许多国家和地区对某些特定类型的数据（如个人医疗数据、金融数据等）有严格的隐私保护法规，要求对这些数据进行加密存储。对数据库数据加密有助于应用程序满足相关法律要求，避免因数据泄露引发的法律责任和声誉损失。

### 9.24
**a. 定义触发器及`takes_trail`关系模式**
假设`takes`关系模式为`(student_id, course_id, grade)` ，创建用于记录审计追踪信息的`takes_trail`关系，其模式定义如下：
```sql
CREATE TABLE takes_trail (
    id SERIAL PRIMARY KEY,
    operation VARCHAR(6) CHECK (operation IN ('INSERT', 'UPDATE', 'DELETE')),
    user_id VARCHAR,
    timestamp TIMESTAMP,
    old_student_id VARCHAR,
    old_course_id VARCHAR,
    old_grade VARCHAR,
    new_student_id VARCHAR,
    new_course_id VARCHAR,
    new_grade VARCHAR
);
```
**插入操作触发器**
```sql
CREATE TRIGGER takes_insert_trigger
AFTER INSERT ON takes
FOR EACH ROW
EXECUTE FUNCTION log_takes_insert();

CREATE OR REPLACE FUNCTION log_takes_insert() RETURNS trigger AS $$
BEGIN
    INSERT INTO takes_trail (operation, user_id, timestamp, new_student_id, new_course_id, new_grade)
    VALUES ('INSERT', user_id(), current_timestamp, NEW.student_id, NEW.course_id, NEW.grade);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
**更新操作触发器**
```sql
CREATE TRIGGER takes_update_trigger
AFTER UPDATE ON takes
FOR EACH ROW
EXECUTE FUNCTION log_takes_update();

CREATE OR REPLACE FUNCTION log_takes_update() RETURNS trigger AS $$
BEGIN
    INSERT INTO takes_trail (operation, user_id, timestamp, old_student_id, old_course_id, old_grade, new_student_id, new_course_id, new_grade)
    VALUES ('UPDATE', user_id(), current_timestamp, OLD.student_id, OLD.course_id, OLD.grade, NEW.student_id, NEW.course_id, NEW.grade);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
**删除操作触发器**
```sql
CREATE TRIGGER takes_delete_trigger
AFTER DELETE ON takes
FOR EACH ROW
EXECUTE FUNCTION log_takes_delete();

CREATE OR REPLACE FUNCTION log_takes_delete() RETURNS trigger AS $$
BEGIN
    INSERT INTO takes_trail (operation, user_id, timestamp, old_student_id, old_course_id, old_grade)
    VALUES ('DELETE', user_id(), current_timestamp, OLD.student_id, OLD.course_id, OLD.grade);
    RETURN OLD;
END;
$$ LANGUAGE plpgsql;
```
这里使用的是PostgreSQL的语法，不同数据库系统在语法细节上可能有差异。

**b. 对于恶意管理员操作的审计追踪问题**
不能保证恶意数据库管理员（或获取管理员密码的人）的更新操作会记录在审计追踪中。因为数据库管理员拥有对数据库的最高权限，包括对触发器和审计追踪表的操作权限。恶意管理员可以禁用或修改这些触发器，使其不记录自己的操作；也可以直接修改审计追踪表`takes_trail`，删除或篡改与自己相关的记录。要解决这个问题，可以考虑将审计追踪数据备份到一个受保护的外部存储（如只读设备或由其他安全机制保护的系统 ），或者使用区块链等技术来确保审计数据的不可篡改。 

### 9.25
HTTPS协议使用数字证书来验证网站真实性的过程如下：
- **证书颁发与存储**：网站所有者向认证机构（CA）申请数字证书，CA验证网站所有者身份后，使用自己的私钥对网站的公钥等信息进行签名，生成数字证书。标准网页浏览器中会存储根认证机构的公钥。
- **证书传输与验证**：当用户在浏览器中输入`https://mybank.com` ，浏览器向网站请求数字证书，网站将数字证书发送给浏览器。浏览器使用存储的根认证机构公钥来验证证书上CA的签名。如果签名验证通过，说明证书是由受信任的CA颁发的。
- **获取公钥与认证**：验证签名后，浏览器从证书中提取网站的公钥。浏览器可以使用该公钥与网站进行安全通信（如加密数据），确保通信是与真实的`mybank.com`网站进行，而不是被黑客篡改的站点。如果黑客的站点没有有效的数字证书（即没有经过受信任CA签名的证书 ），浏览器会提示用户该网站存在安全风险，阻止用户与该站点进行不安全的交互，从而防止用户在黑客网站上输入用户名和密码等敏感信息。 

### 9.26
**挑战 - 响应系统认证解释**
挑战 - 响应系统是一种认证机制，在该机制中，数据库系统（或服务器）向用户发送一个随机生成的挑战字符串。用户使用自己的秘密密码作为加密密钥，对该挑战字符串进行加密，并将加密后的结果返回给数据库系统。数据库系统使用相同的秘密密码对用户返回的结果进行解密，并与原始挑战字符串进行比对。如果比对一致，则证明用户拥有正确的密码，认证通过。

**比传统密码认证更安全的原因**
- **防止密码嗅探**：在传统基于密码的认证系统中，密码在网络中传输时，若被窃听者嗅探到，攻击者就能使用该密码登录系统。而在挑战 - 响应系统中，密码并不在网络中直接传输，攻击者无法通过嗅探网络流量获取密码，大大降低了密码被盗用的风险。
- **增加破解难度**：即使攻击者截获了挑战字符串和加密后的响应，由于不知道用户使用的秘密密码（加密密钥），也很难破解出密码。这增加了攻击者破解认证的难度，提高了系统的安全性。   


  
