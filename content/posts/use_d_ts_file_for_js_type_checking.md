---
title: "使用`.d.ts` 檔給JavaScript加入型別檢查"
date: 2022-07-23T20:20:03+08:00
# weight: 1
# aliases: ["/use_d_ts_file_for_js_type_checking"]
tags: ["TypeScript"]
author: "Yu-Pung"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "透過註解的方式給文字編譯器知道變數的型別"
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



前言：

因為目前專案都是使用 JavaScript(文章中簡稱JS) + Vue 2 寫的，很多事發生在`window`底下，要知道某個變數是什麼、從何而來，又要去哪裡是件極度痛苦的事。因此開始尋找不用改寫原始程式邏輯，又能夠應用TypeScript(文章中簡稱TS)方式。

- `.d.ts` -- declaration file
- JSDoc
- Triple-slash directives

這篇文章要應用這三樣幫`.js`檔案套上TS型別裝甲；第一個是實際會存在的檔案，後兩個則是檔案內的註解。

## TL;DR

> `<declaration>`請替換成你的檔案名稱

1. 在JS檔案中加入`/// <reference path="<declaration>.d.ts">`
2. 在`<declaration>.d.ts`這個檔案內編寫TS定義
3. 把定義的名稱透過JSDoc的語法註解到JS檔案中

## `.d.ts`型別宣告檔

這個檔案是給TypeScript使用的，如果是用VSCode，不用安裝也可以用滑鼠去指JS檔案中的變數，會看到型別註解。

但要編譯`.ts`的檔案就還是要安裝TS，請參考官方頁面 -- [TypeScript: How to set up TypeScript](https://www.typescriptlang.org/download)

---

在 [About  "*.d.ts" in TypeScript - Stack Overflow](https://stackoverflow.com/a/21247316/18972098) 這個回答中提到：

> The ".d.ts" file is used to provide typescript type information about an API that's written in JavaScript.

這個`.d.ts`的副檔名呢，簡單來說，就是我們JavaScript的型別參照檔案。

使用同樣來自該則回答內舉例的檔案及資料夾結構：

|- src/  
|- |- thing.js  
|- |- thing.d.ts  
|- |- index.ts  ~~~~

在`.js`檔案寫下變數定義：

```javascript
// @filename: thing.js
let thing = 42;
export { thing };
```

然後在建一個同檔名但副檔名是`.d.ts`的檔案：

```typescript
// @filename: thing.d.ts
export declare const thing: number;
```

然後在`index.ts`檔案引入後，就可以看到`.d.ts`的型別參照有反映到我們從thing.js引入的變數上了：

```typescript
// @filename: index.ts
import { thing } from "./thing";

// from thing.js
console.log(thing); // (alias) const thing: number

// from thing.d.ts
type TypeOfThing = typeof thing; // type TypeOfThing = number
```

如果只是需要幫後續要用TS寫的檔案做過去檔案的參照，到這邊就可以了；但我的需求是：在thing.js這個檔案，看到thing.d.ts的型別參照，或是把`.d.ts`檔案內定義的型別，加在`.js`的檔案中，所以，還沒結束...

既然index.ts中有引入定義，那我就在thing.js內`import { thing } from "./thing"`，然後還真的VSCode提示`// (alias) const thing: number`，又成功了，真簡單；接著我們可以執行這個index.js：

> SyntaxError: Cannot use import statement outside a module

Okay...不能這樣做🥲

---

> 👋 我使用Node.js執行這個檔案，因為Node.js預設的module loader是CommonJS，要在package.json中加入`"type": "module"`，來改用ES Module loader，不然import和export的語法會報錯。

## JSDoc

規範文件：[Use JSDoc: Index](https://jsdoc.app/index.html)

JSDoc可以想成是一個註解的規範，這些註解目的在幫助讀程式的人更好理解這段程式(內)變數的定義，不算新的東西了。

[JavaScript 註解 & 型別檢查 – 竹白記事本](https://chupai.github.io/posts/2102/comments/)這個網站很清楚的說明了這個JSDoc的基本語法，而且也提到了這個段落的意義：配合TS的支援，讓我們的`.js`檔案在VSCode中得到型別檢查：

> 也可以參考官方文件 -- [TypeScript: Documentation - JSDoc Reference](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)

```javascript
// @ts-check

/** @type {number} */
let thing = 42;
export { thing };

console.log(thing); // expects: 42
```

現在滑鼠移到test上應該可以看到型別提示：

```
let thing: number
@type--number
```

並且，若在檔案程式碼中加入一行`thing = "test"`，可以看到<u>thing</u>在編譯器上出現紅色的底線並提示：

> Type 'string' is not assignable to type 'number'.

如果不是要拿到`.d.ts`中的定義來用在`.js`檔案，我們用JSDoc語法就能完成簡易的型別檢查了。

## Triple-Slash Directives

接下來，我們需要`/// <reference />`這個XML標籤讓我們結合JSDoc語法和`.d.ts`中的型別定義；這是TS的功能，請參考：[TypeScript: Documentation - Triple-Slash Directives](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html#handbook-content)。

讓我們在thing.js這個檔案的最上頭加入`/// <reference path="thing.d.ts" />`：

```javascript
/// <reference path="thing.d.ts" />
// @ts-check

/** @type {thing} */
let thing = 42;
export { thing };

console.log(thing); // expects: 42
```

把`@type{number}`改為`@type{thing}`，這邊我期待的是：

- thing成為了一種type
- 這個type的定義來自檔案最上方宣告的reference

但是，VSCode會提示這邊產生了幾個問題：

1. `thing`跟`@type{thing}`，這個thing撞名了
2. 就算把`let thing = 42`改為`let something;`，`@type{thing}`還是沒作用

問題出在thing.d.ts的`export`，只要把它拿掉：

```typescript
declare const thing: number;
```

可以注意到滑鼠移過去`let something;`現在會提示是：

```
let something: number
@type--{thing}
```

如果拿掉上面的`@type`，則會是`any`，沒錯，thing.d.ts的定義過來了。

## 還有一些問題

還記得一開始的index.ts嗎？裡面有段`import { thing } from "./thing";`，因為thing.d.ts現在沒有`export`了，所以這段會顯示錯誤。

考量這篇文章一開始的目標 -- 不要動到原始的JavaScript檔案，也就是thing.js，我這邊調整的是thing.d.ts：

1. 改名為types.d.ts
2. 改為`declare const numThing: number;`

在thing.js就要對應把第一行的reference改為types.d.ts，以及`@type {numThing}`。

## 結語

關於`.d.ts`檔案要怎麼寫，基本上就是TS正常寫法，但每個變數宣告前加入`declare`，可以參考[這個回答](https://stackoverflow.com/a/65585157/18972098)，然後回答中有提到一個字 -- "ambient"，可以透過這個字，在官方文件中找到更多宣告的細節，也可以先參考[這個回答](https://stackoverflow.com/a/61082185/18972098)和[引用的內容](https://github.com/Microsoft/TypeScript-Handbook/issues/180#issuecomment-195452691)建立觀念。

如果只是要粗略的型別，**JSDoc**(必須是有TS支援的宣告)就可以完成了，還可反向用這些註解從JS檔案配合`tsconfig.json`的設定輸出`.d.ts`檔案，方法請見：[TypeScript: Documentation - Creating .d.ts Files from .js files](https://www.typescriptlang.org/docs/handbook/declaration-files/dts-from-js.html)

`/// <reference path="<declaration>.d.ts" />`還有另一個寫法是`/// <reference types="<declaration>" />`省去寫副檔名的部分。
