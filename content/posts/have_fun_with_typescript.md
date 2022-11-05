---
title: "有趣的 TypeScript 型別用法與機制"
date: 2022-11-03T17:39:03+08:00
# weight: 1
# aliases: ["/Funs_with_typescript"]
tags: ["TypeScript"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Dive into TypeScript's usage and built-in types"
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

這篇文章需要閱讀者對於 TypeScript (以下簡稱 TS) 及 JavaScript (以下簡稱 JS) 的語法有基礎的認知，入門可以參考 TS 官網的 [Get Started](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html) 並選擇適合你的章節開始閱讀。

這篇文章中 Generics、Error Type in Catch 和 Utility Types 是 Matt Pocock 超讚的 TS 教學 - [Total TypeScript](https://www.totaltypescript.com/tutorials) ，在 [Beginner's TypeScript](https://www.totaltypescript.com/tutorials/beginners-typescript) 課程中有用到的。這些範例為了方便我有精簡修改程式碼來說明，完整程式邏輯可以看 Matt 的原始教學，有提供可以互動的範例唷 (透過像 Jest 測試的方式)。

## Generics

可以給 constructor function 加上 `<type parameter>` 來定義可加入的值 (原教學中用 `Set` 做例子)，這邊給進 `new Map()` 的是要求 `<key, value>` 的型別：[TS Playground](https://www.typescriptlang.org/play?#code/MYewdgzgLgBARgUwG4IE4EMDmCDyqAmaMAvDGAgO4wCy6ADgDzSoCWYmANGQK4C2iqAHwAKAJQBuAFCTEKDNjyFUAOggIowgOSgAZjoQJNXAIwTpstFlwE0q9cONdtIPQc0SgA)

```typescript
const beverageOrder = new Map<string, number>();

beverageOrder.set('coffee', 1);
// @ts-expect-error
beverageOrder.set(1, 'coffee');
```

## Error Type In Catch

Try & catch 中，catch 的 `e` 參數是什麼型別呢？[TS Playground](https://www.typescriptlang.org/play?ssl=15&ssc=2&pln=1&pc=1#code/MYewdgzgLgBATgUwgZSgQygVwjAvDAbwFgAoGcmAIgCYAGWygLipAGs0BPSgGlIqoAstAUypgQsAGYhMYACaVSAX1KlJs4FACW4GBARQASkkwAbKAApQchM2hwtYAOYBKQnwpQ4Hd2X4UtSRgrEBs8XHxKIRE3Yj9-fygACzgQAHcYMAQMgFE4VLgLRBR0LAgAbWiAXRcAbg8ElXiYJRhgDGAkiwR82Ib-RCw4MBgeuAA6AFskCDQnBHrmpv5BzGH4JFQMbHK6WirFpSA)

```typescript
const resStatus = {
    "200": "okay",
    "404": "not found"
}

function setResult(code: string) {
    try {
        if (code === "404") {
            throw new Error(resStatus[404]);
        }
    } catch(err) {
          // error
        return err.message;
    }
    return resStatus[200];
}
```

上面這段程式碼會在 `err` 報錯：Object is of type 'unknown'.

那試著給 err 指定類型：

```typescript
// error
catch(err: Error) {
    return err.message
}
```

這次報錯在 `Error` 型別身上：Catch clause variable type annotation must be 'any' or 'unknown' if specified.

Okay，所以現在知道 `err` 的型別要給 'any' 或 'unknown'，前面已知 `unknown` 會報錯，所以選 `any`，解決，thank AnyScript!

其他可能的解法如：

```typescript
catch(err) {
    return (err as Error).message
}
```

或是

```typescript
catch(err) {
    if (err instanceof Error) {
        return err.message
    }
}
```

## Utility Types

全部的 utility types 可以在 TS 官網的 [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html) 文件看到。

### Promise

[TS Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgDIFcDWEDKmCeA7nADbZTIDeAUMsgNoIAWcUikUwAzmMAgFzIenEAHMAuoOGhR1AL7VqCAPYgeyGBDDMM2PEVLlkAXmRwu+EAmQAKAJQmAfFVrIVasMgAmcMHBNmxMCemtpMNgBETGBgAA5c-AD0iVzEscAAdF4QAG6JcOmJsRDKsSQQiQCMEXYA3K7u6sVQXKqCurgExGTQAXBBnj5+GQBWrSD29XRQWuhQIMjN4-Vy9UA) 這段 code 是在 `person` 這個變數做型別宣告，讓 TS 知道它是 `LukeSkywalker`，

```typescript
interface LukeSkywalker {
  [characteristic: string]: string
}

const fetchLukeSkywalker = async () => {
  const data = await fetch("https://swapi.dev/api/people/1");
  const person: LukeSkywalker = await data.json();
  return person;
};
```

滑鼠移到 `fetchLukeSkywalker` 就會顯示：`const fetchLukeSkywalker: () => Promise<LukeSkywalker>`；就也可以寫成，但指去 `person` 就會顯示它是 `any`：

```typescript
const fetchLukeSkywalker = async (): Promise<LukeSkywalker> => {
  const data = await fetch("https://swapi.dev/api/people/1");
  const person = await data.json();
  return person;
};
```

### Record

在 Generics 提到的 `new Map<key, value>`，如果想要對也是 key-value 配對的 object 使用，可以用 `Record<key, value>`：

```typescript
const beverageOrder: Record<string, number> = {};

beverageOrder['coffee'] = 1;
// @ts-expect-error;
beverageOrder[1] = 'coffee';
```

- 這個 utility 的應用還可以參考官網例子，更靈活：[TypeScript Doc](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeys-type)

### Pick

有時，我們只想要 `interface` 中特定幾個 properties 來產生一個新 `type`，就可以用 `Pick<Type, Keys>`：[TS Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgLITgG2QbwFDKHIBEA9gLYSYRiTEBcyIAruQEbQA0BRxCpMGBAgMmrDlG5ESbOAHMsolu2h4AvnjxgAngAcUAMVKkAJsgC8yAArAEAawA86LJxIUqNOsgA+M+YoA+TWByXVIoMFxkOABnGOgwVwBRAEdmLBjkNWQYKAoSMBi4IWIAbjxY+IiHVPTMGIcjU1ccMkpqWhFGZQlXYlkFTCVxaDUAoMqEmrSMxuMTFraPTuGVKDGJuKna2aaF3CWOukYYsChQOT6BxW6R9fGKreqd+rnmg-5BYVXev0Gf0bjIA)

```typescript
interface Meal {
    "omelette": number,
    "coffee": number,
    "bagal": number
}

type Food = Pick<Meal, "omelette" | "bagal">
```

### Omit

Pick 中提到的狀況，如果換個方向，也就是我們不想要 `interface` 中特定的元素(們)，來產生一個新的 `type`，就可以用 `Omit<Type, Keys>`：[TS Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgLITgG2QbwFDKHIBEA9gLYSYRiTEBcyIAruQEbQA0BRxCpMGBAgMmrDlG5ESbOAHMsolu2h4AvnjxgAngAcUAMVKkAJsgC8yAPLlgYADzosnEv0HDiAPk3Byu0lBguMhwAM6h0GAuAKIAjsxYochqyDBQFCRgoXBCxADceGERgfZxCZih9kamLjhklNS0IozKEi7EsgqYSuLQap7eRZGl8YlVxia19VQ0dC29UP2D4cNlY9WTuNONc8ihYFCgcu2divMqiwOFKyVrFeM1W25CzWIXJ-JnbxJLQA)

```typescript
interface Meal {
    "omelette": number,
    "coffee": number,
    "bagal": number
}

type Food = Omit<Meal, "coffee">
```

## Inferring Within Conditional Types

標題就是官網介紹 `infer` 這個 keyword 段落的的標題 - [Inferring Within Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#inferring-within-conditional-types)；就我淺白的理解 `infer` 是用來伸手到 function 裡去指(infer)，拿取我們想要的元素用的，要用它有幾個條件：

- 搭配 generics

- 搭配 `extends`

- 搭配 [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator#:~:text=The%20conditional%20(ternary)%20operator%20is,if%20the%20condition%20is%20falsy.)

看例子應該比較好懂，LogRocket Blog 的 [Understanding infer in TypeScript](https://blog.logrocket.com/understanding-infer-typescript/) 裡講解了整個情境的前因後果，順便帶到了 [utility types](https://www.typescriptlang.org/docs/handbook/utility-types.html) 中的 Extract、Exclude，和 ReturnType 的實踐概念就是跟 `infer` 有關。

借用文章中開頭提的例子來看：[TS Playground](https://www.typescriptlang.org/play?#code/GYVwdgxgLglg9mABAEwKYGcICcYCNUAKqW6CAFAA7GlgBciA3gLABQiiYAhgLar3pQcYAOYBuVu07C+HEN3xZxbRAAs4uXDAz0A2gKHCANIn0wRAXSUBfAJSMJiLKiggsSAAYASBlRIIAdFy8Vogw6IjevjT+UqghAJ6onCSIcAA2yIicYJlpcABuqBE+1AFqGlro-gBWcGZkAERZOewNNlb+7tasrBAIAllpqAAeiAC89spBMgDkAIJDwzOGDrH0AEwADCvK5ZraiDozAO6caQDWZsLLiDN9cJciM+asVj0saJg4+ER+YGRnEY2IA)

```typescript
function describePerson(person: {
  name: string;
  age: number;
  hobbies: [string, string];
}) {
  return `${person.name} is ${person.age} years old and love ${person.hobbies.join(" and  ")}.`;
}

const alex = {
  name: 'Alex',
  age: 20,
  hobbies: ['walking', 'cooking']
}
// error: Type 'string[]' is not assignable to type '[string, string]'.
describePerson(alex)
```

這例子有個假設，這個 function `describePerson`，它的參數 `person`，只會出現在這個函式中，沒有必要為了它獨立寫一個 type (當然要寫也可以)。這樣做就不會報錯：

```typescript
type Person = {
  name: string;
  age: number;
  hobbies: [string, string];
}

function describePerson(person: Person) {
  return `${person.name} is ${person.age} years old and love ${person.hobbies.join(" and  ")}.`;
}

const alex: Person = {
  name: 'Alex',
  age: 20,
  hobbies: ['walking', 'cooking']
}

describePerson(alex)
```

那麼使用 `infer` 的話：[TS Playground](https://www.typescriptlang.org/play?#code/GYVwdgxgLglg9mABAEwKYGcICcYCNUAKqW6CAFAA7GlgBciA3gLABQiiYAhgLar3pQcYAOYBuVu07C+HEN3xZxbRAAs4uXDAz0A2gKHCANIn0wRAXSUBfAJSMJiLKiggsSAAYASBlRIIAdFy8Vogw6IjevjT+UqghAJ6onCSIcAA2yIicYJlpcABuqBE+1AFqGlro-gBWcGZkAERZOewNNlb+7tasrFDxVIgEyTzOxAAq-agAPGMAfIgAvIhjiKgAHlCoOeGUpXShYMDEg3sTVHYL89nxiAD8J35gZ0X0YKiFir2TiAAiGNh4Qh7IZYEbcTZYZ6LQbDXgQ55TPpUODAFD-HD4IiPWY9FgQBACLJpdb0P6YDFAx4gsHw75LZjKIIyADkAEFiWtmYYHLF6AAmAAM3OU5U02kQOmZAHdOGkANZmYRcxDM-FwBUiZnmYVWXFocmArE0Miy9Y2IA)

1. `type ParameterType<T>` 用來提取 `describePerson` 的參數出來當成一個 type 指派給 `alex`，這就是我們的目標。

2. 等式的右邊，type parameter `T`  是之後要放 function type 的，也就是 `typeof describePerson`。

3. `extends` 後面是描繪一個函式的形狀，因為 function return 不是我們的目標，所以寫個 any，目標是函式的參數 `(person: infer PersonType)`，`infer` 就是為了指 person 的型別定義，這邊給它一個變數 `PersonType`。

4. 藉由 ternary operator，當 T 符合 extends 後面這個函式的形狀時，讓它回傳 `PersonType` 出來，這樣我們就能拿到函式的參數型別了。

```typescript
function describePerson(person: {
  name: string;
  age: number;
  hobbies: [string, string];
}) {
  return `${person.name} is ${person.age} years old and love ${person.hobbies.join(" and  ")}.`;
}

type ParameterType<T> = T extends (person: infer PersonType) => any ? PersonType : never;
type DescribePersonParamemterType = ParameterType<typeof describePerson>

const alex: DescribePersonParamemterType = {
  name: 'Alex',
  age: 20,
  hobbies: ['walking', 'cooking'],
}

describePerson(alex)
```

## Object Literal And Structural Type System

這個不是我發現的，而是社群中看到的討論，做個筆記紀錄：[TS Playground](https://www.typescriptlang.org/play?#code/GYVwdgxgLglg9mABAWzgJzDMBzAwgCwFMIBrACgCM1CBDE4GgZygC5EBvRMG5Qt5tFmwBfAJQcAsAChEiCAkZwANoQB0SuNkrU6DZqIDc04dOmhIsBFzgICxckvAR8bTt178ognABpEAB0EIPi4QZApCNEQxSRkUdEwcO1IyR0h8Q2NTKXkwZkR0ABNIxABeDi4eEIAiYEEoKBpG6r9AmGC2ACYAVgAGaOzUDCFk8jcqtlr6xubWoJCe-rEjKSHEvCIUosjM1YSRzbHKj0QpmAammhaA+a6+6MQmDncQgSE59pCwMIi0ZaA)

```typescript
function morningCheck(breakfast: { name: string}) {
  console.log(breakfast);
}

function noonCheck(lunch: { name: string, price: number }) {
  morningCheck(lunch);
}

const order = { name: "frittata", price: 250 }

// error - Object literal may only specify known properties
morningCheck({ name: "frittata", price: 250 });

morningCheck(order);
morningCheck({ name: "frittata", price: 250 } as {name: string, price: number});
```

把這個錯誤描述拿去 Google 可以看到 Stackoverflow [這則回答](https://stackoverflow.com/a/31816062/18972098)提到：

> As of TypeScript 1.6, properties in object literals that do not have a corresponding property in the type they're being assigned to are flagged as errors.

可以再對照一下 TypeScript 1.6 的[更新說明](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-6.html#stricter-object-literal-assignment-checks)。

那麼關於 `morningCheck(lunch)` 這個用法不會報錯，可以參考：

> The shape-matching only requires a subset of the object's fields to match.
> 
> -- [Structural Type System | TypeScript Documentation](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html#structural-type-system)

上面這段節錄自官網，也就是以變數型式當參數時，只要符合該參數的形狀 (必須元素)，多出來的屬性不會被視為錯誤。

社群中看到不錯的解釋脈絡是；如果用 object literal 的型式當參數，很可能就是針對這個函式而存在，就會嚴格檢查物件中的屬性，但如果以變數型式去傳參數，可能是其它地方也會用到，保留彈性的情況下，不做嚴格檢查，畢竟多出來沒用到的參數，理論上不會造成錯誤。有點像 像 Inferring Within Conditional Types 這段裡也有提到的概念。
