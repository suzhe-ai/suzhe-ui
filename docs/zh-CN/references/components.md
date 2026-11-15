# 组件写法

所有组件都基于 `tokens.css` 里的变量和类名。这里只写结构、关键数值和容易做错的地方，不写完整页面（完整页面见 `assets/starter.html`）。

## 目录
1. 顶部导航
2. 首屏 hero
3. 区块头与双色标题
4. 卡片：方案卡、商品卡、理由卡、大块陈列
5. 品牌通栏 / 横幅
6. 横滑货架
7. 弹窗与手机底部抽屉
8. 表单：输入框、步骤、报错
9. 提示：alert、toast、按钮内加载
10. 长任务状态卡
11. 徽章、选择器、分段控件
12. 页脚
13. 空状态
14. 图标

---

## 1. 顶部导航

```html
<nav class="gnav" aria-label="主导航">
  <div class="gnav-in">
    <a class="brand" href="/">…标志 + 名称…</a>
    <div class="gnav-links">…链接，flex:1 居中…</div>
    <div class="gnav-act">…36px 圆形图标钮 / 28px 高的浅色胶囊登录钮…</div>
  </div>
</nav>
```

- 48px 高，深色半透明 `rgba(22,22,23,.86)` 加 `saturate(180%) blur(20px)`。链接 12.5px，白色 80% 透明度，悬停变纯白。
- 标志用透明底单色 SVG（`fill: currentColor`），约 20×13px，后面跟 14px/600 的名称。**不要把标志放进圆角方块里。**
- 下拉菜单铺满全宽：深色 `.96` 透明度，内容分三栏，大字链接 600 24px；页面其余部分盖一层 `rgba(0,0,0,.24)` + `blur(18px)` 的遮罩。
- 开合状态写在属性上（`[aria-expanded="true"]`、`[data-menu-open]`），样式跟着属性走，不靠 JS 加类名。
- 1023px 以下改成汉堡菜单：两根 16×1.4px 的线，打开后各旋转 ±45° 合成 ×；菜单全屏覆盖，链接 600 28px，逐项延迟 30ms 依次出现。
- 页面第一块要留出导航的高度：`padding-top: calc(var(--nav-h) + …)`。

## 2. 首屏 hero

```html
<section class="hero sec-black glow-host">
  <h1 class="h-hero">一句话说清价值</h1>
  <p class="sub">一句补充，不超过两行</p>
  <div class="hero-cta"><a class="btn btn-lg btn-primary">主操作</a><a class="tlink">次操作</a></div>
  <div class="hero-stage"><img …主角…></div>
</section>
```

- **高度**：`max(440px, calc(100svh - var(--head)))`（先写一行 `100vh` 兜底）。`--head` 是下一块要露出来的高度，桌面约 120px，手机约 200px，**让首屏底部能看到下一块的标题**，用户才知道往下还有内容。
- **结构**：居中标题 → 按钮组（gap 16px，上距 28px）→ 主角陈列区（`flex: 1 1 0` 吃掉剩余高度，图片按高度缩放，比视口宽时两侧裁掉）。
- **背景**：黑底，叠两团很淡的径向渐变（透明度 .07–.09）做色温变化，不用背景图：
  ```css
  background:radial-gradient(56% 60% at 12% 0%,rgba(46,211,166,.075),transparent 70%),
             radial-gradient(48% 56% at 92% 6%,rgba(90,125,255,.085),transparent 70%),#000;
  ```
- **入场**：标题 `rise 1.1s var(--spring)`，按钮组延迟 .1s，陈列区 `fade 1.2s` 延迟 .15s。
- **矮屏**：`(max-height:820px)` 和 `(max-height:700px)` 两档，逐级缩小标题和留白，把高度让给主角。
- **整页只允许 hero 这一处有循环动画**，并且离开视口就暂停。

## 3. 区块头与双色标题

```html
<section class="sec sec-gray">
  <div class="wrap">
    <header class="sec-head">
      <p class="eyebrow">新品</p>
      <h2 class="h-sec"><span>主句，说是什么。</span><span class="t-soft">副句，说为什么值得。</span></h2>
    </header>
    …内容…
  </div>
</section>
```

- 一个标题同时交代「是什么」和「为什么」，副句用次要灰。这是苹果商店式的标题写法，信息量大但不显得挤。
- 区块左对齐（首屏和推广通栏才居中）。
- 区块结尾可以放一个「了解更多 ›」文字链接，不要再放一个按钮。

## 4. 卡片

**放在什么底上决定怎么画**：灰底区块里放白卡加 `--sh-card` 阴影；白底区块里放灰卡（`--bg-2`），不加阴影。`tokens.css` 的 `.card` 已经按这个规则写好了。

### 方案卡（订阅档位、套餐）
- `padding: 30px 24px 24px; border-radius: 18px`。
- 从上到下：产品图（约 132px）→ 眉标 → 名称（600 26px/1.12）→ 一句标语 → 价格（600 28px/1，`tabular-nums`；单位 14px 次要灰；划线价 14px 三级灰）→ 发丝线 + 要点列表 → 按钮。按钮用 `margin-top:auto` 贴到底部，几张卡的按钮才能对齐。
- 要点列表用 16px 小圆图标：黑底白勾表示包含，蓝底表示升级，灰底表示不含。列表行写成 `grid-template-columns: 16px minmax(0,1fr)`。
- 悬停：卡片上浮 3px、阴影加深；产品图 `scale(1.05) rotate(-2deg)`，.7s spring。
- 桌面 4 列（3 档就 3 列），平板 2 列，**手机改成横滑卡片带**（`flex: 0 0 min(82vw,320px)` + 吸附），并取消悬停上浮。

### 商品卡
- 宽 248px，最小高 344px，padding 22px。名称 600 19px/1.26，最多两行截断。
- **产品图是主角**：透明底渲染图，加 `filter: drop-shadow(0 14px 16px rgba(0,0,0,.16))`。背后放一团柔光，颜色由每张卡自己的 `--glow` 变量决定：
  ```css
  .card .media{position:relative}
  .card .media::before{content:"";position:absolute;inset:16% 20%;border-radius:50%;
    background:radial-gradient(closest-side,var(--glow),transparent);filter:blur(16px);opacity:.75;z-index:-1}
  ```
- 主推商品可以用宽卡：左文右图（56% / 44%），可以是黑底；手机上改成上图下文。

### 理由卡（为什么选我们）
- 灰底卡，悬停 `scale(1.015)`。40px 线性图标在上，标题用 `margin: auto 0 8px` 推到卡片底部，留出上方的呼吸空间。
- 桌面 3 列，平板 2 列，手机 1 列并改成「左图标、右文字」横排。

### 大块陈列（精选、旗舰）
- 两列大块，最小高 `clamp(560px,46vw,680px)`，内容居中，灰底或黑底，**没有圆角**，块与块之间靠 12px 白缝分隔。
- 悬停时图片 `translateY(-8px) scale(1.035)`，1s spring。

## 5. 品牌通栏 / 横幅

- 骨架不变：底色只能是黑、白、浅灰；字体是系统黑体；按钮是站内的胶囊按钮。
- 品牌感只放在点缀上：眉标颜色、标题里一个词的渐变字（`background-clip:text`）、底部一团品牌色柔光、苹果 App 图标式的圆角品牌图标。
- 每个横幅在自己的根选择器上声明一组局部变量（`--xx-glow`、`--xx-accent`），只在横幅内生效，不污染全站变量。
- 整屏通栏高约 600px，上下留白 132px / 136px（手机 92px / 96px）。
- 彩色横幅上方加 `.seam`（12px 白缝），像苹果首页横幅之间的白色间隔。
- 同一页的几张横幅，构图和材质要刻意区分开，不要套同一个模板。
- 光晕用超大径向渐变放在 `z-index:-1` 的伪元素上，父级 `.glow-host`（`isolation:isolate`）。

## 6. 横滑货架

```css
.track{display:flex;gap:18px;overflow-x:auto;overscroll-behavior-x:contain;scroll-snap-type:x mandatory;
  scroll-padding-inline:var(--pad);padding-inline:var(--pad);scrollbar-width:none}
.track::-webkit-scrollbar{display:none}
.track>*{flex:0 0 auto;scroll-snap-align:start}
/* 铺满整屏宽，但第一张卡与正文左边对齐 */
.track{--pad:max(var(--gut),calc((100% - var(--wrap))/2))}
```
- 两侧渐隐用 `mask-image`，并且按状态加：能往左滚才显示左侧渐隐，能往右滚才显示右侧。
- 桌面上可以显示左右箭头，每次滚 `max(240px, 可视宽 × .82)`；`@media (hover:none)` 时隐藏箭头。

## 7. 弹窗与手机底部抽屉

- 遮罩 `rgba(0,0,0,.42)`，淡入 .24s。
- 盒子：`width:min(560px,100%); max-height:calc(100dvh - 48px); padding:30px 30px 28px; border-radius:22px; box-shadow:var(--sh-modal); overscroll-behavior:contain`。
- 入场：`modal-in .44s var(--spring)`。
- 标题行：左边标题 600 24px/1.17，右边关闭按钮。**关闭按钮做成无底色的图标，悬停时提亮**（带描边或灰底的圆形关闭钮被否过两次）。
- **手机上变成底部抽屉**：贴底，顶角 16px，`sheet-up .4s var(--ease-apple)`；`padding-bottom: calc(24px + env(safe-area-inset-bottom))`；顶部一个 36×5px 的抓手。内容滚到顶以后可以下拉关闭：位移 = 手指位移 × .85（阻尼），超过 120px 松手就关闭，否则回弹。
- **开合分两步**：先加 `.is-open`（`display:block`），下一帧（两个 `requestAnimationFrame`）再加 `.is-in` 触发过渡，否则过渡不会播放。
- **焦点**：打开时焦点移进弹窗（`focus({preventScroll:true})`）；Tab 在弹窗内循环；Esc 关闭（多层时只关最上层）；关闭后焦点回到触发按钮。设置 `aria-modal="true"`。
- **滚动锁**：打开时 `html, body { overflow:hidden }`。

## 8. 表单

- **输入框**：用 `tokens.css` 的 `.field`。高 52px，圆角 12px，17px 字。聚焦是单层 2px 蓝边（`border-color` 加 `0 0 0 1px` 同色阴影），不加外光晕。
- **字段头**：标签在左（600 14px），提示在右（13px 三级灰，最多占 62% 宽，单行省略）；手机上提示换到下一行。
- **报错**：用 `aria-invalid="true"` 驱动样式。边框变红，抖动一下，下方显示 13px 的报错行加 15px 线性图标，报错行设 `role="alert"`。**只在提交后才显示报错。**
- **自动填充**修正底色：`:-webkit-autofill { box-shadow: inset 0 0 0 100px <底色>; -webkit-text-fill-color: <字色> }`。
- **密码眼睛**：输入框右侧放一个 40px 的按钮（输入框 `padding-right:48px` 留位置），斜杠用 `stroke-dasharray` 画出来，状态写在 `aria-pressed` 上。
- **验证码框**：48px 高，600 20px 字，`letter-spacing:.32em`，居中，`tabular-nums`。
- **步骤**：桌面左边一列 28px 编号圆加 2px 竖连接线，完成后编号圆变蓝并显示白色对勾；手机上去掉编号列，把编号放到标题前的 24px 小圆里。
- **流程页的主按钮**可以用石墨黑（`.btn-ink`），圆角 10–12px，高 44–50px。品牌页的主按钮用蓝色胶囊。

## 9. 提示

- **alert**：圆角 14px，14px/1.55 字，按状态换浅底色（错误 / 成功 / 信息 / 默认灰）。左侧 18px 圆形图标用 `::before` 加内联 SVG 画，不用额外 DOM。入场只上移 6px。
- **toast**：用 `tokens.css` 的 `.toast`。深色半透明胶囊，顶部居中、紧贴导航下方，3 秒后消失，容器设 `role="status" aria-live="polite"`。
- **复制反馈**：复制后图标或文字变绿约 1.5 秒，不弹窗打扰。
- **按钮内加载**：18px 转圈绝对定位在按钮中央；文字 `opacity:0`，但保留它的占位，按钮宽度就不会跳。成功后按钮变绿并禁用点击。

## 10. 长任务状态卡（兑换、充值、生成）

完整说明见 `motion-perf.md`「长任务的进度表现」。要点：

- 主按钮原地长成一张深色状态卡（类似灵动岛）。分阶段的时间轴大约是：0ms 图标换成转圈 → 650ms 收起次按钮 → 950ms 卡片横竖展开 → 1620ms 内容入场 → 1900ms 展开分段步骤。
- 卡片内容：真实进度环（`stroke-dashoffset` 绑定进度变量）+ 沿着环流动的高光 + 下方 4 段分段条（当前段流光，完成段填满）。
- 高度动画用 `grid-template-rows: 0fr → 1fr`，不要写死 `max-height`。
- 收起时按展开过程倒放：先压扁成宽胶囊，再收窄回按钮。
- 成功：徽章弹出，对勾线条画出来。失败：进度环抖一下，产品图变灰，再加一句话说明后果。

## 11. 徽章、选择器、分段控件

- **状态徽章**：`padding:5px 10px`，胶囊，600 12px/1，浅色底配同色系深色字。
- **状态圆点**：8px 圆点，外加 3px 同色浅光圈。**正常状态不显示圆点，只标异常**（库存紧张标橙，没货标红）。
- **实时胶囊**：浅蓝底 `rgba(0,113,227,.08)`，字色 `#0058b0`，500 12px；前面一个 6px 的闪烁圆点，数字用 `tabular-nums`。
- **可选项**（胶囊或卡片）：默认 `inset 0 0 0 1px var(--line)`，悬停描边变深，选中改成浅蓝底加 `inset 0 0 0 2px var(--blue)`。用 inset 阴影做描边，选中时尺寸不会跳。
- **分段控件**：
  ```css
  .seg{display:inline-flex;gap:2px;padding:2px;border-radius:9px;background:var(--bg-3)}
  .seg button{padding:7px 16px;border:0;border-radius:7px;background:none;font:500 13px/1.3 var(--f-text)}
  .seg [aria-pressed="true"]{background:#fff;box-shadow:0 1px 3px rgba(0,0,0,.12),0 0 0 .5px rgba(0,0,0,.04)}
  ```
- **侧栏选中滑块**：选中项后面垫一块白色浮起卡片，切换时用 `transform` 和尺寸 .5s `var(--ease-glide)` 滑过去。

## 12. 页脚

- 灰底，12px/1.4，次要灰。
- 结构：注释说明 → 发丝线 → 4 列链接（列标题 600 12px 主色，链接 `--ink-4`，悬停变主色并加下划线）→ 发丝线 → 版权和语言切换（用 1px 竖线分隔，当前项加粗）。
- 手机上链接改成 2 列，版权和语言上下排。

## 13. 空状态

- 居中一行次要灰文字，上下各 48px 留白，再给一个可点击的替代建议（比如「试试 A、B 或 C」）。不放大插画。
- 数据能内联到页面里就内联，首帧就能渲染，不需要骨架屏。

## 14. 图标

- 用线性 SVG：`fill:none; stroke:currentColor; stroke-width:1.5–2; stroke-linecap:round; stroke-linejoin:round`，颜色自动跟随文字。
- 箭头、对勾、播放三角用 CSS 边框画就够了。
- 不用图标字体，不用 emoji 当图标。
- **按钮里放了 SVG 图标时，点击委托写 `e.target instanceof Element`**，不要写 `HTMLElement`。
