---
title: "Break_vues_reactivity"
date: 2023-02-28T16:50:44+08:00
# weight: 1
# aliases: ["/Break_vues_reactivity"]
tags: ["vue"]
author: "Yu-Pang"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://allieschen.github.io/"
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

This article won't do a deep dive into the implementation of Vue.js reactivity, I just want to show some examples that will break the reactivity when you are writing with the framework.

> Should you be interested in the mechanism of the reactivity under the Vue.js, here are some information I think that would help:
> 1. [Reactivity in depth | Vue.js](https://vuejs.org/guide/extras/reactivity-in-depth.html)
> 2. [Vue 3 Reactivity | Vue Mastery](https://www.vuemastery.com/courses/vue-3-reactivity/vue3-reactivity)
> 3. [Vue JS 3 Reactivity Fundamentals - Composition API by Marius Espejo](https://youtu.be/gew7a_Mdh5U)

## TL;DR

- Primitive value props cannot be modified from the child component.
- For a prop that is an object, modify its attributes from the child component is possible with both mutation and immutation functions, but the data structures may be different.
- `shallowRef` only updates when a referred source has reassigned.

## Two Way Binding

Let's start with a simple input element with a reset button, we can see what we have typed down below the input element.

- [Vue Playground](https://sfc.vuejs.org/#eNo9UbtuwzAM/BVWixOgsdDVVYy2U5cuGdpFi+PIiRK9IEouCsP/Xspus5G64x15mthrCPWYFWuYwD7qkABVyqGVTtvgY4IJohpghiF6CxVRK+mk673DBBbPsC/4pnpXxnj48tGcHqptoQzZ9Ul7RzhJfuB5s4VJOihT9diZrGi2qp6lm6UTfHUnX2qSssF0SVEHIC5P7VtU3Q0+aeSgOlIddfoRnICFoF3ICcad9Sdl9pKRgWQrdNJjKzB0rhU9oS1Bgi9VhbAuobEBsi+caVpOmmfBy+CicMwp0REvvdH9jcT/ryGHQykFXxnEFvy+OHtka34724X6it5Rwsv18g9AyZo1j/JGuZZesktKARvOcejLv1yx9vHMqapjdklbVSu0u2P036giCUtWJCjBmc2/O5GfWQ==)

It works! Not surprising, right? But in reality, we frequently separate elements into child components to make the function of each component clearer. So, let's move the input element and the reset button to `Comp.vue`. And it seems Vue is arguing about the prop are read-only.

- [Vue Playground](https://play.vuejs.org/#eNqFUk1v1DAQ/SvGl2ylkgjBaXEjKKoESEBVEFx8CckkddexLX+ERVH+OzMOGyoB7c2e92bee2PP/LVz5ZSA77kIrVcusgAxOaYbM1xIHoPktTRqdNZHNjMPPVtY7+3ICmwrNuiNHd3velnRhaYiLE1rTYhsDAO7oPZd8Ra0tuyb9bp7UpwRRVSrNirhJcLodBMBb4yJ22f1pYfmwL4mYDfQtFFNKv4UFQKZ0JLy9HS0HWh0jEKSs2rFOjXVIrjG1EjroEZQVPlUBDY1GkeqsGeoT5x5zjaXRVTUSL42L/ycn1I9sqs1r/PWBUzcQa8MXNNN5PF7FqJXBmXqXQ7fJ4OZrMHl4LgPYdidsZnc5xHluriieCnN8vCmlHEp3tvE1p9tIeF7ihF1XrVatQcknAQRv6GjqFbGX8ljwFC9Gsq7YA3Gz/Ykp80rDf6TI/+Yfr8aJ6zBN/7xPteiT3B+qre30B7+Ub8LR6pJfk2u/ASSb1hs/ABxha8+f4QjnjcQ0yaN7AdADGd1Io8r7TKZDm3f42W37/JPxqf5Eq6OEUw4hSKjxFwyX3L8A/QX/hf9j93n5Yvch0/Hl1+k+Sfq)

It's good to see Vue prohibit modifying props "directly", because it usually causes data flow of the states to become a mess.

How about to destructure the `props` that is an object type in JavaScript? Deeper to the `props.modelValue`'s value property. (I add styles to stand out the elements 😁)

- [Vue Playground](https://play.vuejs.org/#eNqNVMtu2zAQ/JWtLnaASGra9OIqRpsg6AN9BG6QXnShJVpmTJEEH44Nw//eJWnLclGn8cnkLmdnZ3e0ST4qlS0dTUZJYSrNlAVDrVPAiWiuysSaMhmXgrVKagsb0HQGW5hp2cIAnw260I1s1e4+y/3Bo2K4FJUUxkJrGrjyz4cbWBLu6AgGnynnEn5LzetXA9ie+eyZE5VlUmCqcS39bprhGWxKAR4hCy8R5yTG+1JsS1HksRdkjgdLW8WJpXgCKOYX42tNyQIeEGlCCVZbMrsucgyEhMp3skxbWVOOCmDZMoE8xmq2hIoTYzCgiCaNJmoeFAL8FUYRMb6f08gO5Myj1XTcUS/ycAZmRoAkffpm0+tsi+QRJ8c6seDUWYti7GtOrYBpk6ZKs5boNVi6smn6NGeWIskPFWfVAtM67ZDaJPwHK6GmM0e4LfIIigWKvKcNHo1d8yiTpxllB5iSatFo6USdVpJLPYJPhAkzlVqi3D5DkbpmokmZ4EzgVF5nb95p2oZo6CjrxNqDIv2GidRK5dO77APWlMtqcRSLSF6Cl2Ic+DxX4MAVe5W6ptjhhVqBkZzVR/epJjVzOLtLtdoFKqeNl0RJJizVPaK92ewJ7+T7xpq5vVkT0W+rN9WTuk9o5BNXPAwrOU/2bnuph62c0Jn5y8bRpUpLZdBfuCoo3J0/Fbif3goP0XCd84zVqCKibMfD4NyIsIvj/dWu0DCAZgeQM+zh2OjU9nwe3ndOHyC3/ziaCeVsz7Hh6c6UewP1rRGqRWfQE25AVa3BhmasyR6NFCht4FYm/uvAONU/lSePyqIkcVxlQvBT9PQ13Fnt6Pn+vprTavGP+0ez8ndlcudZ6SWS7mIWd5vaGL799QN3qRfETh3H7GeC2JzkznOMade4Rki7lxfYfglLgYO8N7crS4XZN+WJhlUL+Siqo37PTrV+oPs2u9yt6DbZ/gHvaSZl)
    - By clicking "Reset" button, you trigger the `restMsg` function in the `Comp.vue` to clear the `value`'s value to empty string.
    - By clicking "Resume to default" button, you trigger the `resumeMsg` function in the `App.vue` to reassign an new object to the `msg`.

Reassign an object to the `msg` breaks the `props`'s reactivity. As in JavaScript, every object points to a reference of memory, the `msg` is not pointing to the same ref of the `value` in the `Comp.vue`.

## Immutation and Mutation

For this part, let's play both mutable and immutable method on two array `ref`s to see how we can break the reactivity.

On `List.vue`, clicking "Add to ordered list" button will trigger a function to push the value from `msg.value`  into it. However, clicking "Add to unordered list" button should see a warning message popping out.

- [Vue Playground](https://play.vuejs.org/#eNqtV19v2zYQ/yqcXuwOsd103YvnGE3SoN2QrEWSdQ9RHmiJltnIpEBScQzD3313pChRspKsQAukMHn/fveHd6dddFoU48eSRdNophPFC0M0M2VBciqykzgyOo7mseDrQipDdkSxJdmTpZJrMgCxwR817Vyui4ownuAB1Qb0S65NTceDpxMSi0QKoK51Rk7QxHBHHmlesikZfGZ5Lsm/UuXpLwOyf+N5pUqZYqnV6mTu7t+ANkctxXP0WCxLkRguBUlyRtWVzoZvyA5hgPmxNev+B7E4iiOQ2cdiNnHRgVjAwbB1kVPD4ETIbHU8P1OMPpBvIHTNKGh/5GY7mwDBMiQYmsfRWqYsh5iCnTgiE0dL+SMAoVoDoaCKZooWKxtzAv9muqBifrtiLh5ELlFbyuaINVOMGS4yPZvYO8L1lABQFNntGnfIHhwAXROw5YzmEBVnoEI1rcI1QgogCaIHMWhx1qH1vK1Ye+4pWPeuupsPulysuRk5go+9p2I0ZpMgrnDUZpu7EKN7LkeELGjykClZinSUyFyqKflEudALqaQtJkIKmqYQlhEXORdQQ2/H735XbG2pNhLjZc6evL6Ua7C5neJdwJLRwnPAT1Ry3NVSp8szrqnKuBgZaflr7gbRIpfJQ4vmNC2MeFVHRVA8W5kOnD6XX7IeSkLcIHlTclw8ES1zngawbJRZWkfeso4UTXkJlfa+CONl2JMZjQrFAefWS1QJukTM51sqWjZ9+q7lluZnUKcH2nSZJEzrjrbPUrDtR7bpVfYJ3oQzEyraUCUgAh1FNxCXnJ0puTmUSKH9MdURuKJKypB3kR24fFieH2UKqjoOomTHvUPJKwaRXt8w2vUKpTs+HUpfsrUU5yu+XB5gbnvXYxhe8vZa6hBxUiotFfgruTBBaOz1lFTXXgDakH290VHkZ8ErI8a37kLJQkPvTdkSSvkrnmbQzLD3fHMzoR4O2iiMwJ7s58Om94fMQDtxGtutXzGAELb+WqLu/YPBq41fMz9IXP+2bQW6RdO+uShKE7b+jp2KMzazRWlMoMo/vU7UYW58SHKePCBL5QPouMafs4nT4bBNKnDtvgrp8KP3lXQQUs98I6/ZUnfHPr6KZ/Pl3A/mwpScKkW30NMxZfPq7bZGRz8LjIu+jNsyc1n3QBhMlwbIBZ48EDd6IFZTcnfvRRvJXYj0qI3qyG4lWEYuDEPrr7OLf3VJQTO5lV9QxBdVbAI91WZRlHo17CwaThlxDgwHNdiB3VcMFOGhoX/ahlqQ6xK+G4/HPRTrUgjg3ttBl2LTXpBedQnGJ0/Y8G0/Wqvjf6EVbOMqwGbVanrp8VFlOCj3L231bo7aYBeCX3556rzPTPG0fpud3cuuRA0NqO41NRdQLRUvjuuwBfvH2h42B083VFW/4qZs2gzz0xQUSl+ZBPG1XngL5QG0qsO3htmzPaUXWJP8DjB3OEcyMbCZ+mWyD53Mw1POoRUupXKLIeEifHcQfLe0wro6m+S8SdMk0NIssfjvp2XSj+J2Jv3tD2TSVnp/JuuqfyWXL+H8SWntQ/kjaS2bhMQGhtdhYjsfBb2ptaKTQFeY3HCA4al57s9/JowRs19MuivxcXvlxl7Q/QQgeFk1Y/w58oZwLyrXApbe46XCP797HmxOixyuQhX24wGW6+puw1OzmsIy/wQSkCZhupjm5NfnF7PNipt6KQt2LKNB25Jn4+9aCpjsrtFG+OHJc6a+FBhJGOwwSqvYRxS+qzd/2TujYCj4+2TFkoee++/6Ce/i6CsuHuoRdpeaZuDbhEGakXxx8zdUZ0CEnafMgfsFIuwvEGDE6NjOwGWAHfBZtH/ajQSSeqsvniB22juFQG1QLH8cwYqCW+dzrjdwfxu/r4K5j/b/AV3MpFA=)

As mentioned above, reassign a new array to the `ref` via the prop breaks the reactivity which is not allowed.

> Unlike states which are all immutable in React, the Vue doc mentions that mutation on `ref` is valid-- [Reactivity API: Core #ref | Vue.js](https://vuejs.org/api/reactivity-core.html#ref).

If you would like the immutation way to update the `unorderedlist`, you may need to modify its structure, like what we did to `msg`:

```javascript
// from
const unorderedlist = ref([]);
// to
const unorderedlist = ref({ value: [] });
```

You should also make some change on the functions:

```javascript
function addToUList() {
	unorderedList.value.value = [...unorderedList.value.value, msg.value.value];
    emits('submitMsg');
}
function clearUList() {
	unorderedList.value.value = new Array();
}
```

- [Vue Playground](https://play.vuejs.org/#eNqtV19v2zYQ/yqcXuwOtd103YvnGm3Sot3QrEWSdQ9RHmiJltnIpEBScQzD3713pChRkh0jQAukMHnH4+/+8O6nXfS+KMYPJYum0UwniheGaGbKguRUZG/jyOg4mseCrwupDNkRxZZkT5ZKrskAjg3+qmUXcl1UgvEEF2g2kH/h2tRyXHg5IbFIpADpWmfkLV4x3JEHmpdsSgafWZ5L8r9UefrbgOxfeF2pUqZYaq26M7d3L8Cak5aiL69t3t6BHVCNxbIUieFSkCRnVF3qbPiC7BARIBlbbfc/WIijOIIz+1jMJi5QEBZYGLYucmoYrAiZrc7m54rRe/IdDl0xCtYfuNnOJiCwCglG6WG0linLIbxwTxyRiZOl/AGAUK1BUFBFM0WLlQ0/gX8zXVAxv1kx5waRS7SWsjlizRRjhotMzyZ2j3A9JQAUj+x2jTtkDw6ArQnc5S7NIUDuggrVtIrcCCWAJAgkxKClWUfZ67bC7rWncLt31e280+Vizc3ICXzsvRSjMZsEcYWlNtvchRjdczkiZEGT+0zJUqSjROZSTcknyoVeSCVtXRFS0DSFsIy4yLmA1L8av/5TsbWV2kiMlzl79PZSruHO7RT3ApWMFl4DfqKRs66VOl1ecU1VxsXISKtfazeIFrlM7lsyZ2lhxEkblUDxbGU6cA65/NTt4UmIGyRvSs6KR6JlztMAlo0yS+vIW9WRoikvodLeFGG8DHs0o1GhOODc+hNVgr4g5ostFa07ffqu5Jbm51CnPWu6TBKmdcfaZynY9gPbHDT2Cd6EuyY0tKFKQAQ6hq4hLjk7V3LTP5FCJ2Sqc+CSKilD3UXWc7lfnh9kCqY6DuLJjnv9k5cMIr2+ZrTrFZ7u+NQ//YWtpbhY8eWyh7nt3YGL4SVvr6QOESel0lKBv5ILE4TGbk9Jte0PQBuyrzd6GfmxcGLa+C5eKFlo6L0pW0Ipf8PVDJoZ9p7vrpXXPV0bhRHYk/182IyBUBlkb53FdutXDCCErb8+Uff+weBk49fMDxLXv21bgW7RtG8uitKErb9zT6UZm9miNCYw5Z9eJ+owN94lOU/uUaXyAWxc4c/ZxNlw2CYVuHZfhXT4KXwiHYTU49/IK7bUXQaAr+Jovpz7wVyYkvdK0S30dEzZvHq7rdERJLalS/a+A+rsUPJtxbkC8JgYDJoG00dceUxuCkHYkBL4o83JXQj6ZRvgS8tVsKJcRIbWdXcv/tXVBX3lRn7FI76+YhPYqUhGUerVsMM5nDHiHBgOarADS10M1GP/ov/aF7Ugd/jM7Xg8Piq37oUbdyfBoN+xaROqk37DuOUJG7467JK18QyXBNu4crEFYO099WSpMhyu8O9z9XqONoFBwS9PuTqvOlM8rV90h7FZItXIQOreYLMBhVXp4pAPG7d/4u0R1Xvwoan67TcV1laYv0/BoPRFTBBfqy+0UPagVXOhNQKPdqKDwJoS6ABziwsUEwN81lPQQ+hkHq5yDg10KZWjk4SL8IlC8B3VBZI7m+S8SdMksNJQX/z3yzLpB3g7k373GZm09X44k3Xtn8jlUzh/UVoPoXxOWssmIbGBkddP7IGHfiTB1sAksBimOBx+uGoe/fFPjDEi96SmS6fP2nQdO0L384HgZtUw8efIX4ScqlwLIMxnS4V/nrf2WNcih63QhP3wAGJe7W14alZT+BB4hBOQLGG6mObk9+OkbrPipiZ0AT8zGqwteTb+oaUAVuCaboQfrTxn6muBkQRSALO3in1E4fN884/dMwomh99PViy5P7D/Qz/iXhx9Q9KiHiCrtczAdw2DskLxx+t/oUYDIfClMgftJ4TAfSDAiNGpnYPLADvQs2j/tmwGknqjPz5C7LR3CoHaoFj9OAJ6g4z1mOsN3D/Gb6pg7qP9T5dJvNs=)

## Shallow Ref

The example below use `shallowRef` instead of `ref` for the `lists`. You can see both add to list button don't make changes on the screen. But if you then click the "restore to default" button, the added items show up suddenly.

As the `msg` is used in the `List.vue` and the "Resume" button triggered a reassign to the value from the parent-- `App.vue`, it also have the `lists` to update.

- [Vue Playground](https://play.vuejs.org/#eNrFV19v2zYQ/yo3vdgdYqvpuhfXMdakRbuh3Yqk2B7iPNASLbOhSIGkkhiGv/uOpChTcuyk6EMDJDF5/373h3fnTfK2qsZ3NU0myVRnilUGNDV1BZyI4myeGD1PZnPBykoqAxsw64rC1YpwLu8v6fIElP2j2wvYwlLJEgaoc/CmFbyQZdUQxqk9WJsR/RPTpqXbQ6ADzEUmBVJLXcCZNTfcwB3hNZ3A4CNFs/CfVDz/ZQDbF4GXowY9iXBONyD5BLRRTBTXN2+gjk6wncGZNbVzY7ixZ3BC1zcn/mBlmsP2BWJLUzASw0WBLcGsKJCqQoCKihz/5B6M5HTMZTEcKDrylAHCXNYiM0wKZNd1ST/rYvgCnE10c+zcQ2cPOorGt3MxTX3GMD94MLSsODEUTwDT1ensXFFyC/+ipktK0NodM+tpigTHkNmM3I1KmVM+QaOYa/w7TyD19JzdQcaJ1kioiCKFItXK1YINxVRXRMy+os8eq1xajTmdtfCnqTsDwzwgUMu+2UTebdEB1JOiHW9wURuDAQk2F0bAohiNKsVKotZg6IPZnZSsMZQ5ZLXSUuG9ZMJQhfD/yDjLblFBG1kEfek+Q0m1JgVC87a8XVsrbSBc4aCw+4/aDgVomkbhxqM2a+4jb732mQRYkOy2cFBHmeRSTeADYUIvpJKutAEqkudYgyMmOBOY6JfjV78rWjqqC9B4yelD0JczjTbXE3sXsRSkChz40So57WtpMxgYMYoFEyMjHX/LvUO04DK77dC8JpuXp3Q0BMWKlenBeczlY9ZjSYwbPp8JnFYPgO+K5RGsUBAh8o51pEjOaizA11Ucr24pNRJNgj5ZzBdrIjo2Q/ou5ZrwcyzfPW26zjKsrp62j1LQ9Tt6/6iyD4pSbyZWdE+UwAj0FF1hXDg9V/J+XyLHTk1VT+AzUVLGvPFbOlie72SOqnoOWsmee/uSnylGuryipO+Vle75tC/9iZZSXKzYcrmHuevdI4bxpa4vpY4R97pCGxp3PYHmOghgd3KvNzlJwmR69jSUOCt0f+aFKVQpWWls4zldYqV/sadp6PGTXXP3Y8gi2c6Gdq4EedcuUflZY2foFHqOeIBQ89j8aKfIYPDktNDUKwvN1zUd7CW7fs9EVbdt0rdCb6BhmZt+A39ei3bYfYemptuY0wZVt91ilsJ+8ESWAHp5cstKP1n2xRxN1qF0NW+6WTWO7xeu0jC5O3MbL3hyMMV28ZmbNs0Zp0RZx4d2A5vALV3jzLWf8Z9T5dPRVMHcRHfXlu1mjLODZXT40hWQcZjsb9dEVEmP11J4NR1R7E5f5Y+hq2q9GvYM+jhgyFpkMfRjBU2UYSgVqnf1ambRaVx/XrUbTK/mC8Xytt57C5CFG9EObyuh04Xi7/b0I08Bg342i+I4kBz3xGT2NkclEtzgQH0WSOeVHIfTNM/OnHjGu/RgdiUXwHiDF/be7bsuLO6p7iOSPD5xhr1jKZXvHMBEUxWSo1q/F+JGOE052yUgjVTs9kT782M5CrOsm6Nw+z05qjs5qsVPzlID53uyVO9CPDfYww/lqT6UJyeVRmriTMUt3J52r/Lw/jx2+3jTgPq74ml3F7VPtr8bg71s+ob9OAqG7MJQlwJ79elS2d+wlO2tFAuOV7EKt1Xj1tnc3bPcrCa45T6gBGZFmD6mGfx6eGO5XzHTbivR8mE0aluyYvxNS4GzzbfKxH5RY5yqfyobSRxtOGya2Cfu6+pf7s6omjbfU1FmRbPbR+6/6Qd7N0++2NGr7nB6tzSDSzvFF2TJ76/+xmKMiDj2a47cR4g4wTHAFqNnO0eXEXbE59D+6WYyJvWrfv+AsdPBKQvUBcXxzxOcz3YdO+T6Du5v49dNMLfJ9n+cUJ0m)

Thanks for reading 🙏
