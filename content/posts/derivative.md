---
title: "Derivative"
date: 2022-10-22T00:53:05+08:00
# weight: 1
# aliases: ["/Derivative"]
tags: ["math"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Learning notes for MIT 18.01SC Session 1 and Session 2"
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

這篇文章是我學習 MIT 18.01SC [Session 1](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/pages/1.-differentiation/part-a-definition-and-basic-rules/session-1-introduction-to-derivatives/) 及 [Session 2](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/pages/1.-differentiation/part-a-definition-and-basic-rules/session-2-examples-of-derivatives/) 做的學習筆記，希望能夠幫助閱眾更容易地吸收課程中的內容。

這篇主要會輔助說明課程中導數的用途(查資料歸納出來的)，並說明 EXAMPLE 2 裡提到要用二項式定理細部來看是怎麼來的。 EXAMPLE 1 因為課程講義和課程影片中就蠻清楚了，就不多做筆記了。

## 這篇文章會涉及到

- 導數(derivative)
- 二項式定理(binomial theorem)

## 基礎知識需求

- [切線(tangent line)和割線(secant line)](https://www.mathsisfun.com/geometry/tangent-secant-lines.html) -- 參考：math is fun
- [斜率(slope)](https://www.mathsisfun.com/geometry/slope.html) -- 參考：math is fun
- [線性方程式(linear equation)](https://www.mathsisfun.com/algebra/linear-equations.html) -- 參考：math is fun
- [組合(combination)與排列(permutation)](https://www.mathsisfun.com/combinatorics/combinations-permutations.html) -- 參考：math is fun

## 前言

關於導數 (derivative) 的概念 18.01SC 講的簡明扼要，但又舉了很多深度的例子啟發思考。切得更細，以及更多小練習題，可以看 Khan Academy - Differential Calculus 下的 [Unit: Derivatives: definition and basic rules](https://www.khanacademy.org/math/differential-calculus/dc-diff-intro)，從 Average vs. instantaneous rate of change 這段到 Derivative definition 該段的 Formal definition of the derivative as a limit。

## TL;DR

首先你需要先有一個非線性方程式才能推導它的導數。

導數公式：
$$
m = \underbrace{f'(x_0)}_{\text{derivative of } f \text{ at } x_0} = \lim_{\Delta x \to 0} \frac{\Delta f}{\Delta x} = \lim_{\Delta x \to 0} \underbrace{\frac{f(x_0 + \Delta x) - f(x_0)}{\Delta x}}_{\text{difference quotient}}
$$
(來源：[18.01SC Session 1 Clip 5 Notes](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/resources/clip-5-main-formula/))

這個 m 就是線性方程式中 $$y = m(x + b)$$ 裡的 m (斜率)；導數可以用來帶出非線性函式中的特定點的切線斜率。

### Notations

導數有幾種數學的符號表示法，這個在 [Session 2 Clip 3](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/resources/clip-3-notation-for-derivatives/) 的 Notes 裡有整理出來。這邊就簡單列舉，因為在這篇文章後面 -- 課程中 \(y = x^n\) 的例子會這邊提到的別名寫法。

- \(Delta y = \Delta f = f(x) - f(x_0) = f(x_0 + \Delta x) - f(x_0)\)

- Difference quotient: 
    $$
    \frac{\Delta y}{\Delta x} = \frac{\Delta f}{\Delta x}
    $$
    

- 假設 \(\Delta x \to 0\)，difference quotient 可以寫成：
  - \(\frac{\Delta y}{\Delta x} \to \frac{dy}{dx}\)
  - \(\frac{df}{dx}  \frac{d}{dx}f\)
  - Leibniz' notation: \(\frac{d}{dx}y\)
  - Newton's notation: \(\frac{\Delta f}{\Delta x} \to f'(x_0)\)
  - 其他的註記方式： \(f'\) 和 \(D f\).

## 從線性到非線性

舉例來說，汽車的起步加速能力可以看從時速0到100公里需要多少秒數。

想像y軸是速度，x軸是經過的秒數，第1秒10km/h、第2秒20km/h...線性的穩定增加，這樣的條件出來的函式圖應該是一條直線(如圖1)：

![linear equation](https://i.imgur.com/ZPQiczi.png)
(圖1：線性增加)

要求這條線的斜率只要找線上任意兩點
$$
(x_0, y_0), (x, y)
$$
然後計算
$$
m = \frac{y - y_0}{x - x_0} = \frac{\Delta y}{\Delta x}
$$
m 就會是斜率，比如
$$
(x_0, y_0) = (1, 10), \quad (x, y) = (2, 20)
$$
代入公式得出
$$
m = \frac{20 - 10}{2 - 1} = 10
$$


因此這條線的線性方程式就會是
$$
y = f(x) = 10x
$$


---

但通常時速不會是每秒穩定增加的，而是非線性的(如圖2)，可以想像是起步比較慢，但後續會增加得愈來愈快：

![nonlinear equation](https://i.imgur.com/8naOhLS.png)
(圖2：非線性增加)

可以發現上面求斜率的公式在這條(曲)線上，會有困難，切線延伸出去會變成割線，這時要找某個點的斜率就需要使用導數公式。導數公式的概念是：當割線無限接近我們要找的那的點
$$
(x_0, y_0)
$$
就會是切線，也就拿的到斜率，而這個無限接近指的便是
$$
\lim_{\Delta x \to 0}
$$
微分的概念。

> [18.01SC Session 1](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/pages/1.-differentiation/part-a-definition-and-basic-rules/session-1-introduction-to-derivatives/) 提供了視覺化切線與割線、非線性函式，與導數的[小工具](https://ocw.mit.edu/ans7870/18/18.01SC/f10/mathlets/secantApproximation.html)(Mathlet)

## 二項式定理

[Session 2 Clip 4](https://ocw.mit.edu/courses/18-01sc-single-variable-calculus-fall-2010/resources/clip-4-example-2-y-xn/)的例子 -- 非線性函式：
$$
y = x^n
$$
n 為正整數(非零自然數)，透過導數公式展開會變成：
$$
f'(x^n) = \frac{\Delta y}{\Delta x} = \frac{f(x_0 + \Delta x) - f(x_0)}{\Delta x} = \frac{(x_0 + \Delta x)^n - x_0^n}{\Delta x}
$$
然後要讓 \(\Delta x\) 不出現在分母，才能假設它是 0，不然除以 0 會是未定義。首先要看到式子中的這個部份
$$
(x + \Delta x)^n
$$
可以用 [二項式定理(binomial theorem)](https://www.mathsisfun.com/algebra/binomial-theorem.html)：

> 參考 math is fun，這個網站寫了循序漸進，淺白易懂的解釋這個公式是合理的，而且還會看到帕斯卡三角形(Pascal triangle)的應用，終於知道這個常見於題目中的數陣可以做什麼了。

$$
(a+b)^n = \sum_{k=0}^{n} \binom{n}{k}a^{n-k}b^k
$$

我們可以把 a 和 b 換成 x 和 Delta x，從課程中的筆記可以看到單純展開會是這樣：

$$
(x + \Delta x)^n = (x + \Delta x)(x + \Delta x)...(x + \Delta x) \quad \text{n times}
$$


但是，參考維基百科 [binomial theorem - geometrical explanation](https://en.wikipedia.org/wiki/Binomial_theorem#Geometric_explanation) 這項，再把這個公式乘開來一些：

$$
(x + \Delta x)^n = x^n + nx^{n-1}\Delta x + \binom{n}{2} x^{n-2} (\Delta x)^2 + ...
$$

> 維基百科還有提供一張很棒且有趣的幾何圖幫助了解

這樣就接近到課程中提到的 *junk* 是怎麼來的了，因為
$$
\binom{n}{2} x^{n-2} (\Delta x)^2 + ...
$$
會假設 \((\Delta x)^2\) 極度接近0，可以被忽略；於是課程中寫成 \(O((\Delta x)^2)\)，指的就是餘下後面會乘到 \((\Delta x)^2\) ， \((\Delta x)^3\) ...到 \((\Delta x)^n\) 的部份，所以是：
$$
x^n + n(\Delta x)x^{n-1} + O((\Delta x)^2)
$$


所以關於 \(y = f(x) = x^n\) 的導數會是:

$$
\frac{\Delta y}{\Delta x} = \frac{(x + \Delta x)^n - x^n}{\Delta x} = \frac{(x^n + n(\Delta x)(x^{n-1}) + O(\Delta x)^2) - x^n}{\Delta x}
$$

- 看向第三個公式，分子的部分頭跟尾的 \(x^n - x^n\) 相消，再把 \(\Delta x\) 從 \(n(\Delta x)(x^{n-1}) + O(\Delta x)^2)\) 提取出來並跟分母抵銷，就會變成

$$
nx^{n-1} + O(\Delta x)
$$



再來，這個導數要解，就要回到前面提的，假設 \(Delta x\) 無限接近 0
$$
\lim_{\Delta x \to 0}
$$
於是
$$
\lim_{\Delta x \to 0} \frac{\Delta y}{\Delta x} = nx^{n-1}
$$
所以:

$$
\frac{d}{dx}x^n = nx^{n-1}
$$
這個結果也會是個公式，課程中提及叫：power rule，用於求多項式 (polynomials) 的導數：

$$
\frac{d}{dx}(x^2 + 3x^{10}) = \frac{d}{dx}(2x^{2-1} + 3 \times 10x^{10-1}) = 2x + 30x^9
$$

> 比較淺白的例子及應用練習可以看上面提到的 Khan Academy 同一個單元的 [Power rule](https://www.khanacademy.org/math/differential-calculus/dc-diff-intro/dc-power-rule/v/power-rule) 段落。
