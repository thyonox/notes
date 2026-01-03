![image.png](attachment:b9b9a124-d5d8-438a-ad7d-6efbb043c4af:image.png)

# 概述

---

## 简介

---

**JWT**（Json Web Token）是一种基于 **JSON** 的的数据交换格式，因为**轻量**与**安全**，常用于 Web 和移动应用的**认证**和**授权**过程。

JWT 由**头部**（Header）、**负载**（Payload）、**签名**（Signature）三部分组成。

![image.png](attachment:d253d81c-6cce-40d5-86c9-0637ef09b7d2:image.png)

## 需求

---

## 对比

---

# 开始

---

## 安装

---

因为 JWT 只是一种数据传输的开发标准，需要在项目中使用，需要使用它的实现库，其中最流行的实现库是 JJWT。

在较早版本，JJWT 是一个单独的 Jar 包，而在较新版本，JJWT 被拆分成了多个模块：

- jjwt-api：JWT 的核心 API，提供创建和解析 JWT 的接口。
- jjwt-impl：具体实现，包含默认的 JWT 解析和签名逻辑。
- jjwt-jackson：JSON 解析支持，基于 Jackson 处理 JWT 负载部分。

Java8 推荐使用 0.9.1 版本。Java8 以上推荐使用 0.11.5 版本。

Java8 以上无法使用 0.9.1 版本，因为没有 DataTypeConverter，而这是 JJWT 进行 Base64 编码的工具类。

## 使用

---

![image.png](attachment:4cc4e5a9-1786-466a-a6d1-79507caa8efd:image.png)

# 概念

---

## 头部（Header）

---

头部包含两部分：

- `alg`：加密算法，如 `HS256`（HMAC SHA256）或 `RS256`（RSA SHA256）等。
- `typ`：令牌类型，通常是 `JWT`。

头部经过 Base64Url 编码后就是 JWT 的第一部分。

- 示例
    
    ```jsx
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
    

## 负载（Payload）

---

负载包含了声明（claims），主要分为三部分：

- 注册声明：通常是用户的身份识别，如 iss（签发者）、exp（过期时间）、sub（主题，一般存储用户名）等。
- 公共声明：可以由任何人定义。
- 私有声明：用于在通信双方交换自定义声明。

负载默认未加密，可以被编码，通过 Base64Url 编码后得到 JWT 的第二部分。

- 示例
    
    ```jsx
    {
      "sub": "1234567890",
      "name": "John Doe",
      "iat": 1516239022
    }
    ```
    

## 签名（Signature）

---

签名是对头部、负载和密钥通过加密算法计算得到的，用于验证 JWT 是否被修改。

secret 是服务端持有的密钥，防止伪造请求。

- 示例
    
    ```jsx
    HMACSHA256(
      base64UrlEncode(header) + "." +
      base64UrlEncode(payload),
    	your-256-bit-secret
    )
    ```
    

# 配置

---

# 附录

---

<aside> 🌐

**官网地址：** [https://jwt.io/](https://jwt.io/)

</aside>

<aside> 💾

**代码地址：** [https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)

</aside>

<aside> 📓

**其它文档：** [https://www.youtube.com/watch?v=P2CPd9ynFLg](https://www.youtube.com/watch?v=P2CPd9ynFLg)

</aside>