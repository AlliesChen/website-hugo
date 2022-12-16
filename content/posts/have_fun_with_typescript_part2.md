---
title: "有趣的 TypeScript 型別用法與機制 Part 2"
date: 2022-12-16T15:01:10+08:00
# weight: 1
# aliases: ["/Have_fun_with_typescript_part2"]
tags: ["first"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Dive into TypeScript's mechanism"
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

繼上一篇 -- [有趣的 TypeScript 型別用法與機制](https://allieschen.github.io/posts/have_fun_with_typescript/)，這次閱讀到 [Gabriel Vergnaud](https://twitter.com/GabrielVergnaud) 的 [Type-Level TypeScript](https://type-level-typescript.com/) 這系列文章的第 1 到 4 章，屬於免費閱讀的章節，讓我重新認識了 TypeScript (以下簡稱 TS) 的型別系統；該系列文章中有提供即時的程式碼編輯區塊，每一章最後都有小測驗，閱讀與學習的體驗都很棒，非常值得一看。

這篇文章的內容是我個人針對這系列做的一個學習筆記，在寫這篇文章時也意外發現其實 Gabriel 提到的內容，TS 官網上都有條目寫到；過去只是讀過，從沒理解過😅。

## Levels of Language

貫穿該系列文章的第一個重點；language of values (value-level) 和 language of types (type-level)，JavaScript (以下簡稱 JS) 因為(還)沒有型別的語法所以 100% 是 value-level 語言。

> 還沒有，但正在提案中的型別註釋-- [tc39/proposal-type-annotations: ECMAScript proposal for type syntax that is erased - Stage 1](https://github.com/tc39/proposal-type-annotations)

如果對 TS 實作不陌生，有個錯誤就是：`'Somthing' only refers to a type, but is being used as a value here`-- [TS Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAYg9nKBeKBnYAnAlgOwOYDcAUEQMZw7pQBGAhhsrAgUA)；正是這個 value 和 type 的概念。於是乎，TS 不只是給 JS 寄住的外殼，而是用來撰寫型別，type-level 程式語言。

## Generics-- 泛型

上一篇提過 JS/TS 內建的型別可以透過給 type parameter 進 `<type>` 來產生型別，像是-- [TS Playground](https://www.typescriptlang.org/play?ssl=2&ssc=49&pln=1&pc=1#code/C4TwDgpgBA8gRgKwCrmgXigJQgYwPYBOAJgDwDOwBAlgHYDmANFDQK4C2cEBAfANwBQ+GhSicAblwCGdCDGJcoGGhADuUALKSw5SrUbN2nHgAoAlLyA)

```typescript
type ObjType = Record<string, number>;
const beverageOrder = new Map<string, number>();
```

過去我一直以為泛型的應用一定要給 type parameter 做型別宣告，像是這樣-- [TS Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAMg9gcwCrggHgIIBooCEB8UAvFABQAKAXFNlAMIBG1uAlMYbgNwBQ3A9HxpQAFgEsEwiACcocKQBNpUAGYBXAHYBjYKLjr+ggDaJZq4FGCSLqAM6zlUAIYqN23eoDkd0erBnSYI5SjgC2EMDSbI7q8lCiwHZwZn7A3GpaOnpQxsioAUGh1BoA1upwAO7qOJpMZABujoZF6qUV6mxEhCVllWwA3txQQ1CaejbmUhA2qobmJDX5wSEsPMMjY3CGEAB0OaQABgCSvmZQKJDUACR9oJBwDoFLAL4AOuoA8smn59Ak17cQe5QSbTWZPfYsQbDSbAVRSdTAqYzYA8J68UbqcaImwARmI2UQP1IUKGOIATABmAAsWBJ9UaHUIACkAMrvABy23GUh8CFEyhApAahkhUTs8FykDQ6lUIQY0hw3N5+B43AxWJBZPxOSJdL6onk1HJ1Jw6lCEGoACJlHA4JanrS1kKGewoO8GAArCDabbFCAgGzOwxOOzur3aFjbEKOMCkeIQEKu+MhbbAOAAVTAkCkdEcNggpBYopDsEJqDQcE93uAOAwUmCIDQSvUCHwKvRGy2u0QpEtIJxlpw-ZWUAEUEtxqplrVnZ2ez7UzJg+xZJHY4A2pbDgARZeW9kYACyAFFLQBdIA)：

```typescript
type LogType<A, B> = (P: A, Cb: B) => B;

// A higher order function
// log out the types of a function's input(parameter) and its output
function logType(param: unknown, cb: (val: unknown) => unknown) {
    const result = cb(param);
    console.log(`Input Type: ${typeof param}\nOutput Type = ${typeof result}`)
    return result;
}

const res1 = logType(
    1234,
    (val) => JSON.stringify(val)
) as LogType<number, string>;

const res2 = logType(
    {id: 1234, name: "foo"},
    (val) => Object.keys(val as Object).map(item => item.toUpperCase())
) as LogType<object, Array<string>>;

console.log("res1", res1); // "1234"
console.log("res2", res2); // ["ID", "NAME"]
```

又臭又長，不但需要先訂好輸入與輸出的 type parameters，還要靠 `as` 把型別給上去，甚至在 `res2` 的 `Object.keys()` 中也需要給 `val` 參數用 `as` 加定義，不僅難以複用，還要寫太多程式碼了。

但重新認識泛型後，才知道可以這樣應用-- [TS Playground](https://www.typescriptlang.org/play?#code/GYVwdgxgLglg9mABAGzgcwCoE8AOBTAHgEkwcQoAaRAeXLKgD4AKHAQwCdWBbALkRPpUIAIz5MAbq2R8B5AJSIAvAxp15fWlHqIA3gChEhxBAQBnKInZ5TIZBcXHhLDtzkBuA0ZNhTcZHgA6VDQmAANZC2x8PgASHShcPDhgRDZOLgBfAB0wTW0ovCVEOIT8ZMtrWygM0LlPQysoEHYkKxs7Dwy9PW9zCtMARiLggqYBgCYAZgAWKgkpBWVEACkAZWoAOQDzdhgwNBhgLHnkOXcesws28eH0UZ0YABM+CZmqMG48PgAiYDg4b4ZOaSU5KFTUYQAKzw0ACAGs8FhTCc5AEuKwcEwYFA8FwwYhsbiAlA4ABVHD4dgAYVYpjwTDO5wuPj8gWCTG+bQG3yoXPOvVZQXQHOuPP643cQA)：

```typescript
// A higher order function
// log out the types of a function's input(parameter) and its output
function logType<Input, Output>(param: Input, cb: (val: Input) => Output): Output {
    const result = cb(param);
    console.log(`Input Type: ${typeof param}\nOutput Type = ${typeof result}`)
    return result;
}

const res1 = logType(
    1234,
    (val) => JSON.stringify(val)
);

const res2 = logType(
    {id: 1234, name: "foo"},
    (val) => Object.keys(val).map(item => item.toUpperCase())
);

console.log("res1", res1); // "1234"
console.log("res2", res2); // ["ID", "NAME"]
```

可以看到 `logType` 使用了泛型宣告 `Input` 和 `Output`，但在指派給 `res1` 使用時卻不必給 type parameters，而是反過來，由 TS 從給進去的參數推斷 `Input` 和 `Output` 該有的型別是什麼。

## Data Structures

在原系列文章的第二章-- [Types are just data - Data Structures](https://type-level-typescript.com/types-are-just-data#data-structures) 該節中提到：

> In our type-level world, we have four built-in data structures at our disposal:

四個 type-level 的資料結構為：

```typescript
type ObjectType: {key1: number, key2: string};
// 有限的鍵(key)數配對不同型別的值(value)
type RecordType: {[key: string]: number};
// 類似 object type，但為不限數量的鍵配對特定型別的值
type TupleType: [string, number];
// 有限長度的資料集，個別資料的型別可能不同
type ArrayType: number[];
// 未知長度的資料集，但與 record type 相像，所有資料有特定的型別
```

這四個資料結構分別在該系列文章的，

- 第三章 [Objects & Records](https://type-level-typescript.com/objects-and-records)
- 第四章 [Arrays & Tuples](https://type-level-typescript.com/arrays-and-tuples)

深入的討論四種型別資料結構的特性，如何 merge 及 extract 當中的屬性...等。

## Types are Sets

在原系列文章的第二章-- [Types are just data - Types are Sets](https://type-level-typescript.com/types-are-just-data#types-are-sets) 提到，

> An interesting feature of TypeScript is that a value can belong to more than one type.

這是第二個貫穿系列文章的重點-- **assignability**；

下面這個錯誤訊息在 TypeScript 常見到，

> "Type A is not assignable to Type B"

放個方向想，當"A is assignable to B"，A 即是 B 的 subset，B 的型別範圍涵蓋 A 的型別範圍，所以可以把 A 放進 B 裡，Gabriel (該文章作者) 稱之為 assignability。

## Objects and Records

文章第三章中提到，關於物件型別，在 TS 中跟 JS 不同的地方：

- 在 type-level，要讀物件的屬性不能用 dot (`obj.prop`) 只能用 square brackets notation: `obj["prop"]`
    > `namespace` 中 `export` 出來的物件倒是可以用 dot 讀取-- [TS Playground](https://www.typescriptlang.org/play?#code/CYUwxgNghgTiAEA7KBbEBnADlMCBmA9gfAN4CwAUPNfCAB6YEwAu8zAnpggEazwC88ABQBKAQD546ZjACWiAOYBuSgF9KlMAUTT4ACxAQIBAFzxCBAHS8YA4WP6SARAAlDx+AHUmEYE5UUmtroBBAglsYKQgZGBKIiQA)
- Value-level (JS) 的 Logical AND (`&&`) / OR (`||`) operator 和 type-level (TS) 中 [union](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types) (`|`) / [intersection](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types) (`&`) 運作機制是不同的-- [TS Playground](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgMIHsA26rIN4CwAUMqclBACYBcyIArgLYBG0A3MWcgOYUQi0AzmCihuHEmWaZ6EWgxbtiAX2LFQkWIhQZsuQpNJxMABwAWceU1ZQJqosQToQwnlkq6ctT7gC8+TjIKGmQAJgBWcIAaQNJeCH5aACJQgEZwpJjDZGlZWgAGLK5jc0tkVKKyAHoq5AABMEEAWggADxMIBDAWqCgcWOQYdHRk5jgoJJU1IicXMGRIV39udx8AbSTgpOQAHx3kJPj+JIBdR2dBLAgAOmxuAApFsABKZBqwyOQm5AAVMxQAG7GWTIdAwA5bZCgNyYDxYfoOIhgACeHQWEGEP1RKH8602VG2+0OfBApzetWEohA3F2dGs0CAA)
- JS 中物件屬性的 merging 會透過 spread operator (`...`) 或物件方法，如 `Object.assign()` 達成，在 type-level 中則是用 `&`、`extends`，或 `implements` 等達成，可以參考 stackoverflow 上的[這則回答](https://stackoverflow.com/a/52682220/18972098)。

> 題外話：Gabriel 有提到由於在 `type` 使用 `&` 來 merge 屬性 TS 會持續遍歷過當中全部的型別來產生最終的結果，如果不是需要泛型，`interface` 會是效能上比較好的選擇，詳細的說明可以參考[這篇文章的章節](https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections)。

## Arrays and Tuples

文章第四章中提到，關於陣列型別的應用和特色：

- 想要取到陣列中的值的「型別」可以用 `arr[number]`

- 使用 `keyof` 可能不是理想的選擇，試想在 JS 中下面這段程式碼會印出什麼？在 type-level 也會拿到這些內建的屬性鍵：

    ```javascript
    const arr = ['foo', 'bar', 'map']
    console.log(arr['map'])
    ```
