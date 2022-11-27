---
title: "如何避免 React 重新渲染元件"
date: 2022-11-27T21:56:57+08:00
# weight: 1
# aliases: ["/React Re Render"]
tags: ["React"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "There are ways for conditions to prevent React to re-render a component"
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

因為面試被問倒 `useCallback`、`useMemo` 的用途和用法，還有如何避免 React 做無謂的重新渲染 (re-render)，特別是最後一個當下我完全沒有頭緒...

> 小提示：使用 `React.memo`，整理筆記發現有記過這個，但看來真的只是寫下來 😅

事後亡羊補牢時看到 Debug Bear 的[這篇文章](https://www.debugbear.com/blog/react-rerenders#children-props)；文章中漸進式的說明與例子終於讓我比較看懂這三個方法對重新渲染的影響，另外也提到了 list 元素中，React 會提示需要提供給元件的 `key` 屬性也是影響是否重新渲染的因子之一。

我參考文章中的例子，寫了個應該算是互動式的應用範例：

> [react-re-render - StackBlitz](https://stackblitz.com/edit/vitejs-vite-4njj4q?file=src/components/Question1.tsx)

範例中因為版面有限，主要透過題目中的簡短說明，期望使用者配合閱讀程式碼，及程式碼中設定 `console.log` 印出的資訊來瞭解這四個主題 -- 總共有 9 個問題。

這篇文章則是進一步描述每個主題的癥結點，提取出關鍵的程式碼部份做說明 (因此會與實際範例中的程式碼有差異)，但不會細部的去分析這些方法原始碼的運作概念 (因為我也還不懂)。

## React.memo

問題 1 及問題 2 是針對 `React.memo` 的應用，

```typescript
// @filename: Question1.tsx
export default function Question1() {
    const [answer, setAnswer] = useState<boolean | null>(null);
    return <Selection setAnswer={setAnswer} />
}
```

因為 `Selection` 有設定 `onClick` 會觸發 `setAnswer` 改變 Question1 元件 `answer` 的值，因此造成整個元件重新更新。

透過使用 `React.memo` 當 `Selection` 沒有特別的改變時，比如從母元件接收到的 props 沒有變化，React 就不會重新渲染它，也就是 Question2 想展示的：

```typescript
// @filename: Question2.tsx
const Selection = React.memo(function Selection({setAnswer}) {
    // ...
});
```

## useCallback

問題 3 及問題 4 是針對 `useCallback` 的應用，主要的變化在給 `Selection` 這個元件 `setAnswer` 的值的變化，變成了給 arrow function 而不是在 React 管理下的 setState 函式；儘管有使用 `React.memo`，但每次 `Question3` 狀態改變就會產生一個新的 arrow function 給 `Selection` 也因此會被 React 判斷為元件拿到的 props 有更新，進而有重新渲染。

```typescript
// @filename: Question3.tsx
export default function Question3() {
    return <Selection setAnswer={(ans) => setAnswer(ans)} />
}
```

而這邊的解法，就是用 `useCallback` 把這個 arrow function 的輪廓記起來：

```typescript
// @filename: Question4.tsx
export default function Question4() {
    return <Selection setAnswer={useCallback((ans) => setAnswer(ans), [setAnswer])} />
}
```

注意，`useCallback` 的第二個參數是一個陣列，可以給變數做為 dependencies，當 dependencies 中有任一值改變時，就會觸發重新渲染，這邊選用 `Question4` 的 `setAnswer` 做為是否重新渲染的依賴。

## useMemo

問題 5 和問題 6 是針對 `useMemo` 的應用，這兩個例子中 Question 元件中有個 `childStyle` 物件會做為 props 傳給 `Selection` 元件做為最外層 `div` 元素的 inline style 的 CSS 屬性設定使用；並且裡面屬性的值會依據 Question 中 state 值有所不同，

```typescript
// @filename: Question5.tsx
export default function Question5() {
    const [answer, setAnswer] = useState<boolean | null>(null);
    const childStyle = {
        fontWeight: answer ? '500' : '700',
        color: answer ? '#fff' : 'cyan',
    };
    return <Selection style={childStyle} setAnswer={setAnswer} />
}

const Selection = React.memo(function Selection({style, setAnswer}) {
    return (
        <div style={style}>
            {/*...*/}
        </div>
    )
});
```

這裡的問題與 `useCallback` 該節中提到的一樣，因為 `childStyle` 在 `Question5` 的 `answer` 狀態被子元件觸發 `setAnswer` 給改變而重新渲染，又物件裡的值會依據 `answer` 的值有所不同，現在 React 認為它不一樣了。

我們可以使用 `useMemo` 來記住這個物件，並且跟 `useCallback` 一樣指定 dependencies：

```typescript
// @filename: Question6.tsx
export default function Question6() {
    const [answer, setAnswer] = useState<boolean | null>(null);
    const childStyle = useMemo(() => ({
        fontWeight: answer ? '500' : '700',
        color: answer ? '#fff' : 'salmon',
        }),
        [setAnswer]
    );
}
```

可以注意到，因為使用 `setAnswer` 做為 dependencies，`childStyle` 並不會在 `Question6` 重新渲染時被改變，雖然子元件 `Selection` 沒有被重新渲染了，但也因此 inline style 沒有改變，這可能不是好的 UX 表現。

## List Elements' Key Property

問題 7 到問題 9 是針對 list 元素中，`key` 這個 props 的作用，

```typescript
// @filename: Question7.tsx
const descriptions = [
    {
        name: 'foo',
        content: '母元件透過陣列產生 list 元素，沒有綁 key 屬性',
    },
    {
        name: 'bar',
        content: '點選回答會讓 list 元素的順序調換',
    },
];

export default function Question7() {
    const [items, setItems] = useState(descriptions)
    return (
        <ul>
            {items.map((item) => (
                <ListItem item={item} />
            ))}
        </ul>
    )
}

const ListItem = React.memo(function ListItem({item}){
    return <li>{item.content}</li>
});
```

首先 console 就會跳錯誤說透過 `map` 產出 `ListItem` 元素需要加入 `key` 屬性幫助 React 辨識，所以不意外的 `Question7` 重新渲染後 `ListItem` 也跟著重新渲染了。

那麼在問題 8 我們加入 `key` 這個屬性，用的是 `map` 會自帶的 `index` 參數，

```typescript
export default function Question7() {
    const [items, setItems] = useState(descriptions)
    return (
        <ul>
            {items.map((item, index) => (
                <ListItem key={index} item={item} />
            ))}
        </ul>
    )
}
```

然後子元件 `Selection` 還是會重渲染；我這邊的操作是反轉陣列中物件的順序，`index` 並非唯一獨特的值，而是 0 和 1，反轉後原本 `name: "foo"` 的物件變到 index = 1 的位置，`name: "bar"` 則變到了 index = 0 的位置；React 官網中有提到這會產生非預期的表現，比如這裡，就被當成了兩個與原先不同的子元件，觸發了子元件的重新渲染。

這樣講或許不好懂，所以來看問題 9，用對比的方式會比較清楚是否為唯一獨特的 `key` 產生的差異；將物件中的 `name` 屬性做為 `key`，現在它們是：

```typescript
export default function Question7() {
    const [items, setItems] = useState(descriptions)
    return (
        <ul>
            {items.map((item) => (
                <ListItem key={item.name} item={item} />
            ))}
        </ul>
    )
}
```

可以看一下 `console.log` 印出的狀況，應該可以發現子元件 `Selection` 就算觸發了順序調換，也沒有被重新渲染了。

## 題外話：動態載入元件 (lazy loading)

為了動態的切換元件，我參考了 Digital Ocean 的[這篇文章](https://www.digitalocean.com/community/conceptual-articles/react-loading-components-dynamically-hooks)，也學到了怎麼用 React 的 `lazy` 配合 `Suspend` 元件，做 lazy loading。

Joe Chang 在 Medium 上的[這篇文章](https://www.digitalocean.com/community/conceptual-articles/react-loading-components-dynamically-hooks)也不錯，比較簡單扼要，而且是中文的。

> 文章中提到 `lazy` 必須搭配 `Suspend` 使用，不然會報錯，另外 `Suspend` 元件需要 `fallback` 屬性指定使用 `lazy` 的元件載入完成前做為備案顯示的元件。

Aleksandr Hovhannisyan 的[這篇文章](https://www.aleksandrhovhannisyan.com/blog/react-lazy-dynamic-imports/) 有把 Vanilla JS 中的 lazy import 的做法寫出來，另外也提到了：

> 配合 React 的 `lazy` 必須進行動態載入的元件，必須是能使用 `import default` 引入的。 (但 Vanilla JS 的做法中使允許使用 named import 的)。

> [Code-Splitting | React Docs](https://reactjs.org/docs/code-splitting.html#named-exports)
