---
title: "透過 CSS 製作書本翻頁效果"
date: 2023-09-19T23:29:44+08:00
# weight: 1
# aliases: ["/Page-Flip Animation"]
tags: ["CSS", "Animation"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "配合 react-spring, use-gesture 操作 CSS 來做翻頁動畫"
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
    appendFilePath: true # to append file path to Edit link# 
---

近期想透過「翻頁」這個互動方式來做一個 web 小遊戲，過程中發現意外地不好實現。

這篇文章需要讀者對 React 這個前端框架及 CSS 的 transition 屬性的應用有基本的了解，成果及原始碼是建立於這兩個技術之上的，內文會大略地對實踐的方式說明，但不會詳細地提及所使用的框架及函式庫確切 API 的功能及用法。

## TL;DR

成果: https://codesandbox.io/s/page-flippable-book-5h5ktm?file=/src/Page.tsx

## 實踐方式

- 透過 `position: relative/absolute` 來堆疊書頁的正反面
- 用 `z-index` 切換堆疊的順序
- [react-spring](https://www.react-spring.dev/) 控制 `rotateY`、`opacity` 做翻頁轉場
- 配合 [`perspective`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/perspective) 屬性改變視角增添立體感
- [use-gesture](https://use-gesture.netlify.app/) 加入拖曳翻頁功能

## ~~讓套件再次偉大~~

首先我想到的是有沒有現成的(免費)翻頁書效果套件可以用，

- [turn.js](http://www.turnjs.com/)-- 建立在 jQuery 之上的套件，快十年沒更新了，考量後續擴充功能可能有困難，暫不考慮。
- [StPageFlip](https://github.com/Nodlik/StPageFlip)-- 效果很擬真，從 demo 看起來使用體驗流暢；而且有 [React 版本](https://github.com/Nodlik/react-pageflip)，但是，從 commits 看來有兩年沒更新了，以 front-end 的迭代速度加上，擔心搭 React 18 會不會出現不明的坑因此沒有使用。

後續又看了幾個套件(比較新的)好像也是搭在這些套件上實踐出來，覺得難以在短期理解其運作方式，怕未來不好改。

## 純 CSS 的現成範例

- [Notebook page flip animation](https://scrimba.com/scrim/c6GV2Ay) on scrimba-- 程式碼很單純，效果簡單，但能夠接受；嘗試了一陣子，發現在呈現次頁的內容上，一旦有多頁，雖然做得到，但程式碼可讀性不是那麼好，擔心後續增加應用，改動起來會有問題，而且試做的是動畫的版本，實際上我需要的是手動觸發。

這邊，我認為應用 [`transform`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform) 有搞頭，又看到 MDN 裡的範例有 `matrix` (最近剛好在看[線性代數](https://www.3blue1brown.com/topics/linear-algebra)的教學影片)，拿[這個網站](http://www.eion.com.tw/Blogger/?Pid=1168)沙盤推演了一下，我以為能透過操作`scaleX` 和 `skewY` 的矩陣轉換來做出翻頁效果殊不知...

這個作法下 `backface-visibility: hidden` 不管用；也沒有 rotate 能用，翻頁效果有奇怪的抖動。

<iframe height="300" style="width: 100%;" scrolling="no" title="page-flip animation with matrix" src="https://codepen.io/allieschen/embed/NWOQxwx?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/allieschen/pen/NWOQxwx">
  page-flip animation with matrix</a> by YPChen (<a href="https://codepen.io/allieschen">@allieschen</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

- [Book page flip animation](https://codepen.io/pizzabote/pen/xxxXmXN) on Codepen-- 實踐單純，效果也不錯，嘗試讀改了一下程式碼，發現有些可以簡化的地方，主要的問題在 `position: relative/absolute` 堆疊使用有點複雜，還用上了 `overflow: hidden` 遮蓋。

從這個例子中，我確信了可以用 `rotateY`、`perspective`，加上 `z-index` 切換當作主要實踐概念。

---

題外話：製作期間突然好奇如果是設計師會怎麼做，得知有個叫 Flip PDF Pro 的軟體，拿來當關鍵字查發現製作翻頁書網頁的應用蠻多的，只是我需要的是造輪子的方法。

另外也被建議說可以從 PPT 過場動畫的方向去想，也是我上面查 CSS 範例的想法來源；

- [figma 翻頁動畫](https://www.figma.com/community/file/1000299056910046829/Magical-Page-Flipping-Animation)-- 這個例子是一格一格的排出來
- 也有看到蠻多 youtube 影片是教用 Adobe After Effect 做

## Animation library for React

React 強大的生態系，有好多很棒的 animation library 可以選，這也是我選擇用 React 製作的原因：

- [Framer Motion](https://www.framer.com/motion/)-- 官網的互動範例看起來很好上手
- [react-spring](https://www.react-spring.dev/)-- 使用在[這個例子](https://www.reddit.com/r/reactjs/comments/dl8eqp/i_made_a_book_with_flipping_as_in_life/)，完美的呈現了我想要的效果

> [那個例子](https://github.com/pylnata/livebook)的原始碼實踐中看來有複雜的轉換...我理解不能，所以沒有 fork 直接使用。他也用了 use-gesture，所以我後續也選這個做 drag-n-drop 應用。

純 JS 也有關於動畫的套件，比如：

- [GSAP](https://greensock.com/gsap/)
- [Three.js](https://threejs.org/)

> Vercel [這一篇](https://vercel.com/blog/building-an-interactive-webgl-experience-in-next-js)文章分享他們怎麼用 three.js 做出 Next conf 的活動頁面。

這兩個 lib 的進入門檻都不低，又 react-spring 有人做出過我想要的效果，因此我也選擇了它。

## 使用 react-spring 實踐翻頁動畫

首先參考了 react-spring 官網的[這個例子](https://codesandbox.io/s/cju2d?file=/src/App.tsx)做為轉場實踐的基底，並把頁面與圖面的動畫分開；也就是，翻頁與圖片變化是分開的 `useSpring` 管理：

- `rotateY`-- 翻頁效果都靠它 
- `opacity`-- 讓翻轉效果看起來更自然

然後是不在 `useSpring` 管理的：

- `z-index`-- 正反面圖片的變化是它
- `transform: perspective`-- 這個提供視角的變化，讓翻頁效果從消失點延伸，視覺上更擬真
- `transformOrigin`-- 改變翻頁的軸心

```tsx
// @/App.tsx
import { useState } from "react";
import { useSpring, animated } from "@react-spring/web";

const [flipped, setFlipped] = useState(false);
const pageStyle = useSpring({
  rotateY: 0,
  config: { mass: 5, tension: 500, friction: 150 },
  touchAction: "none"
});

return (
    <animated.div
      style={{
        ...pageStyle,
        transformOrigin: "left",
        transform: "perspective(600px)",
        zIndex: flipped ? 10 + zIndex : 100 - zIndex
      }}
      className="page"
    >
      ...the pictures
    </animated.div>
)
```

> 關於 config 屬性的作用，引用自 [Getting started with react-spring: physics, API, performance - Apptension Blog](https://www.apptension.com/blog-posts/getting-started-with-react-spring-spring-physics-api-performance)
> - Mass (when mass is higher, the element needs more velocity to be moved and more time to stop)
> - Friction (higher friction reduces velocity and bounciness)
> - Tension (higher tension reduces the impact of friction).

確保單頁的效果可以之後，我就把它封成一個元件-- `FlippablePage`。

接著是放複數功能頁形成一本書，先透過 `position` 屬性讓各功能頁脫離預設的排版，堆疊在一起，然後調整 `z-index`；前面的頁數 `z-index` 為 x 從 0 算起，元件中使用邏輯為

- 尚未翻頁 = $100 - x$
- 翻頁後 = $10 + x$

> 聰明的你應該會發現，當 x = 45，也就是頁數來到 46 頁時，這個算法會出現問題，但因為我的應用不會到這個頁數，也應該能用 [virtual list](https://www.patterns.dev/posts/virtual-lists) 的方式避免這個問題，所以沒關係

這邊遇到一個問題，如果把 `z-index` 傳入 `useSpring`，在正反面調換時，會產生延遲，所以改直接給進 `animated.div` 的 `style` 屬性中。

> 在製作前以及過程中使用盡量少的元素來達成想要的動畫效果，因為曾被社群中群友的提醒，在 React 的機制中操作動畫，可能會產生預期外的效能問題

到此，已經有翻頁書的樣子了。

## 使用 🖐️use-gesture 實踐拖拉翻頁

參考 use-gesture 官網的[這個範例](https://use-gesture.netlify.app/docs/state/#movement-and-offset)，提供了跟 `useSpring` 一起使用的作法。

接著就慢慢試怎麼樣的屬性和邏輯呈現出來的翻頁效果可以接受，又不會太複雜😵‍💫

> 注意為了拿 `useSpring` 的 api 物件給進 `useDrag` 裡操作，參數會從物件改為一個箭頭函式

```tsx
import { useState } from "react";
import { useDrag } from "@use-gesture/react";

const [flipped, setFlipped] = useState(false);
const [pageStyle, api] = useSpring(() => ({
  rotateY: 0,
  config: { mass: 5, tension: 500, friction: 150 },
  touchAction: "none"
}));
const bind = useDrag(({ movement: [movX], cancel, dragging }) => {
  // console.log(movX, pageStyle.rotateY.get());
  const currentRY = pageStyle.rotateY.get();
  if (currentRY > -60) {
    api.start({ rotateY: 0 });
    setFlipped(false);
  }
  if (currentRY <= -60 && (movX < 0 || !dragging)) {
    api.start({ rotateY: -180 });
    setFlipped(true);
  } else if (dragging && currentRY <= 0) {
    api.start({ rotateY: currentRY + movX });
  }
});
```

> 一個小插曲是要注意圖片載入速度，會影響翻頁動畫的流暢度(會卡住)

最後，打開封面後，書本寬度改變，一來會蓋到邊界超出畫面，二來沒有置中，所以在 `FlippablePage` 加入一個 `onFlipped` 屬性透過 `useEffect` 來觸發 callback-- `onFlipped`，做 `translateX` 的改變。
