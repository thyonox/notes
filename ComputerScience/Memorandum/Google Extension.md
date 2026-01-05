# 概述
---
## 简介
---



## 需求
---



## 对比
---




# 开始
---
## 安装
---




## 使用
---





# 概念
---





# 配置
---




# 思考
---



# 附录
---
- [Chrome Extensions 官方文档](https://developer.chrome.com/docs/extensions/get-started?hl=zh-cn)
- [Chrome for developers 主页](https://developer.chrome.com/?hl=zh-cn)


https://github.com/GoogleChrome/chrome-extensions-samples/tree/main
```json
{
  "manifest_version": 3,
  "name": "My Extension",
  "version": "1.0",
  "description": "A simple Chrome extension",
  "icons": {
    "16": "images/icon16.png",
    "48": "images/icon48.png",
    "128": "images/icon128.png"
  },
  "action": {
    "default_icon": {
      "16": "images/icon16.png",
      "32": "images/icon32.png"
    },
    "default_title": "My Extension",
    "default_popup": "popup.html"
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"],
      "css": ["styles.css"]
    }
  ],
  "permissions": [
    "activeTab",
    "storage",
    "tabs"
  ],
  "host_permissions": [
    "https://*.example.com/*"
  ],
  "web_accessible_resources": [
    {
      "resources": ["images/*.png", "script.js"],
      "matches": ["<all_urls>"]
    }
  ],
  "content_security_policy": {
    "extension_pages": "script-src 'self'; object-src 'self'"
  },
  "options_page": "options.html",
  "default_locale": "en"
}
```
- **基本信息**
	- manifest_version (必需): 指定Manifest版本，当前必须为3（Manifest V3）。
	- name (必需): 扩展名称，显示在Chrome Web Store和扩展管理页面。
	- version (必需): 扩展版本号，格式如"1.0.0"。
	- description (可选): 扩展功能描述，简短说明用途。
	- default_locale (可选): 默认语言，用于多语言支持（如"en"）。
- **图标配置**
	- icons (可选): 定义扩展的图标，用于Chrome Web Store、扩展管理和安装页面。
		- 格式：对象，键为尺寸（如"16"、"48"、"128"），值为图片路径。
		- 推荐尺寸：16px、48px、128px（PNG格式）。
- **工具栏行为**
	- action (可选): 配置工具栏图标和交互行为。
		- default_icon: 工具栏图标，对象或单一路径。
		- default_title: 鼠标悬停时的提示文本。
		- default_popup: 点击图标时弹出的HTML文件路径。
			- 当有该字段时，chrome.action.onClicked 不能被触发。
- **后台逻辑**
	- background (可选): 定义后台运行的脚本。
	- Manifest V3中使用service_worker（单一JS文件，如"background.js"）。
	- 用途：处理全局事件、消息传递、通知等。
	- 旧版V2用scripts（数组）或page（HTML），现已废弃。
- **内容脚本**
	- content_scripts (可选): 定义注入到网页的脚本和CSS。
		- 结构：数组，每个元素包含：
			- matches: 匹配的URL模式（如`["https://*.example.com/*"]`或`["<all_urls>"]`）。
			- js: 注入的JS文件数组（如["content.js"]）。
			- css: 注入的CSS文件数组（如["styles.css"]）。
			- run_at: 脚本注入时机（"document_start"、"document_end"、"document_idle"，默认document_idle）。
			- all_frames: 是否注入到所有iframe（true/false）。
- **权限**
	- permissions (可选): 声明扩展需要的API权限。
		- 常见值："activeTab"、"storage"、"tabs"、"notifications"等。
		- 需遵循最小权限原则，避免请求不必要的权限。
	- host_permissions (可选): 声明访问特定域的权限。
		- 格式：URL模式，如"https://*.example.com/*"。
		- 用于内容脚本或网络请求的跨域访问。
- **资源访问**
	- web_accessible_resources (可选): 声明网页可访问的扩展资源。
		- 结构：数组，每个元素包含：
			- resources: 可访问的文件路径（如["images/*.png"]）。
			- matches: 允许访问的URL模式（如[""]）。
		- 用途：让网页通过chrome.runtime.getURL()访问扩展中的资源。
- **安全策略**
	- content_security_policy (可选): 定义内容安全策略（CSP），限制脚本来源。
		- Manifest V3中为对象，包含：
			- extension_pages: 控制扩展页面的脚本（如"script-src 'self'"）。
			- sandbox: 控制沙盒页面的策略。
		- 默认：只允许本地脚本（'self'），禁止远程代码。
- **选项页面**
	- options_page (可选): 指定扩展的设置页面（如"options.html"）。
		- 用户可通过扩展管理页面或右键菜单访问。
- **其他字段**
	- commands (可选): 定义快捷键命令。
	    - 示例：绑定快捷键到特定操作。
	- devtools_page (可选): 指定DevTools扩展的HTML文件。
	- omnibox (可选): 配置地址栏关键字搜索功能。
	- chrome_url_overrides (可选): 覆盖Chrome特定页面（如新标签页、历史记录）。



2015


# service_worker
---

