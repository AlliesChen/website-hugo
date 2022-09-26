---
title: "WTM - D2 workshop 活動心得"
date: 2022-09-26T19:58:58+08:00
# weight: 1
# aliases: ["/D2_workshop"]
tags: ["murmur"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Reflecting on WTM D2 Workshop"
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

活動表訂從9:40到17:30，實際製作應該是5個小時左右，當天是一組四個組員，兩個工程師搭兩個設計師。

題目是 WTM 官網註冊頁及首頁，我只做了首頁...另一位工程師除了註冊頁，還能給大家開發及設計的建議，希望自己也能慢慢朝這個方向邁進。

這次實作要的技術東西都看過，都沒自己深入想過、做過😅，沒能好好的判斷作法，寫個筆記警惕一下自己：

## Web Vitals

`<img>`載入造成的版面位移，忘了寫個預設值的情況下可能影響[web指標](https://web.dev/vitals/)中的累積佈局偏移(CLS)，而且使用者要點東西的時候版面跳掉應該是蠻差的體驗。

## React

1. React 18 的 `useEffect` 在 render 時會跑兩次，原因參考[這篇文章](https://blog.bitsrc.io/react-v18-0-useeffect-bug-why-do-effects-run-twice-39babecede93)。

2. 正常在元件中使用 `setTimeout` 來觸發 re-render 拿 state 不會有更新位址的問題，想太多了 - [寫了個例子](https://codepen.io/allieschen/pen/WNJXwBq)。這個作法我用來實踐題目中輪播圖功能。
   
    會有事的是在Line群上看到群友提出的[這個情境](https://codepen.io/huibizhang/pen/rNdPpJx?editors=0011)；在useEffect中使用setTimeout必須配合 `useRef` 來更新 re-render 後的 state 位址，才能穩定的每秒倒數並考慮更新的 state - [該情境可行的解法](https://codepen.io/huibizhang/pen/yLKZvNO?editors=0011)。
   
    至於不會穩定的每秒倒數則是我嘗試想解題時的[這個例子](https://codepen.io/allieschen/pen/poLGpGv)；每次觸發按鈕確實更新了 log 中的 state，但計時也會跟著重置，連按的情況下就會等最後一下按完開始算一秒才有 log。😥

## Tailwind CSS

1. Tailwind CSS 預設的 utilities 想用[自訂值](https://tailwindcss.com/docs/width#arbitrary-values)只要個加方框...

2. 透過CDN引入一樣可以寫 configure, utilities 和用官方 plugin，作法參考[官方文件](https://tailwindcss.com/docs/installation/play-cdn)。我應用這個在漢堡選單的展開，寫了個影格及動畫。

## 關於codesandbox

不知道怎麼解，但純記錄下來：

1. 嘗試了在 codesandbox 用非CDN的方式引入 Tailwind CSS，看了幾個設定好的環境，不是把 stylesheet 整個輸出出來，就是要改Node.js做環境，但我只是想要不顯示scrollbar...。 

2. 環境跑 HMR 時好像會讓 `useEffect` 變得怪怪的，有狀態殘留、重複觸發，但重整後又沒事了，也可能是我的寫法不安全造成的？🤔

3. 沒有確認好展示時要用的環境，雖然開發時有跟工程夥伴說好用codesandbox的mobile preview為基準開發，但它這個preview就算是用內建的open in new window，版面也會變得不同，也沒再跟全部組員確認demo時會用什麼環境，結果就有很重要的東西跑板了...

4. 不知道為什麼做著做著`<img>`中原本抓的到的svg就抓不到了...把那個檔案改個名字又正常了。

## Murmur

輪播圖的功能不知道一開始我怎麼寫的，`useEffect` 的安全機制讓 `state` 一直以2為間距在跳，搞不懂機制的情況下後面在 `transform` 跟 `transition` 間的設定卡了很久...該學習用 [Swiper](https://swiperjs.com/) 了。

react-router 也是更新的很勤，google要怎麼達成`history.pushState()` 的效果看到要用 `useHistory` ，試了一下一直報錯說沒有這個函式，才又發現 v6 開始變成 `useNavigate`。
