<!--
 * @Date: 2021-09-05 18:11:52
 * @LastEditors: wenfujie
 * @LastEditTime: 2021-09-05 18:37:14
 * @FilePath: /Animation/flip-animation/README.md
-->

## 前言
该 Demo 是从 [sl1673495/flip-animation](https://github.com/sl1673495/flip-animation) 搬运过来的。

[运行效果](https://wenfujie.github.io/Animation/flip-animation/)

## 主要使用 Web Animation API 实现

实现一张图片移动，如果用css实现

```js
const currentImgStyle = currentRect.img.style
currentImgStyle.transform = `translate(${invert.left}px, ${invert.top}px)`
currentImgStyle.transitionDuration = "0s"

this._reflow = document.body.offsetHeight

currentRect.img.classList.add("move")

currentImgStyle.transform = currentRect.img.style.transitionDuration = ""

currentRect.img.addEventListener("transitionend", () => {
  currentRect.img.classList.remove("move")
})

```

这也是 Vue 内部 transition-group 组件实现 FLIP 动画的大致思路，Vue 应该是为了兼容性和代码体积等一些方面的权衡，还是选择用比较原生的方式去实现 FLIP 动画，这段代码让我觉得不舒服的点在于：

- 需要通过 class 的增加和删除来和 CSS 来进行交互，整体流程不太符合直觉。
- 需要监听动画完成事件，并且做一些清理操作，容易遗漏。
- 需要利用 document.body.offsetHeight 这样的方式触发 强制同步布局，比较 hack 的知识点。
- 需要利用 this._reflow = document.body.offsetHeight 这样的方式向元素实例上增加一个没有意义的属性，防止被 Rollup 等打包工具 tree-shaking 误删。 比较 hack 的知识点 +1。


而利用 Web Animation API 的代码则变得非常符合直觉和易于维护

```js
const keyframes = [
  {
    transform: `translate(${invert.left}px, ${invert.top}px)`,
  },
  { transform: "" },
]
const options = {
  duration: 300,
  easing: "cubic-bezier(0,0,0.32,1)",
}

const animation = currentRect.img.animate(keyframes, options)

```

关于兼容性问题，W3C 已经提供了 Web Animation API Polyfill，可以放心大胆的使用。