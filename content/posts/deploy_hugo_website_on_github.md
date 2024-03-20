---
title: "使用 Hugo 產生一個部落格並部署在 GitHub 上"
date: 2022-07-16T13:59:00+08:00
# weight: 1
# aliases: ["/deploy-hugo-website-on-github"]
tags: ["Hugo"]
author: "Yu-Pung"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "想做一個部落格來放點什麼？"
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

## Hugo?

> "Hugo is a static HTML and CSS website generator written in Go."
> -- [Hugo's GitHub](https://github.com/gohugoio/hugo)

簡單來說，你只要照著操作指南進行，就能產出部署網站需要的檔案。

- HTML 和 CSS 是標籤語言，分別做為網頁的文字骨架及渲染設定。
- [Go](https://go.dev/) 是一種程式語言。

## 建立

首先我參考了這篇文章：[Hugo 搭配 Github page 架設靜態部落格 | JCYH Develop Blog](https://jcyh0120.github.io/posts/blog-with-hugo-and-github-page/)，以及文章內提到的參考文章，步驟上蠻清楚的，但因為我的作業系統是*Windows*：

另外要依照官方的[安裝指南](https://gohugo.io/getting-started/installing/#windows)操作，也推薦跟著指南中的[影片](https://youtu.be/G7umPCU-8xc)進行。

1. 取得.exe檔後，要在C:\下創建Hugo\bin\，並把解壓縮的檔案放到bin資料夾內。
   - 透過 `Windows key + R` 叫出執行視窗，輸入 `cmd` 叫出Command shell並移動到創建的bin資料夾下。
   - 輸入 `hugo version` 可以確認Hugo是否有安裝成功。
2. 要把 `hugo` 這個指令增加到Windows環境變數 (environment variable) - `PATH` 中。
   - 新增這個位址 `C:\Hugo\bin` -- 也就是我們創建來放Hugo相關檔案也是網站文章的資料夾。
   - 在cmd用 `echo %PATH%` 可以看到PATH中現有的變數，有看到hugo被加入就是成功，或也可以離開 `C:\Hugo\bin` ，然後輸入 `hugo version` ，看到資訊就是成功了。(如果不行可以先關掉cmd再重開打指令確認)
   - cmd 在 `C:\Hugo\` 下輸入 `hugo new site <folder_name>` ，folder name的名字可以取自己喜歡的，我採用與參考的文章相同的名稱：website-hugo。

## 套用主題

這裡應該與作業系統無關，要幫我們的網站套用佈景主題，可以到[Hugo主題庫](https://themes.gohugo.io/)選擇自己喜歡的，步驟上同參考文章，但一樣有遇到一些情況：

- 參考文章內提到要把下載下來主題的static、layouts換成主題資料夾內的，但我使用的主題[PaperMod](https://adityatelange.github.io/hugo-PaperMod/)沒有static只有layouts，這部份就只要取代layouts就好。

- 要記得看選擇的主題的GitHub說明來確認如何使用及操作；我一開始想用Doks這個主題，但他似乎有用到Node.js npm，我不能在我的網站資料夾下(`C:\Hugo\website-hugo`)使用 `hugo server -D` 來啟動本地端伺服器(測試用)。

- 另一方面，在config.toml，或是config.yml（也是取決於選擇的主題設定），那個 `theme: <theme_name>` ，是取決於抓下來的主題資料夾的名稱的，像Doks這個主題我clone下來是得到my-doks-site（可以改名），然後是用toml格式，我就要寫 `theme = "my-doks-site"`。

## 使用模板文章

可能也取決於下載的主題，我使用的佈景主題PaperMod的[文章模板](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-installation/#sample-pagemd)；將這個內容複製並依照文章指示放到archetypes/post.md內，就可以用 `hugo new --kind post <filename>.md` 這個指令在content資料夾下產生檔案，再移到posts資料夾內就可以在網站中看到了。

他有一些設定要注意，像是date，要把UTC的時區寫正確，像台灣是 `+08:00` ，如果沒寫的話，好像會被認為是未來貼文，所以就不會顯示在網站上。（待測試：感覺可以用來預約發文？）

```markdown
---
title: "my post title"
date: 2022-07-16T16:00:00+08:00
# weight: 1
ShowReadingTime: false
ShowWordCount: false
---
```

另外像 `weight` ，如果是1就是置頂，預設是註解掉的(不使用這個參數)。

然後，可能因為我用的是中文， `ShowReadingTime` 和 `ShowWordCount` 這兩個顯示的數值不太可靠，所以我把它們改為false（不顯示）。

> 部份設定像weight，可以在官網[Front Matter](https://gohugo.io/content-management/front-matter/)的主題找到更詳細的說明。（因為有些是主題客製的，就要參考主題的文件說明了）

## 部署到GitHub

推送部份就如參考文章中所述，但一樣有遇到問題：

GitHub頁面開起來沒有吃到stylesheet，就是純文字，看了console發現有跳錯誤： `Failed to find a valid digest in the 'integrity' attribute for resource css with computed SHA-256 integrity hugo`

這邊借助Google大神，找到了[這個辦法](https://stackoverflow.com/a/65052963/18972098)：

1. 用編輯器打開 `head.html` ，這邊可能又依主題不同檔案位址不同，PaperMod我是到複製的layouts資料夾(不是themes裡的)，然後partials資料夾。

2. 把 `integrity` 照上面Stackoverflow的回答，改為`""`：（不只一條有integrity這個屬性，但我只動rel="preload stylesheet"這條的就可以了）
   
   ```html
   <link crossorigin="anonymous" href="{{ $stylesheet.RelPermalink }}" integrity="" rel="preload stylesheet" as="style">
   ```

另外，我有用參考文章中提及的另一篇參考所寫到，再開一個GitHub repo，放build到public前的原始檔案；因為PaperMod這個主題的模板文章，可以在各篇文章標題作者旁用一個Suggest Change的連結，目的就如字面上所示，但因為public做出來都是html了，連過去也不好閱讀和發pull request，乾脆再開一個放原始的.md檔們。

- 在public外的這個資料夾，我新增了 `.gitignore` ，把public和themes排除在推送的內容外，因為public的內容已經在blog部署的repo上了，而themes的內容都是抓下來的，沒必要推上去。

## 深入學習

[Hugo - Static Site Generator | Tutorial - YouTube](https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3) -- 這個影片清單有更多關於 Hugo 設定的說明。

## 題外話：Markdown編輯器

因為 Hugo 使用的檔案格式是 .md (markdown)，除了最強編輯器 VSCode 可以編輯外，我也會使用 [MarkText](https://github.com/marktext/marktext) 以及 [Obsidian](https://obsidian.md/) 這兩款編輯器；介面比 VSCode 更像個文件編輯器， MarkText 用來開單個檔案，因為 Obsidian 需要透過指定資料夾的方式才能開檔案，但也因此 Obsidian 很適合建立及管理文件庫，端看使用習慣。
