---
title: "那些網頁開發的名詞"
date: 2022-07-16T19:20:00+08:00
# weight: 1
# aliases: ["/web-dev-terms"]
tags: ["murmur"]
author: "Yu-Pung"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Those technical terms for web dev"
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

這篇文章是我在職訓班時寫來釐清這堆術語的，稍微更新了比較理解的部份（前端），但到了現在我也還在更了解他們中（後端和網路的部份），有錯誤的部份還請不吝指教。

文中資料來自以下兩篇The Odin Project的主題，或由其中導向的網頁提供：

- [Introduction to the back end](https://www.theodinproject.com/paths/foundations/courses/foundations/lessons/introduction-to-the-back-end)
- [Introduction to frameworks](https://www.theodinproject.com/paths/foundations/courses/foundations/lessons/introduction-to-frameworks-web-development-101)

要開發一個網站，你需要選擇伺服器、資料庫、程式語言、框架及前端工具，這些開發技術一個個堆起來，被稱為堆疊(stack)[^rubyGarage]

[這篇文章](https://rubygarage.org/blog/technology-stack-for-web-development)的末段有提到幾個知名網站使用的軟體堆疊。

## 前端 Front end

前端，又稱client-side[^rubyGarage]，通常是指應用HTML標識(markup), CSS演示(presentation), JavaScript編程(scripting)。

JavaScript有很多函式庫(library)可用，如[jQuery](https://jquery.com/)，React(他寫自己是library)以及框架，如Angular, Vue等，幫助簡化及快速開發[^rubyGarage]。基本上，可以把框架想成有制定使用慣例的函式庫，選擇某個框架意味著要使用對應的框架生態圈，比如狀態管理（處理資料流），Vue的話就會用Vuex或Pinia，React的話可能是Context或Redux。

[這篇文章](https://blog.teamtreehouse.com/i-dont-speak-your-language-frontend-vs-backend)提供了前端與後端的比較。

## 後端 Back end

後端，又稱server-side[^rubyGarage]；指運作在server端的應用，由於脫離了使用者瀏覽器相容性的限制，有大量的程式語言被應用在這塊。

主流的語言有：PHP, [ASP.NET(C#)](https://zh.wikipedia.org/wiki/ASP.NET), Ruby, Python, Node.js[^DEV]和Java, Go等。

這些語言又延伸出對應的框架(framework)來幫助開發者們實踐功能，如Ruby on Rails, Laravel(PHP), Django(Python)...等。

普遍被提到的WordPress，就是一個將前端及後端整合起來的開源(open source)框架，建立在安裝了PHP的資料庫(database)，並允許設計者透過CSS, jQuery及JavaSript。[^treehouse]

### 什麼是後端開發

翻譯自TechTerms -- [Backend](https://techterms.com/definition/backend)

以程式術語來說，後端是數據訪問層(data access layer)，前端是(presentation layer)表現層。

後端的運作流程大致如：

1. 處理網頁送來的要求(request)
2. 執行語法(PHP, ASP, JSP, etc.)來產生HTML
3. 使用SQL查詢(queries)—某個特定主題(article)，然後訪問(accessing)該主題在資料庫中的資料。
4. 儲存並更新資料庫
5. 加密或解密資料
6. 處理檔案上傳與下載
7. 透過JavaScript運算使用者輸入的資訊

### 後端包括哪些部分及各部分的名稱

參考自Codecademy的這篇文章 -- [Back-End Web Architecture](https://www.codecademy.com/articles/back-end-architecture/)：

- 客戶端(clients)：任何發送要求給後端的東西
- 伺服器(server)：接收需求的電腦
- 應用(app)：運行在伺服器上，接收要求，從資料庫取回資訊並送出回應(response)
- 資料庫(database)：組織及保管資料

[前面提到的這篇文章](https://rubygarage.org/blog/technology-stack-for-web-development)前段也有把client side, server side的軟體堆疊做成圖片幫助理解。

網頁應用需要一個伺服器來處理來自客戶端的需求，在這一塊的兩個主流的角色是：[^rubyGarage]

- Apache
- Nginx

伺服器上的應用，會基於HTTP verb及Uniform Resource Identifier(URI)決定如何回應客戶端送來的要求；HTTP verb與URI成對，被稱為route，配對他們與客戶端要求的行為稱作routing。

部分應用會被稱為中介軟體(middleware)；即伺服器在接收及發送回應的過程中被執行的程式。

中介軟體可能會修改要求(modify request)，且通常結束在傳送控制(passing control)給另一個中介軟體，而不是發送回應。

但最終，在中介軟體發送HTTP response給客戶端後，結束這一個request-response cycle。

常見的應用有Express(Node.js)或Ruby on Rails來簡化這個邏輯(the logic of routing)。

### 伺服器的回應(response)有哪些

取決於資料形式，例如：

- HTML file
- JSON data
- HTTP status code: e.g. 404 Not Found, 401 Unauthorized, 500 Internal Server Error

### 後端的資料存在哪裡

資料庫(database)：常見於後端應用，保存資料並減少主機CPU及記憶體的負載，讓資料在伺服器發生錯誤或失去電力時能保留下來。

目前有兩種資料庫，關聯式(relational)和非關聯式(non-relational)資料庫並且向下分枝，各有各的長處與短處，以下是常見的資料庫例子：[^rubyGarage]

- MySQL(關聯式)
- PostgreSQL(關聯式)
- MongoDB(非關聯式)

網頁應用也需要一個暫存系統來減少資料庫的負載，並處理大量的流量(traffic)，Memcached和Redis是最廣為使用的暫存系統。

### Web API

分兩個層面解釋，首先是API -- Application Programming Interface(API)是一個被清楚定義的辦法(method)，用來處理各個不同軟體元件間的溝通。

基本上可以聯想成是電腦的那些插孔，USB你可用插隨身碟，HDMI可以接螢幕；程式中會寫你要怎麼呼叫這支程式，比如JavaScript的`console.log(msg)`，可以讓你印出msg在瀏覽器的console(按F12可以看到)中，但你不必真的知道console.log是怎麼運作的(怎麼發指令給瀏覽器的)。

而Web API，則是把對象從程式改成網站，直接看例子好了，[這個網站](https://swapi.dev/)提供星際大戰角色資訊的API，把網站提供的[範例(網址)](https://swapi.dev/api/people/1/)貼給瀏覽器後，可以看到框裡有落落長的文字，但它似乎是有規律(格式)的，也就是上面有提到的JSON格式：

```json
{
    "name": "Luke Skywalker", 
    "height": "172", 
    "mass": "77", 
    "hair_color": "blond", 
    "skin_color": "fair", 
    "eye_color": "blue", 
    "birth_year": "19BBY", 
    "gender": "male", 
    "homeworld": "https://swapi.dev/api/planets/1/", 
    "films": [
        "https://swapi.dev/api/films/1/", 
        "https://swapi.dev/api/films/2/", 
        "https://swapi.dev/api/films/3/", 
        "https://swapi.dev/api/films/6/"
    ], 
    "species": [], 
    "vehicles": [
        "https://swapi.dev/api/vehicles/14/", 
        "https://swapi.dev/api/vehicles/30/"
    ], 
    "starships": [
        "https://swapi.dev/api/starships/12/", 
        "https://swapi.dev/api/starships/22/"
    ], 
    "created": "2014-12-09T13:50:51.644000Z", 
    "edited": "2014-12-20T21:17:56.891000Z", 
    "url": "https://swapi.dev/api/people/1/"
}
```

我可以透過JavaScript加工這筆資料，比如說，把它們做排版，放到我的網站上，顯示給使用者看：

---

#### 角色名稱: Luke Skywalker

身高：172公分
髮色：金
瞳色：藍

### 一覽要求(request)的過程

[完整版請見Codecademy(英文)](https://www.codecademy.com/articles/back-end-architecture/)，這邊我簡化過的說法是：

1. 使用者在逛購物網站，並點擊了商品圖片；這一行為送出一個GET request到`http://www.該網站.com/products/66432`
   GET是使用者對資訊送出要求，URI (uniform resource identifier) `/products/66432`指出使用者想要id: 66432的商品資訊
2. 伺服器接收到要求
3. 觸發對應的Event listeners，要求(HTTP verb: GET, URI: `/products/66432`。這段在要求與回應間的程式碼是中介軟體。
4. 在處理過程中，伺服器程式碼做出查詢指令給資料庫(database query)
5. 資料庫執行查詢後，送回資料給伺服器
6. 伺服器收到資料庫給的資料，並送回給客戶端，回應中會包含HTTP狀態碼: 200；代表要求成功。
7. 使用者的瀏覽器收到回應並解析程式碼
8. 使用者看到了點擊的商品圖片

## 框架(frameworks)

翻譯自這篇文章 -- [Get Started with Web Frameworks](https://web.archive.org/web/20180402231229/https://www.wired.com/2010/02/get_started_with_web_frameworks/)

通常後端框架提供函式庫(libraries)，用來訪問資料庫、管理工作階段(sessions)及cookies，創造樣板(templates)給HTML顯示，還有做出可以被重複使用的程式碼(promote the reuse of code)。

儘管沒有一個框架是單一功能的，但它們有各自有被做出來的目的，比如Ruby on Rails緊密整合JavaScript，使它在處理有大量使用Ajax的網頁上受到歡迎，並且它包含了Prototype JavaScript Library可以讓開發者自行整合。

> Ajax = Asynchronous JavaScript And XML；概念上用來讓網頁以區塊為單位更新，比如Facebook的評論、讚數等，有新留言或按讚時不會牽動到整個網頁頁面。

Django是被做來開發大型新聞網站，並提供自動生產網頁管理區塊(auto-generated site administration section)，給網站使用者創造、編輯和更新內容。並內建工具給暫存資料及建立彈性化URLs。

> Uniform Resource Locators(URLs)，即下圖那一串網址，更多資訊參見[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_URL)(為圖片出處)
> ![URLs](https://i.imgur.com/kIybzKI.png)

以下關於框架的內容參考自[^DEV]

### UI框架(User Interface Framework)

通常會提供grid system讓開發者可以排版，提供color schemes幫助管理顏色，以及提供套用CSS樣式化的HTML元件讓它們看起來更簡潔及專業。雖然這些也在前端的範圍，但通常，前端框架指的會是JavaScript的框架。以下列舉幾個UI框架：

- Bootstrap (CSS)
- Tailwind (CSS)
- Chakra (React)
- Material UI (React)

### 前端框架

大多數是用JavaScript寫成，並用來管理網站的功能及互動，以下列舉幾個前端框架：

- React
- Angular
- Vue
- Svelte
- Lit
- Solid

### 後端框架

撰寫的語言種類繁多，並有廣泛且多元的特性，以下列舉一些後端框架：

- Spring (Java)
- Django (Python)
- Flask (Python)
- Rails (Ruby)
- Express (Node.js)
- Phoenix (Elixir)

## CSS Preprocessor

因為CSS只是語言，也可以當成一種標籤，原本並沒有條件式(condition)或迴圈(loop)的功能；不能用if, for, while等來寫出內容，預處理器就是把這件事化為可能，用程式來編譯出CSS，常見的預處理器有：

- Sass (基於Ruby)
- LESS

> PostCSS寫自己不是預處理器，且能跟他們一起用。

## LAMP

翻譯自Stackoverflow上的這則回答 -- [What is a Web Framework? How does it compare with LAMP?](https://stackoverflow.com/a/4507543/18972098)

> 為Linux, Apache, MySQL, PHP/Perl,Python，首字組成的縮寫，是一個組件(package)包含了web server(Apache)，能夠讓網頁運作起來。相對的，框架只是加速程式開發的函式庫。

[^rubyGarage]: [Technology Stack for Web Application Development | RubyGarage](https://rubygarage.org/blog/technology-stack-for-web-development)

[^DEV]: [What is a Web Framework, and Why Should You use one? | DEV](https://dev.to/aspittel/what-is-a-web-framework-and-why-should-i-use-one-38c0)

[^treehouse]: [I Don’t Speak Your Language: Frontend vs. Backend | treehouse](https://blog.teamtreehouse.com/i-dont-speak-your-language-frontend-vs-backend)
