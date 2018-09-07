# <div style="text-align:center;color:#FF9900">Html点滴</div>

* 为了跨浏览器兼容，请使用 [`window.pageXOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/pageXOffset) 和 [`window.pageYOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/pageYOffset) 代替 `window.scrollX` 和 `window.scrollY。`

  如果不能访问，替代方案：

  ```js
  // For scrollX
  (((t = document.documentElement) || (t = document.body.parentNode))
    && typeof t.scrollLeft == 'number' ? t : document.body).scrollLeft
  // For scrollY
  (((t = document.documentElement) || (t = document.body.parentNode))
    && typeof t.scrollTop == 'number' ? t : document.body).scrollTop
  ```

* 