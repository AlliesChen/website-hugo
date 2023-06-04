---
title: "Debounce 和 Throttle"
date: 2022-11-19T23:30:06+08:00
# weight: 1
# aliases: ["/debounce_and_throttle"]
tags: ["javascript", "vue"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "網頁效能改善技巧"
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



針對像是輸入、鼠標移動，和頁面滾動...等可能在短時間被使用者大量觸發的事件做觸發限制，

- debounce 是群組短時間觸發的多個事件成一次執行的動作
- throttle 是觸發事件後進入冷卻時間

避免網頁在短時間內要對大量觸發的事件做出反應，而花費過多處理效能。

- [Live demo and source code on stackblitz](https://stackblitz.com/edit/vitejs-vite-2tw9n9?fbclid=IwAR3hw63c0h--dDKV0gtrFUtcGm5Nr53yuzNfjjo_znN4oQWkTr0JCpqnHT4&file=src%2FApp.vue)
