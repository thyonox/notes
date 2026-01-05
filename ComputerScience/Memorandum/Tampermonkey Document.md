要在tampermonkey中加载外部CSS，需要允许tampermonkey访问文件网址，并且在tampermonkey的设置中，外部的更新间隔设置为每天
# 用户脚本头
## `@name`
脚本的名称。国际化通过添加命名区域来完成。
```jsx
// @name    A test
// @name:de Ein Test
// @name:zh-CN 一个测试
```
## `@namespace`
- 作用：定义了脚本所属的命名空间，通常是一个唯一的字符串，用于区分不同作者或组织的脚本。
- 常见用法：通常使用作者的网站、邮箱、GitHub 地址或任何唯一标识符作为命名空间。
- 具体功能：
	- 避免冲突：如果两个脚本有相同的 `@name`，Tampermonkey 会通过 `@namespace` 来区分它们，确保用户安装的是正确的脚本。
	- 组织管理：开发者可以用命名空间对自己的脚本进行分组，便于管理和识别。
	- 用户信任：通过设置一个可识别的命名空间（如开发者的网站），用户可以更容易信任脚本的来源。
```jsx
// @namespace    <http://yourdomain.com>
```
## `@copyright`
脚本的版权声明。明确指明谁是脚本的版权所有者，以及该脚本是否受到版权法的保护。
```jsx
// @copyright    2024, Your Name
```
## `@license`
脚本的许可证。
- MIT：允许几乎所有的使用方式，只需在分发时附带原始许可证文件。
- GPL (GNU General Public License)：强制性开源，任何基于 GPL 许可证的代码修改或衍生作品也必须以 GPL 许可证发布。
- Apache 2.0：允许使用、修改和分发，包含对专利权的保障，适合企业和商业项目。
- BSD：类似于 MIT 许可证，但有一些额外的条款，允许再分发和修改。
- Creative Commons：针对创意作品（如文本、图片、音乐等），允许不同的使用方式（如署名、非商业、相同方式共享等）。
```jsx
// @license      MIT
```
## `@version`
脚本的版本。用于更新检查。版本号有多种形式，通常采用三段数字形式。
- 主版本号（Major）：大版本更新，或不兼容的API更新。
- 次版本号（Minor）：添加向后兼容的更新。
- 修订号（Patch）：添加向后兼容的错误修复。
```jsx
// @version      1.2.0
```
## `@Description`
脚本的描述。国际化通过添加命名区域来完成。
```jsx
// @description    Test script
// @description:zh-CN 测试脚本
```
## `@icon`
低分辨率的脚本图标。可以是任何标准图片格式。有相同作用的还有 `@iconURL` 和 `@defaulticon`。
`@iconURL` 是 Tampermonkey 在旧版本的使用的字段，现在多用 `@icon` 替代。
`@defaulticon` 是在没有指定图标情况下，提供一个默认的图标。
```jsx
// @icon    <https://example.com/icon.png>
// @iconURL    <https://example.com/icon.png>
// @defaulticon    <https://example.com/default-icon.png>
```
## `@icon64`
$64*64$像素大小的图标。相同作用的还有 `@icon64URL`。
`@icon64URL` 是 Tampermonkey 在旧版本的使用的字段，现在多用 `@icon64` 替代。
```jsx
// @icon64    <https://example.com/icon64.png>
```
## `@grant`
用于声明脚本所需要的特殊权限。这些权限包括 Tampermonkey 提供的 `GM_*` 和` GM.*`  函数、`unsafeWindow` 对象和一些窗口函数。如果在脚本中使用这些特殊权限，则必须通过 `@grant` 声明，加入白名单，否则 Tampermonkey 会限制用户脚本使用这些特殊权限。
```jsx
// @grant GM_setValue
// @grant GM_getValue
// @grant GM.setValue
// @grant GM.getValue
// @grant GM_setClipboard
// @grant unsafeWindow
// @grant window.close
// @grant window.focus
// @grant window.onurlchange
```
## `@author`
脚本的作者。
```jsx
//**@author        Maktub**
```
## `@homepage`
脚本的主页。在脚本管理面板显示主页链接。相同作用的还有 ****`@homepageURL`**, `@website`**,** `@source` **。**
`@homepageURL`是Tampermonkey在旧版本的使用的字段，现在多用`@homepage`替代。
`@website`指定与脚本相关的网站的字段，常用于更广义的“开发者网站”或“项目网站”。
`@source` 指向脚本的源码存储位置。
另外，如果`@namespace`是以`http://`或`https://`开头的，也可以用于指向脚本主页。不过优先级低于上述四个标签，只有在这四个标签都没有定义时，才能发挥作用。
```jsx
// @homepage    <https://example.com/my-script-homepage>
// @homepageURL    <https://example.com/my-script-homepage>
// @website    <https://example.com/my-website>
// @source    <https://github.com/myusername/myscript>
```
## `@antifeature`
用于声明脚本中可能包含的用户不太期望的功能（反特性）。一些托管脚本的平台（如 GreasyFork）要求开发者声明反特性。这些反特性将展示在脚本托管平台的脚本主页。国际化通过添加命名区域来完成。
常见的反特性有：
- `ads`（广告）
- `tracking`（跟踪）
- `miner`（挖矿）
- `paywall`（付费墙）
- `sponsored`（赞助）
- `referral-link`（推荐链接）
- `membership`（会员制）
- `data-sale`（数据出售）
- `popups`（弹出窗口）
- `cookie`（cookie使用）
```jsx
// @antifeature ads            we show you ads
// @antifeature:zh_CN ads      我们将向你展示广告
// @antifeature tracking
// @antifeature referral-link
```
## `@require`
用于引入第三方库或其他外部 JavaScript 文件。多个文件将按顺序加载，确保依赖关系的正确安排。Tampermonkey 会缓存这些js 文件。
```jsx
// @require <https://code.jquery.com/jquery-2.1.4.min.js>
// @require <https://code.jquery.com/jquery-2.1.3.min.js#sha256=23456>...
// @require <https://code.jquery.com/jquery-2.1.2.min.js#md5=34567>...,sha256=6789...
// @require tampermonkey://vendor/jquery.js
// @require tampermonkey://vendor/jszip/jszip.js
```
## `@resource`
用于将图片、CSS、HTML、JSON 等资源引入脚本。Tampermonkey 会将其缓存为 Base64 格式。使用这些资源时，需要通过Tampermonkey API 根据资源名称来引用。
- `GM_getResourceURL` 获取资源URL
    ```jsx
    // @resource     logo <https://example.com/logo.png>
    let logoURL = GM_getResourceURL("logo");
    let img = document.createElement("img");
    img.src = logoURL;
    document.body.appendChild(img);
    ```
- `GM_getResourceText` 获取资源文本。
    ```jsx
    // @resource     customCSS <https://example.com/styles.css>
    let cssContent = GM_getResourceText("customCSS");
    let style = document.createElement("style");
    style.textContent = cssContent;
    document.head.appendChild(style);
    ```
## `@include`
限定脚本的执行范围。限制脚本仅在符合特定URL模式的网页上运行。对于URL的匹配可以通过通配符（`*`和`？`）匹配不同的网页。
- `*`：匹配多个字符，可以出现在域名前后。
- `?`：匹配单个字符。
`@include`匹配机制宽松，如`*://tmnk.net/*` 不止会匹配到`https://tmnk.net/*` 还可以匹配到如`https://example.com/?<http://tmnk.net/`> 等页面。
```jsx
// @include <http://www.tampermonkey.net/*>
// @include http://*
// @include https://*
// @include /^https:\\/\\/www\\.tampermonkey\\.net\\/.*$/
// @include *
```
## `@match`
限定脚本的执行范围。与 `@include` 类似，但匹配规则更为严格和精准。可以通过通配符 `*` 匹配不同网页。
必须遵循 `<protocol>://<domain><path>` 的格式。所以如 `*://tmnk.net/*`，只会匹配到域名为 `tmnk.net` 的页面。
```jsx
// @match *://example.com/*
// @match <https://example.com/path/*>
// @match https://*.example.com/*
// @match *://*/*
```
## `@exclude`
排除脚本在特定页面上的运行。匹配模式类似于`@include` 可以使用通配符（`*`和`?`）来匹配。对于排除的页面即使通过`@include`和`@match`包含在执行范围内，脚本也不会在该页面上运行。
```jsx
// @match <https://example.com/*>
// @exclude <https://example.com/login*>
// @exclude <https://example.com/admin/*>
```
## `@run-at`
指定了脚本的执行时机，从而确保脚本在网页加载的某个阶段触发。
常见时机有：
- `document-start`
    - 在页面的 HTML 开始加载时立即执行，可能会在 DOM 结构完全创建之前。
    - 适合需要尽早修改页面或阻止某些内容加载的场景。
        ```jsx
        // @run-at document-start
        ```
- `document-body`
    - 在页面 `<body>` 已加载但还未完全完成所有资源加载时执行某些操作。
    - 可以直接操作页面的 `<body>` 元素，而不必等到整个 DOM 构建完成。
        ```jsx
        // @run-at document-body
        ```
- `document-end`
    - 在页面的所有 HTML 元素已经完全加载并且 DOM 完成构建时执行，但此时资源（如图片、样式表）可能尚未完全加载。
    - 适合需要操作 DOM 的脚本。
        ```jsx
        // @run-at document-end
        ```
- `document-idle`（默认值）
    - 脚本会在页面的 HTML 和资源（如图片、样式表、脚本等）都加载完成之后执行。
    - 适合需要等待页面完全加载的操作，例如基于页面内容的分析。
        ```jsx
        // @run-at document-idle
        ```
- `context-menu`
    - 用于控制脚本只在用户右键单击上下文菜单时才运行。
    - 一般用于需要与用户交互的脚本，或需要上下文菜单的脚本。
        ```jsx
        
        // @run-at context-menu
        ```
## `@run-in`
执行脚本的浏览器上下文类型。如果不指定该标签，则会在所有选项卡中运行。
类型有：
- `normal-tabs`：脚本只会在正常浏览选项卡（非隐身模式，默认容器）中运行。
    ```jsx
    // @run-in normal-tabs
    ```
- `incognito-tabs`：脚本只会在隐身浏览选项卡（私人模式）中运行。
    ```jsx
    
    // @run-in incognito-tabs
    ```
FireFox浏览器支持容器，可以在`@run-in`中指定容器ID。
## `@sandbox`
脚本的执行位置。
`@sanbox`以执行环境区分，有三种参数：
- `MAIN_WORLD`：脚本可以与网页的脚本和资源进行直接交互，操作页面的全局变量和DOM。
- `ISOLATED_WORLD`：脚本运行在一个单独的JavaScript环境中，通过特定机制（`postMessage`或Tampermonkey提供的接口）进行通信。
- `USERSCRIPT_WORLD`：脚本默认运行的环境，介于`MAIN_WORLD`和`ISOLATED_WORLD`之间，用户脚本无法直接访问页面的全局变量和函数，Tampermonkey的特定API（如GM函数）进行安全交互。
以执行时的访问需求，有三种参数：
- `raw`：默认值，脚本运行在MAIN_WOELD中，与页面共享JavaScript环境，操作页面的全局变量和DOM。
- `JavaScript`：使用 JavaScript 的虚拟机环境运行脚本，运行在`USERSCRIPT_WORLD`中。
- `DOM`：隔离了JavaScript环境，但允许DOM操作。运行在`ISOLATED_WORLD`中。
```jsx
// @sandbox JavaScript
```
## `@tag`
为脚本添加标签。这些标签会在脚本管理面板展示。帮助开发者和用户快速识别和分类脚本。
```jsx
// @tag         social
// @tag         utility
```
## `@connect`
用于声明用户脚本能够发起跨域请求的外部域名。在使用`GM_xmlhttpRequest`发起跨域请求时，`@connect`用于限制请求只能到允许的域名。
设置为`tmnk.net`时，也将包括所有子域名。`self`将把脚本当前运行的域列入白名单。
```jsx
// @connect tmnk.net
// @connect www.tampermonkey.net
// @connect self
// @connect localhost
// @connect 8.8.8.8
// @connect *
```
## `@noframes`
使脚本在顶级窗口上运行，而不会在页面上嵌入的 frame 或 iframe 中执行。
```jsx
// @noframes
```
## `@updateURL`
指定脚本的更新链接。Tampermonkey将定期通过更新链接识别脚本的版本号，判断脚本是否更新。
更新链接通常指向 `.meta.js` 文件，该文件包含脚本的元数据头（包括版本信息等）。如果不使用 `@updateURL`，Tampermonkey 默认会从安装脚本时的原始 URL 检查更新。
```jsx
// @updateURL   <https://example.com/myscript.meta.js>
```
## `@downloadURL`
指定脚本的下载链接。当 Tampermonkey 通过 `@updateURL` 检测到新版本时，将会从这个URL下载并更新脚本。更新的脚本文件通常以 `.user.js` 结尾。
```jsx
// @updateURL   <https://mysite.com/myscript.meta.js>
// @downloadURL <https://mysite.com/myscript.user.js>
```
## `@supportURL`
指定用户脚本的支持页面或帮助链接。它为用户提供了反馈、报告问题或寻求帮助的地方，通常是指向开发者维护的论坛、GitHub 仓库的 issue 页面，或其他可以获取支持的地址。
```jsx
// @supportURL  <https://github.com/username/myscript/issues>
```
## `@webRequest`
允许开发者定义特定的规则JSON文档，这些规则会在用户脚本加载之前生效，从而可以拦截和修改特定的网络请求。
语法规则：
- `@webRequest` 标签：包含一个 JSON 文档，该文档是由 `rule` 参数构成的规则列表。
- `selector`：选择器，用于指定要拦截的网络请求的特定 URL 模式或其他条件。
- `action`：表示要对这些网络请求采取的操作。通常的操作有：
    - `"cancel"`：取消请求，不允许其继续。
    - `"redirect"`：重定向请求到另一个 URL。
    - `"modifyHeaders"`：修改请求或响应的头信息。
```jsx
// @webRequest  [{"selector": {"urls": ["*://example.com/*"]}, "action": "cancel"}]
```
规则参数：
- `urls`：表示需要拦截的 URL 模式，可以是具体的 URL 或通配符。例如 `://example.com/*` 表示拦截所有 HTTP 和 HTTPS 请求。
- `types`：可以指定要拦截的请求类型，如 `main_frame`（页面主文档）、`script`（脚本）、`image`（图片）等。
- `method`：可以指定请求的 HTTP 方法，如 `GET`、`POST` 等。
```jsx
// @webRequest  [{"selector": {"urls": ["*://example.com/*"]}, "action": {"redirect": "<https://example.org>"}}]
// @webRequest  [{"selector": {"urls": ["*://example.com/*"]}, "action": {"modifyHeaders": {"request": [{"header": "User-Agent", "value": "MyCustomUserAgent"}]}}}]
```
## `@unwrap`
默认情况下，Tampermonkey 用户脚本是运行在一个沙盒环境中的，这个沙盒环境可以有效地隔离用户脚本与页面的原始 JavaScript 环境，防止潜在的冲突和安全问题。`@unwrap`用于指示用户脚本是否应当从其沙盒环境中解包（unwrap）。使用 `@unwrap` 可以让用户脚本直接在页面的全局上下文中运行，而不是在沙盒中执行。允许脚本操作全局对象和函数。
```jsx
// @unwrap
```
# 应用程序接口
## `unsafeWindow`
允许脚本访问页面的原始window对象。默认情况下脚本运行在与网页隔离的环境中，而unsafeWindow可以让脚本与页面的window对象直接交互。
```jsx
unsafeWindow.someGlobalVariable;   // 访问网页中的全局变量
unsafeWindow.someFunction();       // 调用网页中的全局函数
```
## `Subresource Integrity`
Subresource Integrity（SRI）是一项安全功能，确保脚本加载的资源（JavaScript文件和CSS文件）没有被篡改。这是通过资源生成的加密哈希并将其包含在`@source`和@`require`中来完成的。
```jsx
// @resource SRIsecured1 <http://example.com/favicon1.ico#md5=ad34bb>...
// @resource SRIsecured2 <http://example.com/favicon2.ico#md5=ac3434>...,sha256=23fd34...
// @require              <https://code.jquery.com/jquery-2.1.1.min.js#md5=45eef>...
// @require              <https://code.jquery.com/jquery-2.1.2.min.js#md5-ac56d>...,sha256-6e789...
// @require              <https://code.jquery.com/jquery-3.6.0.min.js#sha256-/xUj+3OJU...ogEvDej/m4=>
```
Tampermonkey将计算资源的哈希值，并与标签中包含的哈希值比较。如果哈希值不匹配，Tampermonkey将拒绝加载资源。
Tampermonkey 原生支持 SHA-256 和 MD5 哈希值，所有其他哈希值（SHA-1、SHA-384 和 SHA-512）取决于 `window.crypto`。
如果给出多个哈希值（用逗号或分号分隔），Tampermonkey 将使用当前支持的最后一个哈希值。所有哈希值都需要以十六进制或 Base64 格式编码。
**Subresource Integrity**依赖于`link`元素和`script`元素的`integrity`属性：
```jsx
<script src="<https://code.jquery.com/jquery-3.6.0.min.js>"
        integrity="sha384-KyZXEAg3QhqLMpG8r+Knujsl5+5hb7O1L1Uqh5i+8ABH5Yp5JyELJmx9AklZDA5y"
        crossorigin="anonymous"></script>
```
`integrity`就是通过Base64编码的通过哈希算法生成的哈希值。
## `GM_addElement`
用于动态创建并插入HTML元素的API。避免直接操作DOM，增强脚本的兼容性和可维护性，但可能会被内容安全策略（CSP）禁用。这是一个实验性功能，可能会更改API。
- **`GM_addElement(tag_name, attributes)`**
    创建一个新元素并将其自动插入到 `document.body` 中，`attributes`是一个属性对象。
- **`GM_addElement(parent_node, tag_name, attributes)`**
    创建一个新元素并将其插入到指定的 `parent_node` 之下。
```jsx
// 向页面的 body 中添加一个带有样式的 div 元素
GM_addElement('div', {
  id: 'myDiv',
  class: 'myClass',
  style: 'color: red; background-color: yellow;',
  textContent: 'Hello, this is a dynamically added div!'
});

// 向特定的 parent_node 中添加一个 script 标签
let parent = document.querySelector('.container');
GM_addElement(parent, 'script', {
  src: '<https://example.com/some-script.js>',
  type: 'text/javascript'
});
```
## `GM_addStyle`
用于动态创建并插入 CSS 样式的API。
- **`GM_addStyle(css)`**
    css 是一个样式规则字符串，使用标准 CSS 语法书写。
```jsx
// 添加自定义样式
GM_addStyle(`
  body {
    background-color: #f0f0f0; /* 更改背景颜色 */
  }
  
  h1 {
    color: blue; /* 更改 h1 标签的文本颜色 */
    font-size: 2em; /* 更改字体大小 */
  }

  .myClass {
    border: 2px solid red; /* 为具有 myClass 的元素添加红色边框 */
    padding: 10px; /* 添加内边距 */
  }
`);
```
## `GM_download`
用于从脚本中下载文件。可用于跨域下载。可以自动触发文件下载，而不需要主动点击链接。
- `GM_download(details)`
    - `details`：一个对象，包含下载所需的详细信息。
- `GM_download(url, name)`
    - `url`：要下载文件的 URL。
    - `name`：保存时使用的文件名（可选）。
`details`：对象具有的属性：
- `url` (字符串，必需)
    - 要下载文件的完整 URL。
- `name` (字符串，可选)
    - 保存时使用的文件名。包含拓展名。如果未提供，将使用 URL 的最后部分作为文件名。
- `headers` (对象，可选)
    - 一个对象，包含请求中需要附加的 HTTP 头。例如，可以用来添加自定义认证头。
- `saveAs` (布尔值，可选)
    - 指定是否显示“另存为”对话框。如果设置为 `true`，用户将被提示选择文件保存位置。默认值为 `false`。
- `conflictAction`(字符串，可选)
    - 指定在下载文件时发生文件名冲突的行为
    - 可选值：
        - `"none"`（默认值）：如果文件已存在，下载将覆盖该文件。
        - `"uniquify"`：如果文件已存在，系统会自动为新文件添加一个后缀（例如 `(1)`、`(2)` 等），以确保文件名唯一。
        - `"prompt"`：如果文件已存在，浏览器将提示用户选择是覆盖文件还是保留原文件。
- `onload` (函数，可选)
    - 下载成功完成后的回调函数。
- `onerror` (函数，可选)
    - 下载失败时的回调函数，可以处理错误信息。
- `onprogress` (函数，可选)
    - 下载取得一些进展，则执行回调。
- `ontimeout` (函数，可选)
    - 由于超时而下载失败，则执行回调。
- `timeout` (数字，可选)
    - 下载请求的超时时间（以毫秒为单位）。如果未指定，将使用默认超时时间。
`onerror`回调的`download`参数，可以具有以下参数：
- `error` ：错误原因。
    - `not_enabled` - 用户未启用下载功能
    - `not_whitelisted` - 请求的文件扩展名未列入白名单
    - `not_permission` - 用户启用了下载功能，但未授予下载权限
    - `not_supported` - 浏览器/版本不支持下载功能
    - `not_succeeded` - 下载未开始或失败，详细信息属性可能提供更多信息
- `details`：有关错误的详细信息。
- `abort`：可以调用此函数来取消下载。
根据下载模式，`GM_info` 提供一个名为 `downloadMode` 的属性，该属性设置为以下值之一：`native`、`disabled` 或 `browser`。
```jsx
GM_download("<http://example.com/file.txt>", "file.txt");

const download = GM_download({
    url: "<http://example.com/file.txt>",
    name: "file.txt",
    saveAs: true
});

// cancel download after 5 seconds
window.setTimeout(() => download.abort(), 5000);
```
## `GM_getResourceText`
获取脚本中通过`@resource`定义的文本资源（如JavaScript或CSS）。以字符串形式返回资源文本。
- **`GM_getResourceText(name)`**
    通过资源名称来获取资源。
    ```jsx
// @resource myscript.js <https://example.com/some-script.js>

const scriptText = GM_getResourceText("myscript.js");
const scriptText2 = await GM.getResourceText("myscript.js");
const script = document.createElement("script");
script.textContent = scriptText;
document.body.appendChild(script);
```
## `GM_getResourceURL`
获取脚本中通过@resource定义的资源（如CSS或图像）URL。以字符串形式返回资源URL。
- **`GM_getResourceURL(name)`**
    通过资源名称获取资源。
    ```jsx
// @resource myimage.png <https://example.com/image.png>

const imageUrl = GM_getResourceURL("myimage.png");
const imageUrl2 = await GM.getResourceURL("myimage.png");
const image = document.createElement("img");
image.src = imageUrl;
document.body.appendChild(image);
```
## **`GM_info`**
用于获取脚本的相关信息。该对象包含以下信息。常用于调试工具。
```jsx
type ScriptGetInfo = {
    container?: { // 5.3+ | Firefox only
        id: string,
        name?: string
    },
    downloadMode: string,
    isFirstPartyIsolation?: boolean,
    isIncognito: boolean,
    sandboxMode: SandboxMode, // 4.18+
    scriptHandler: string,
    scriptMetaStr: string | null,
    scriptUpdateURL: string | null,
    scriptWillUpdate: boolean,
    userAgentData: UADataValues, // 4.19+
    version?: string,
    script: {
        antifeatures: { [antifeature: string]: { [locale: string]: string } },
        author: string | null,
        blockers: string[],
        connects: string[],
        copyright: string | null,
        deleted?: number | undefined,
        description_i18n: { [locale: string]: string } | null,
        description: string,
        downloadURL: string | null,
        excludes: string[],
        fileURL: string | null,
        grant: string[],
        header: string | null,
        homepage: string | null,
        icon: string | null,
        icon64: string | null,
        includes: string[],
        lastModified: number,
        matches: string[],
        name_i18n: { [locale: string]: string } | null,
        name: string,
        namespace: string | null,
        position: number,
        resources: Resource[],
        supportURL: string | null,
        system?: boolean | undefined,
        'run-at': string | null,
        'run-in': string[] | null, // 5.3+
        unwrap: boolean | null,
        updateURL: string | null,
        version: string,
        webRequest: WebRequestRule[] | null,
        options: {
            check_for_updates: boolean,
            comment: string | null,
            compatopts_for_requires: boolean,
            compat_wrappedjsobject: boolean,
            compat_metadata: boolean,
            compat_foreach: boolean,
            compat_powerful_this: boolean | null,
            sandbox: string | null,
            noframes: boolean | null,
            unwrap: boolean | null,
            run_at: string | null,
            run_in: string | null, // 5.3+
            override: {
                use_includes: string[],
                orig_includes: string[],
                merge_includes: boolean,
                use_matches: string[],
                orig_matches: string[],
                merge_matches: boolean,
                use_excludes: string[],
                orig_excludes: string[],
                merge_excludes: boolean,
                use_connects: string[],
                orig_connects: string[],
                merge_connects: boolean,
                use_blockers: string[],
                orig_run_at: string | null,
                orig_run_in: string[] | null, // 5.3+
                orig_noframes: boolean | null
            }
        }
    }
};

type SandboxMode = 'js' | 'raw' | 'dom';

type Resource = {
    name: string,
    url: string,
    error?: string,
    content?: string,
    meta?: string
};

type WebRequestRule = {
    selector: { include?: string | string[], match?: string | string[], exclude?: string | string[] } | string,
    action: string | {
        cancel?: boolean,
        redirect?: {
            url: string,
            from?: string,
            to?: string
        } | string
    }
};

type UADataValues = {
    brands?: {
        brand: string;
        version: string;
    }[],
    mobile?: boolean,
    platform?: string,
    architecture?: string,
    bitness?: string
}
```
```jsx
console.log("Script Name: " + GM_info.script.name);
console.log("Version: " + GM_info.script.version);
console.log("Author: " + GM_info.script.author);

// 检查脚本的命名空间
if (GM_info.script.namespace === 'my.namespace') {
    console.log('This script is part of my namespace.');
}
```
## `GM_log`
用于将信息输出到控制台，常用于调试，不支持日志输出级别，所有日志都以普通日志信息输出。
- **`GM_log(message)`**
    message可以是字符串、数字和JavaScript对象。
```jsx
const userName = "Alice";
const userAge = 30;

GM_log("User Name: " + userName);
GM_log("User Age: " + userAge);

// 记录一个对象
const data = { id: 1, status: "active" };
GM_log("User Data: ", data);
```
## `GM_notification`
用于显示通知。
- `GM_notification(details, ondone)`
    - `details`：一个对象，包含通知的详细信息。
    - `ondone`：一个可选的回调函数，当用户点击通知时触发。
- `GM_notification(text, title, image, onclick)`
    - `text`：通知的内容。
    - `title`：通知的标题。
    - `image`：可选，通知中显示的图像 URL。
    - `onclick`：函数，可选，当用户点击通知时触发的回调函数。
`details`对象包含以下属性：
- `text` (字符串，必需)：通知的主体内容。
- `title` (字符串，必需)：通知的标题。
- `tag`：用于标识此通知。这样，您可以通过再次调用 GM_notification 并使用相同的标签来更新现有通知。如果您不提供标签，则每次都会创建一个新通知。
- `highlight`：一个布尔标志，是否突出显示发送通知的选项卡（除非设置了文本，否则是必需的）
- `url`：当用户单击通知时要加载的 URL。您可以通过在 onclick 事件处理程序中调用 event.preventDefault() 来阻止加载 URL。
- `image` (字符串，可选)：通知中显示的图像 URL，通常用于提供一个图标或相关图片。
- `timeout` (数字，可选)：指定通知在显示后自动关闭的时间（以毫秒为单位）。如果未设置，则通知将保持在屏幕上，直到用户点击它或手动关闭。
- `silent` (布尔值，可选)：指定通知是否静默显示。如果设置为 `true`，则不会播放声音。
- `onclick`：当用户点击通知时将调用的回调函数。
- `ondone`：当通知关闭（无论是由超时还是点击触发）或选项卡突出显示时将调用的回调函数
```jsx
// 对象参数
GM_notification({
  text: "下载已完成！",
  title: "下载通知",
  image: "<https://example.com/icon.png>",
}, function() {
  console.log("Notification clicked!");
});
// 单独参数
GM_notification("下载已完成！", "下载通知", "<https://example.com/icon.png>", function() {
  console.log("Notification clicked!");
});
```
## `GM_openInTab`
在新标签页中打开指定URL。
- `GM_openInTab(url, options)`
    - **`url`**：要打开的页面的 URL。
    - **`options`**：一个对象，包含以下可选属性：
        - **`active`** (布尔值，可选)：如果为 `true`，新标签页将处于激活状态（用户可见）。默认值为 `true`。
        - **`insert`** (整数，可选)：指定新选项卡插入的位置，默认值为 `false` ，表示添加到选项卡末尾。
        - `setParent`(布尔值，可选)：指示新选项卡是否应被视为当前选项卡的子选项卡。默认为 false。
        - `incognito` (布尔值，可选)：使选项卡在隐身模式/私人模式窗口中打开。
        - `loadInBackground` (布尔值，可选)：具有与 active 相反的含义，添加它是为了实现 Greasemonkey 3.x 兼容性。
- `GM_openInTab(url, loadInBackground)`
    - **`url`**：要打开的页面的 URL。
    - **`loadInBackground`** (布尔值)：如果为 `true`，新标签页将在后台加载，用户不会立即看到它；如果为 `false`，标签页将在前台打开。
该函数返回一个对象，其中包含函数 close、侦听器 onclose 和一个名为 closed 的标志。
```jsx
// 对象参数
GM_openInTab("<https://example.com>", {
  active: false, // 在后台打开
  insert: true // 在当前标签旁边打开
});
// 布尔值参数
GM_openInTab("<https://example.com>", true); // 在前台打开
```
## `GM_registerMenuCommand`
为脚本菜单（目标页面中点击Tampermonkey图标后出现的菜单）注册新命令。
- **`GM_registerMenuCommand(name, callback, options_or_accessKey)`**
如果名称、标题和 accessKey 相同，则从不同`frames`创建的菜单项将合并为单个菜单项。
函数采用三个参数：
- `name` (字符串)：菜单命令名称
- `callback`(回调函数)：使用此命令后需要执行的函数。
- `accessKey`(字符串)：使用此命令的快捷键（需要在脚本菜单打开时）。
- `options`(自定义菜单项对象)：
    - `id` ：唯一标识该命令的字符串或数字。通过指定 `id`，你可以在需要时动态移除或更新该命令。
    - `accessKey`： 命令快捷键。
    - `autoClose` ：指定单击菜单项后是否应关闭弹出菜单。默认值为 true。
    - `title` ：一个可选字符串，指定菜单项的标题。当用户将鼠标悬停在菜单项上时，这会显示为工具提示。
该函数返回一个可用于取消注册命令的菜单项 ID。
```jsx
GM_registerMenuCommand("Say Hello", function() {
  alert("Hello, world!");
}, { accessKey: "H" }); // H 是设置的快捷键
```
## `GM_unregisterMenuCommand`
用于通过命令ID取消之前使用 `GM_registerMenuCommand` 注册的菜单命令。
- `GM_unregisterMenuCommand(menuCmdId)`
```jsx
// 注册一个菜单命令并保存返回的 menuCmdId
const menuCmdId = GM_registerMenuCommand("Say Hello", function() {
  alert("Hello, world!");
});

// 取消注册该命令
GM_unregisterMenuCommand(menuCmdId);
```
## `GM_setClipboard`
将指定数据复制到剪贴板中。
- `GM_setClipboard(data, info, cb)`
    - **`data`**（必需）：要复制到剪贴板的数据。这可以是一个字符串，也可以是其他类型的数据（如 HTML等，具体支持取决于 `info` 参数）。
    - **`info`**（可选）：定义剪贴板内容的 MIME 类型或其他额外信息。它可以是一个字符串，也可以是一个对象。
        - **`info`** 可以是 MIME 类型的字符串，比如 `"text/plain"`、`"text/html"`。
        - 如果是对象格式，它可以包含以下属性：
            - **`type`**：指定剪贴板数据的 MIME 类型，如 `"text/plain"`。
            - **`minetype`**：与 `type` 类似，定义数据类型。
            - **`document`**：指定用哪个 `document` 来处理剪贴板内容。
    - **`cb`**（可选）：一个回调函数，在剪贴板操作完成后调用。
该函数不支持复制二进制图片到剪贴板，可以使用浏览器的Clipboard API操作。
```jsx
GM_setClipboard("Copied text", "text/plain", function() {
  console.log("Text copied to clipboard!");
});
```
## `GM_getTab`
用于获取GM_saveTab保存的数据。可以在多个标签页中进行数据共享。
- **`GM_getTab(callback)`**
回调函数接受一个代表当前标签页数据的对象。
```jsx
// 保存数据到当前标签页
GM_saveTab({ userId: 12345, session: "active" });

// 获取并显示数据
GM_getTab(function(tabData) {
  console.log("Tab data:", tabData);  // 输出 {userId: 12345, session: "active"}
});
```
## `GM_saveTab`
用于在当前浏览器**标签页的会话内存**中保存数据。存储的数据仅在标签页的生命周期内有效，即当标签页关闭时，保存的数据会自动丢失。
- `GM_saveTab(tab, cb)`
    - `tab`：保存的数据
    - `cb`：回调函数
```jsx
let sessionData = {
  cartItems: ["item1", "item2"],
  loggedIn: true
};

// 将数据保存到当前标签页
GM_saveTab(sessionData);
```
## `GM_getTabs`
允许用户脚本获取当前浏览器中所有正在运行的**标签页**的数据。这些数据是通过 `GM_saveTab` 保存的，因此 `GM_getTabs` 主要用于跨标签页共享数据。
- `GM_getTabs(callback)`
    - 一个回调函数，当 `GM_getTabs` 成功获取到所有标签页的数据时，将调用这个函数。回调函数会接收一个对象作为参数，这个对象包含了所有标签页的数据。
返回一个包含当前浏览器所有运行标签页的对象，每个标签页的数据以 `tabID` 为键，存储的内容为值。
```jsx
{
  "tabID1": { ...data... },
  "tabID2": { ...data... },
  ...
}
```
如果某个标签页没有通过 `GM_saveTab` 保存数据，该标签页将不会出现在返回的对象中。
```jsx
GM_getTabs(function(tabs) {
  // tabs 是一个对象，包含所有保存了数据的标签页
  for (let tabID in tabs) {
    console.log("Tab ID:", tabID);
    console.log("Data for this tab:", tabs[tabID]);
  }
});
```
`GM_getTabs` 无法在不同域名之间共享数据。
## `GM_setValue`
用于将某个键值对存储到用户脚本的持久化存储中。这些数据是与用户脚本相关联的，并且即使浏览器关闭后也会保留。
- **`GM_setValue(key, value)`**
    - `key`：唯一标识字符串
    - `value`：任意数据对象。
```jsx
let userSettings = {
  theme: "dark",
  notificationsEnabled: true
};

GM_setValue("settings", userSettings);
```
该函数无返回值。
## `GM_getValue`
用于获取通过 `GM_setValue` 存储的数据。它可以读取已保存的键值对，如果指定的键不存在，还可以返回一个默认值。
- `GM_getValue(key, defaultValue)`
```jsx
let userSettings = GM_getValue("settings", {
  theme: "light",
  notificationsEnabled: true
});

console.log("User settings:", userSettings);
```
## `GM_deleteValue`
用于删除通过 `GM_setValue` 存储的某个键值对。删除后，之前存储的数据将不可访问。
- `GM_deleteValue(key)`
```jsx
GM_deleteValue("username");
console.log(GM_getValue("username", "guest"));  // 输出 "guest"
```
## `GM_listValues`
用于获取当前用户脚本中所有存储的键（key）列表。
- `GM_listValues()`
```jsx
// 获取所有存储的键
let storedKeys = GM_listValues();
console.log("Stored keys:", storedKeys);

// 遍历并打印每个键对应的值
storedKeys.forEach(key => {
    let value = GM_getValue(key);
    console.log(`Key: ${key}, Value: ${value}`);
});
```
## `GM_setValues`
用于一次性设置多个键值对。
- `GM_setValues(values)`
    - `values`：对象，包含多个键值对数据
```jsx
GM_setValues({
    username: "john_doe",
    theme: "dark",
    notificationsEnabled: true
});
```
## `GM_getValues`
用于一次性获取多个键的值。
- `GM_getValues(keysOrDefaults)`
    - `keysOrDefaults`：可以是键的数组，也可以是含有默认值的对象。
```jsx
// 数组
let keys = ["username", "theme", "notificationsEnabled"];
let userSettings = GM_getValues(keys);

console.log("User Settings:", userSettings);

// 对象
let defaults = {
    username: "guest",
    theme: "light",
    notificationsEnabled: false
};

let userSettings = GM_getValues(defaults);

console.log("User Settings with Defaults:", userSettings);
```
传入一个对象时，`GM_getValues` 将尝试获取每个键的值，如果某个键不存在，则返回相应的默认值。
## `GM_deleteValues`
用于一次性删除多个键值对。
- `GM_deleteValues(keys)`
    - 键的数组。
```jsx
let keysToDelete = ["username", "theme", "notificationsEnabled"];
GM_deleteValues(keysToDelete);
```
## `GM_addValueChangeListener`
用于监听特定键的值变化。
- `GM_addValueChangeListener(key, (key, old_value, new_value, remote) => void)`
    - **`key`**（必需）：一个字符串，表示要监听的键名。
    - **`callback`**（必需）：一个回调函数，当指定键的值发生变化时将被调用。这个函数接收四个参数：
        - **`key`**：发生变化的键名。
        - **`old_value`**：该键的旧值。
        - **`new_value`**：该键的新值。
        - **`remote`**：一个布尔值，指示值是否是从其他脚本（例如，跨脚本共享）更新的。
```jsx
GM_addValueChangeListener("username", (key, oldValue, newValue, remote) => {
    console.log(`The value of "${key}" changed from "${oldValue}" to "${newValue}"`);
    if (remote) {
        console.log("The change was made by another script.");
    }
});
```
返回一个“`listenerId`”值，可用于稍后使用 `GM_removeValueChangeListener` 函数删除侦听器
## `GM_removeValueChangeListener`
用于取消先前通过 `GM_addValueChangeListener` 注册的值变化监听器。
- `GM_removeValueChangeListener(listenerId)`
```jsx
// 注册一个值变化监听器
const listenerId = GM_addValueChangeListener("username", (key, oldValue, newValue, remote) => {
    console.log(`The value of "${key}" changed from "${oldValue}" to "${newValue}"`);
});

// 取消监听器
GM_removeValueChangeListener(listenerId);
```
## `GM_xmlhttpRequest`
用于在用户脚本中执行跨域 HTTP 请求。
- `GM_xmlhttpRequest(details)`
    **`details`**（必需）：一个对象，包含请求的详细信息，通常包括以下属性：
    - **`method`**（必需）：HTTP 方法，如 `"GET"`、`"POST"` 等。
    - **`url`**（必需）：请求的 URL。
    - **`headers`**（可选）：一个对象，包含请求头。
    - **`data`**（可选）：发送的数据（仅在 `POST` 方法中使用）。
    - **redirect** （可选）：重定向。
    - **cookie** （可选）：需要发送的cookie。
    - **cookiePartition**
    - **binary**
    - **nocache**
    - **revalidate**
    - **context**
    - **responseType**
    - **overrideMimeType**
    - **anonymous**
    - **fetch**
    - **user**
    - **password**
    - **onabort**
    - **onloadstart**
    - **onprogress**
    - **onreadystatechange**
    - **ontimeout**
    - **`onerror`**（可选）：请求失败时调用的回调函数。
    - **`timeout`**（可选）：请求超时时间（毫秒）。
    - **`onload`**（必需）：请求成功后调用的回调函数，接收一个响应对象。
        - **finalUrl**
        - **readyState**
        - **status**
        - **statusText**
        - **responseHeaders**
        - **response**
        - **responseXML**
        - **responseText**
该函数将返回一个**`abort**` 对象，用于取消此请求。
```jsx
GM_xmlhttpRequest({
    method: "POST",
    url: "<https://api.example.com/submit>",
    headers: {
        "Content-Type": "application/json"
    },
    data: JSON.stringify({ key: "value" }),
    onload: function(response) {
        console.log("Response:", response.responseText);
    },
    onerror: function(error) {
        console.error("Request failed:", error);
    }
});
```
## **`GM_webRequest`**
用于监控和修改浏览器中的网络请求。
- `GM_webRequest(rules, listener)`
    - **`rules`**（必需）：一个 JSON 对象，包含匹配请求的规则。这些规则决定了哪些请求会被监听和处理。常见的属性包括：
        - **`urls`**：一个字符串数组，定义要匹配的 URL 模式。
        - **`types`**：一个字符串数组，定义请求的类型，例如 `"xmlhttprequest"`、`"script"`、`"image"` 等。
    - **`listener`**（必需）：一个回调函数，当匹配的请求发生时调用。这个函数接收一个参数，包含有关请求的详细信息。
```jsx
GM_webRequest({
    urls: ["*://api.example.com/*"]
}, (details) => {
    // 修改请求头
    if (details.requestHeaders) {
        details.requestHeaders.push({ name: "Authorization", value: "Bearer token" });
    }
    return { requestHeaders: details.requestHeaders };
});
```