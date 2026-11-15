# 工程细节与交付前验收

## 一、写代码时就要避开的坑

这些坑每一个都在线上真实出过问题，而且**光看截图发现不了**。

| 坑 | 现象 | 做法 |
|---|---|---|
| iOS 输入框放大 | 手机上点输入框，整页自动放大，之后缩不回去 | 手机端 `input, textarea, select { font-size: 16px }`（≥16px 就不会放大） |
| grid/flex 子项撑破 | 一个不换行的长网址把 `1fr` 轨道撑宽，整页出现横向滚动 | grid/flex 子项加 `min-width: 0`；长文本加 `overflow-wrap: anywhere` |
| 点 SVG 图标没反应 | 按钮里放了 SVG 图标，点在图标上时 `e.target` 是 `SVGElement`，委托判断 `instanceof HTMLElement` 直接 return | 统一写 `const t = e.target instanceof Element ? e.target : null; t?.closest('…')` |
| Inter 把 5x5 画成 5×5 | Inter 的 `calt` 特性会把数字之间的 x 替换成乘号 | `font-feature-settings: "calt" 0`。注意 `font` 简写会重置这个特性，要在简写之后再写一次 |
| 数字跳动 | 倒计时、价格、进度百分比变化时宽度来回抖 | `font-variant-numeric: tabular-nums` |
| 自动填充不触发事件 | 浏览器自动填充密码时不触发 `input` 事件，依赖输入才启用的按钮一直是灰的 | 除了监听 `input`，再加 `change` 和 `animationstart`（配合 `:-webkit-autofill`），或者每 400ms 轮询一次兜底 |
| 删掉元素后整段 JS 失效 | 删了 DOM 元素，但模块顶层还有一行裸的 `$('x').addEventListener`，抛错后打断了后面所有初始化。页面看起来正常，其实已经点不动了 | 删元素前全局搜索它的 id 和 class；改完必须看控制台有没有错误 |
| 浏览器缓存旧资源 | 改了 CSS/JS，用户看到的还是旧版 | 静态资源 URL 带版本号 `?v=日期-序号`，每次修改都换新号，所有引用一起换；绝不复用旧号 |
| 搜索框被自动填充 | 浏览器把用户名填进了搜索框 | `autocomplete="off"`，必要时加 `name` 随机后缀 |
| 隐藏的 iframe 被节流 | 用 `display:none` 藏起来的人机验证 iframe 被浏览器节流，验证一直不出结果 | 用 `opacity:0; pointer-events:none; height:0; overflow:hidden` 加 `inert` 藏起来，不要用 `display:none` 或 `visibility:hidden` |
| 全局焦点环变形 | 全局 `:focus-visible` 写了 `border-radius`，把胶囊按钮的焦点环变成了方的 | 全局焦点只设 `outline`，圆角让元素自己继承 |
| 按钮样式被压 | 深色区块或导航里的 `.dark a { color }`、`html.dark button:not(...)` 这类规则特异度更高，把按钮字色盖掉（比如浅灰按钮上出现白字，几乎看不见） | 区块里的链接规则写成 `a:not(.btn)`；写按钮样式前先查有没有全局 `a` / `button` 规则；不要用 `!important` 打补丁 |
| 平滑滚动干扰测试 | `html { scroll-behavior: smooth }` 让脚本里的 `scrollTo` 不能立即到位 | 测试时用 `scrollTo({ top, behavior: 'instant' })` |

## 二、交付前验收（按顺序做）

1. **手机宽度**：390×844 下，页面没有横向滚动（`document.documentElement.scrollWidth <= innerWidth`），文字没有溢出，按钮可以点到（点击区域 ≥ 44px）。
2. **区块底色**：逐块读出背景色，确认相邻两块不同。不要靠眼睛看。

   ```js
   [...document.querySelectorAll('main > section, body > section')]
     .filter(s => s.offsetHeight > 0)
     .map(s => [s.id || s.className, getComputedStyle(s).backgroundColor])
   ```

3. **控制台**：0 个错误（包括被安全头拦截的报错）。
4. **真实点击**：带图标的按钮，用真实坐标点击图标中心，不要只用 JS 的 `.click()`（它的 target 是按钮本身，测不出 SVG 的问题）。
5. **键盘**：用 Tab 走一遍，焦点始终可见，弹窗里焦点出不去，按 Esc 能关闭。
6. **深浅色**：页面支持深色模式的话，两种都截图；只设计了浅色的页面，确认在深色系统下没有被浏览器反色。
7. **弱设备**：有动画或特效的页面，按 `motion-perf.md` 的方法模拟弱设备，确认能降档、不卡。
8. **减少动态效果**：开启 `prefers-reduced-motion` 后，循环动画都停了，页面仍然完整可用。
9. **外部依赖**：用到人机验证、支付、第三方登录的页面，真实打开一次，确认能加载出来。安全加固不能以丢功能为代价。
10. **文案**：数一数元素和句子，能删的都删掉。帮助文案是不是一句话就说清了？有没有灰掉的、不可用的项还显示着？
