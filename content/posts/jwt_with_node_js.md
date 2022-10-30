---
title: "JWT簡介 - 使用Node.js"
date: 2022-10-29T23:47:11+08:00
# weight: 1
# aliases: ["/Jwt_with_node_js"]
tags: ["Backend", "JWT"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "What is JWT and how to use it with Node.js?"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: false
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/AlliesChen/website-hugo/blob/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

關於這篇 JWT 的說明比較偏向我個人的筆記，節錄及參考了下方連結中的內容，拜訪連結文章會有更詳盡完整的資訊：

- JWT 官方的 [Introduction to JSON web Tokens](https://jwt.io/introduction/)

- [The Ultimate Guide to handling JWTs on frontend clients (GraphQL)](https://hasura.io/blog/best-practices-of-using-jwt-with-graphql/) 前段介紹了 JWT 是什麼，中後段則是使用流程與實踐，不過是搭配GraphQL，這裡不會提到。

- 這則[推特貼文](https://twitter.com/GuidesJava/status/1556482072356671488)圖解了JWT的結構與基本的驗證流程。

JWT 是 JSON Web Token 的縮寫，這個 JSON 是一種資料傳輸格式，像是：

```json
{
    "sub": "1234567890",
    "name": "John Doe",
    "iat": 1516239022
}
```

## JWT 結構

JWT 是由三組字串組成，每組用 點(dot) 隔開，像這樣 `xxxxx.yyyyy.zzzzz`；x、y，和 z 分別代表了 header、payload，和signature，接下來會分別再對這三部份的內容用途進行說明。

可以先提到的是 header 和 payload 只是 base64加密，可以透過在瀏覽器的 console 使用 `atob()` 轉換回原來的訊息，比如說[官網的範例](https://jwt.io/)：
  `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

把中間 payload 的部份透過 `atob` 轉換：

```javascript
atob('eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ')
// '{"sub":"1234567890","name":"John Doe","iat":1516239022}'
```

就可以看到原始資訊是 subject (sub)、name，和 issue at (iat)。

### Header

通常有兩個部份，這個 token 的類型(typ)，也就是 JWT，以及要用在產生 signature 的雜湊演算法 (alg)：

```json
{
    "alg": "HS256",
    "typ": "JWT"
}
```

### Payload

如同上面提到，因為可以被輕易的還原回原本的資訊，所以這裡會放的是非敏感的 (non-sensitive) 使用者資訊，像是使用者 id，以及其它關於這個 token 的額外資訊，這些被稱為 claims：

```json
{
    "sub": "1234567890",
    "name": "John Doe",
    "iat": 1516239022
}
```

又，claims 被分為三種：

- Registered claims 指名稱在[這串清單](https://www.rfc-editor.org/rfc/rfc7519#section-4.1)中有被預定義的項目，可以讓開發者彼此間更容易理解 payload 裡的資訊。

- Public claims 是撰寫者自定義的資訊，但要避免與[這個網站](https://www.iana.org/assignments/jwt/jwt.xhtml)列出的名稱重複到。

- Private claims 也是撰寫者自定義，用在團體間分享，但又不屬於 registered 或是 public 的資訊。

### Signature

用來給伺服器驗證 token 的有效性。使用 header 中給訂的演算法 (alg) 對加密後的 header 和 payload，以及一個 secret (一串字串)，去產生 JWT，示例就是像[官網的範例](https://jwt.io/)：

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

出來就是開頭那串看起來像亂碼並用 `.` (dot) 分離的字串：

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

這邊可以注意到，如果 header 和 payload 的內容有變動，signature 的值也會改變，這能避免有人透過修改 payload 的值，比如把 user id 改為別人的來登入不屬於使用者本人的帳號。

> `secret` 是自訂義的一串字，並且**不應該被洩漏**，它用在產生 JWT 的雜湊演算法中，太短安全性較差，太長會花費比較多的時間計算，從而影響效能。

## 使用 JWT

關於上面提到的製作細節，我們都可以不管，只要透過[相關函式庫](https://jwt.io/libraries)就能達成 JWT 的簽發及驗證；接下來範例中，我會使用的是 Node.js 的 [`jsonwebtoken`](https://github.com/auth0/node-jsonwebtoken#readme) 套件。

我跟著 Youtube 上 Net Ninja 的 [MERN Auth Tutorial 系列](https://www.youtube.com/playlist?list=PL4cUxeGkcC9g8OhpOZxNdhXggFz2lOuCT)做出[這個範例](https://github.com/AlliesChen/MERN-stack)；考量到 JWT 如果被偷走，就代表能登入，因此我改把 token 從教學影片中用的 `localStorage` 改到 cookie 中，並且設定了 `httpOnly` 和 `sameSite` ，這兩個的用途可以參考[零基礎資安系列（三）-網站安全三本柱（Secure & SameSite & HttpOnly）](https://tech-blog.cymetrics.io/posts/jo/zerobased-secure-samesite-httponly/)。

> 放在 localStorage 和 Cookies 的 XSS 風險可以參考 [XSS - localStorage vs Cookies ](https://academind.com/tutorials/localstorage-vs-cookies-xss)，這部影片有做程式演示。

P.S. 我使用的前端 JS 框架是 Preact 而不是教學影片中的 React，兩個框架有些許不同在，比如 Router 套件的用法。

### Example: Sign up & Log in

前端程式設定 [signup](https://github.com/AlliesChen/MERN-stack/blob/main/frontend/src/hooks/useSignup.js) 或 [login](https://github.com/AlliesChen/MERN-stack/blob/main/frontend/src/hooks/useLogin.js) 對後端位址發出請求：

```javascript
const response = await fetch('/api/user/signup', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({email, password})
})
```

```javascript
const response = await fetch('/api/user/login', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({email, password})
})
```

後端收到 sign up 或 log in 的請求時，需要做出這個 token，非常簡單，第一個參數就是會出現在 payload 的資料，第二個參數就是 Signature 該節提到用來做雜湊的 `secret`，最後則是這個 token 的有效期，這邊設定為「三天」。

然後 [response.cookie](http://expressjs.com/en/4x/api.html#res.cookie) 裡加入 token 回傳給 browser： ([這段 code 在哪？](https://github.com/AlliesChen/MERN-stack/blob/main/backend/controllers/userController.js))

```javascript
const jwt = require('jsonwebtoken');

function createToken(id) {
  return jwt.sign({ _id: id }, process.env.SECRET, { expiresIn: '3d' });
}

const token = createToken(user.id);
    res.status(200).cookie('token', token, {
        httpOnly: true,
        sameSite: 'strict',
})
```

然後在 Log in 時，token 會在 request 的 header 中的 cookie 裡，提取出來就可以用套件提供的 [`verify` 方法](https://github.com/auth0/node-jsonwebtoken#jwtverifytoken-secretorpublickey-options-callback)驗證：([這段 code 在哪？](https://github.com/AlliesChen/MERN-stack/blob/main/backend/middleware/requireAuth.js))

```javascript
const jwt = require('jsonwebtoken');

const token = req.headers.cookie.split('=')[1];

const { _id } = jwt.verify(token, process.env.SECRET);
```

同樣的機制也被用在限制只有使用者能看到文章內容，會先經過 [auth](https://github.com/AlliesChen/MERN-stack/blob/main/backend/middleware/requireAuth.js) 流程。

### Example: Log out

前端對 logout 這個位址發送請求，另外也清除登入的狀態資料：

```javascript
function logout() {
    fetch('/api/user/logout')

    // remove user from storage
    localStorage.removeItem('user')

    // dispatch logout action
    dispatch({type: 'LOGOUT'})
    workoutDispatch({type: 'SET_WORKOUTS', payload: []})
}
```

後端在 response 中呼叫 [clearCookie 方法](http://expressjs.com/en/4x/api.html#res.clearCookie) 讓瀏覽器清掉 cookie：([這段 code 在哪？](https://github.com/AlliesChen/MERN-stack/blob/main/backend/controllers/userController.js))

```javascript
async function logoutUser(req, res) {
  res.status(200).clearCookie('token');
  res.end();
}
```
