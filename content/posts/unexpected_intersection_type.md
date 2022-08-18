---
title: "TypeScript：Union被判為Intersection"
date: 2022-08-17T21:43:18+08:00
# weight: 1
# aliases: ["/Unexpected_intersection_type"]
tags: ["TypeScript"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "在函式參數使用Union Type時意外的變成Intersection Type"
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

這篇文章的撰寫緣由是我在React中某一元件要傳入兩種不同的物件陣列：

```typescript
interface UserInfo {
  generalInfo: GeneralInfoTemplate;
  educationExps: Array<EducationExpInfoTemplate>;
  practicalExps: Array<PracticalExpInfoTemplate>;
}

type UserInfoKeys = Exclude<keyof UserInfo, "generalInfo">;

interface Props {
  block: UserInfoKeys;
  blockKey: string;
  setInputs: React.Dispatch<React.SetStateAction<UserInfo[UserInfoKeys]>>;
  setAppUserData: React.Dispatch<React.SetStateAction<UserInfo>>;
}
```

簡單來說，我想把`const [state, setState] = React.useState()`這類的`setState`用props傳給子元件，然後這個`setState`是設置UserInfo物件中的educationExps「或」praticalExps用的，結果當我使用`setInputs`把這兩種之一的值傳進時報錯了。

這個錯誤，也是簡單來說，他說我educationExps沒有praticalExps的屬性，praticalExps沒有educationExps屬性...(怎麼聯集變交互比對了？)

我把這個狀況另外寫個測試重現出來如下：

[CodeSandbox連結(問題)](https://codesandbox.io/s/react-setstate-problem-with-typescript-4sjj9v)

```typescript
type Foo = { one: number; two: number };
type Bar = { three: number; four: number };

type Foobar = Foo | Bar;
interface Props {
  value: Foobar;
  setValue: React.Dispatch<React.SetStateAction<Foobar>>;
}
/** This will be used in App component below */
function Beep(props: Props) {
  const boop = Object.assign({}, props.value);

  return (
    <>
      <div>{"one" in boop ? boop.two : boop.four}</div>
    </>
  );
}

export default function App() {
  const [foo, setFoo] = React.useState({ one: 1, two: 2 });
  const [bar, setBar] = React.useState({ three: 3, four: 4 });
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      /** setValue will have an error */
      <Beep value={foo} setValue={setFoo} />
      <Beep value={bar} setValue={setBar} />
    </div>
  );
}
```

- 在App的return中，Beep這個元件的屬性`setValue`會報錯。

## 原因

參考Stackoverflow上的[這則回答](https://stackoverflow.com/a/69644729/18972098)；先是TypeScript(以下簡稱TS)官網在(舊頁面的)[Type inference in conditional types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-inference-in-conditional-types)裡提到：

> "Likewise, multiple candidates for the same type variable in contra-variant positions causes an intersection type to be inferred:"

然後回答者給出的整理：

> It means, if you want to call such function, arguments of all functions in a union will merge (intersect), that why `setValue` expects `React.SetStateAction<Animal> & React.SetStateAction<Cat>`.

當在函式的引數中使用聯集(Union)會造成TS判斷為交集。

知道這點後，來試著修正該回覆中舉例會報錯的例子：

## 除錯

1. 一種做法是用Generic type然後用Ternary operator判斷該值是否繼承自`unknown`，是的話要給攤開後`foo`和`bar`的形式；如果給的是`foo | bar`還是會報錯：
   
   ([TS Playground](https://www.typescriptlang.org/play?#code/CYUwxgNghgTiAEYD2A7AzgF3gMyUgXPABQBuUEhA3vClALYiGYwCWKA5vAL4CU8AvAD54JJC2ABuALAAoUJFgJk6LACNYhUuSrwo7RjQCudVSBjc+QkWMmzZGAJ4AHBABUkAURYYAFmYA8rsL88K7wIAAeGCAowGjwhigA1ihIAO4o8AD8xGQU8NS0DEwYrBzc8AA+Bbr6hCjGpua8AsKi4vD1ICRm0jKy8tBwiKiYCSgsqITuXr4Bji5I2Dh4VfALIEvw6jCCfbKJkyhEuEg8BxOoRDs8QA))

```typescript
declare const foo: (val: { name: string }) => void;
declare const bar: (val: { age: number }) => void;

type ToEither<T> = T extends unknown ? (val: { name: string } | { age: number }) => void : never;

declare const union: ToEither<typeof foo | typeof bar>;

union(foo)
union(bar)
```

2. 或許還不夠簡潔，於是我們可以把`ToEither`的結構改為`foo`和`bar`的函式形式，然後把`foo`、`bar`提取出來改成參數：
   
   ([TS Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAKg9gUQJbABYQE4B4YD4oC8UAFAG4CGANgFywCUh+pcSAJgNwCwAUD6xAGNK5DNAFwAdgGdgUAGZw4tAN4TyAWwi0ZGJBIDmAXz6DhoqOOmyARiJXl9WqBICu665mPd+QkWMkyUC4SSJK08MhomFigkHBy8opQAD5QsRDxULYYuFy83JaBLlKYtOmZCnCEUKoaTgBEAFJwqBL1hnmFsupw1kiUTuUJ2dXKDk4AzAAMHTw8XRbkwACSEgpl4BkJwaES1WRUDAT4hXADAHSUcPoHlHR580urCsTFmPePK2twxD19A-cgA))

```typescript
type ToEither<T> = (val: T) => void;

declare const foo: {name: string}
declare const bar: {phone: number}
declare const union: ToEither<typeof foo | typeof bar>;
```

當然實務上不見得抽得出來，這時第一種作法就派得上用場了。

至於一開始提到在React的錯誤，用剛才學到的辦法就可以除錯：

[CodeSandbox連結(解答)](https://codesandbox.io/s/react-setstate-problem-with-typescript-ans-81io62?file=/src/App.tsx)

```typescript
interface Props<T> {
  value: Foobar;
  setValue: React.Dispatch<React.SetStateAction<T>>;
}
function Beep<T extends Foobar>(props: Props<T>) {
  const boop = Object.assign({}, props.value);

  return (
    <>
      <div>{"one" in boop ? boop.two : boop.four}</div>
    </>
  );
}
```

文章就到這邊，謝謝閱讀😄
