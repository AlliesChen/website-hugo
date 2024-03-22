---
title: "開始 CS61B 學習資料結構及演算法"
date: 2024-03-13T20:34:04+08:00
# weight: 1
# aliases: ["/Start_cs61b"]
tags: ["first"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "選擇哪個版本？怎麼啟用自動評分，開始 CS61B 的學習！"
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
    image: "images/a_computer_programmer.jpg" # image path/url
    alt: "a computer grammer sitting on before a white board" # alt text
    caption: "imagine learning computer science" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
editPost:
    URL: "https://github.com/AlliesChen/website-hugo/blob/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link 
---

因為剛開始要啟動這個課程的資源時繞了一點路，想簡單分享一下：

- 可以上哪個版本？
- 怎麼提交作業？

## 前言

CS61B 是 UC Berkeley 在電腦科學的「入門課」，關於它的討論在 PTT、一畝三分地等論壇，或是直接 google 都可以找到學習心得的分享，就不多在此贅述這門課程的特點。

課程以英文以及 Java 程式語言來教學，但不要求有 Java 基礎，課程中會逐步帶到。反倒是因為此課有前置課程-- CS61A，以 Python 程式語言教學電腦科學基礎的課程，在 CS61B 中會常聽到教授以 Python 作為對比來幫助理解。

延伸閱讀：[CS61A 跟 CS61B 的差別](https://csdiy.wiki/%E7%BC%96%E7%A8%8B%E5%85%A5%E9%97%A8/CS61A/)

## 可以上哪個版本？

首先可以打開瀏覽器，搜尋：CS61B，就能看到最新一季的課程頁面；以這篇文章撰寫的時間是 **CS61B Spring 2024**，在看到網頁中 [Course Info](https://sp24.datastructur.es/policies/#auditing-cs61b) 這個頁面的最下面，有個 "Auditing CS61B" 的條目-- 提到目前可以使用的 "public" autograder 是 Spring 2021。這門課程是透過 [Gradescope](https://www.gradescope.com/) 的服務來做自動化評分，而此頁提供的課程碼-- *MB7ZPY*，開啟的畫面如下圖。

![start_cs61b_gradescope_page_screenshot.jpg](/images/start_cs61b_gradescope_page_screenshot.jpg)

上圖中，可以看到還有個 CS61B Spring 2019，那就是非 "public" 的課程碼開啟的-- 如果是直接從課程網站的 week 1 開始跟著做，基本上影片都能看，直到跟著 [Lab 1](https://sp24.datastructur.es/labs/lab01/) 的教學到了註冊 Gradescope 並開始了第一個提交才會發現...提交會失敗呀😅。

## 怎麼提交作業？

先回到有公開評分器的 [CS61B SP21 的頁面](https://sp21.datastructur.es/materials/lab/lab1/lab1)，跟著 Lab 1 的說明：

1. clone 下 starter files

2. 完成指定功能的撰寫

3. 推上 github repo；整包 starter files 一起推就好，不用分資料夾開

4. 進到 Gradescope  CS 61B Spring 2021 課程中上選 Lab 1，再選擇你推上的 github repo，並選擇 branch "main" (現在預設名稱，過去則為 "master")

5. 等待評分完成✨

如果對於完成這個流程還是不太清楚，有前輩們也寫過更詳細的步驟及截圖可以參考：

- [[cs61b\] 建立作業github repo並clone官方starter code＋註冊gradescope | Yun-Chi Pang (yunchipang.github.io)](https://yunchipang.github.io/ucberkeley-cs61b-sp18-getting-started.html)
- [CS61B autograder排坑篇 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/115229260)

以上，感謝您的閱讀，希望能看到更多人加入學習這門課~
