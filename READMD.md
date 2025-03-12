# 利用 Github + jsdelivr + Picgo 构建的免费图床


全局 js 的重试的机制
```js
// 多个域名
const baseUrls = ['fastly.jsdelivr.net', 'gcore.jsdelivr.net', 'cdn.jsdelivr.net']
// WeakMap
const offsetWeakMap = new WeakMap()
// 捕获阶段处理
window.addEventListener(
  'error',
  (event) => {
    const dom = event.target
    // 过滤 img 标签
    if (!/img/i.test(dom.nodeName)) {
      return
    }
    const src = dom.src
    // 引入 jsdelivr.net 判断
    const isJsdelivr = /jsdelivr\.net/.test(src)
    if (isJsdelivr) {
      let offset = offsetWeakMap.get(dom) ?? 0
      if (offset < baseUrls.length) {
        const url = new URL(src)
        for (let i = offset; i < baseUrls.length; i++) {
          const baseUrl = baseUrls[i]
          if (url.hostname === baseUrl) {
            continue
          }
          url.hostname = baseUrl
          offset++
          dom.src = url
          offsetWeakMap.set(dom, offset)
          break
        }
      }
    }
  },
  true,
)

```