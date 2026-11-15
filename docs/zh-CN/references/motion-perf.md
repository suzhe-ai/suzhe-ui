# 动效与性能

> 顶尖的标准：最好看、最高级的同时，好电脑、坏电脑、好手机、坏手机上都流畅丝滑。流畅本身就是设计的一部分，不是上线前的优化项。

## 动效的分寸

- **动效只服务于状态变化**：出现、展开、完成、失败。纯装饰的无限循环动画（流光、呼吸光、脉冲点）能不用就不用，必须用的话，离开视口就暂停。
- **缓动统一**：
  - 常规过渡用 `--ease: cubic-bezier(.28,.11,.32,1)`，这是苹果式的「快起、慢收」；
  - 出现用 `--ease-out: cubic-bezier(0,0,.5,1)`；
  - 需要一点弹性时用 `--spring: cubic-bezier(.2,.9,.25,1.02)`，只回弹 2%，不要做成果冻。
- **时长**：按钮、悬停 150–250ms；卡片展开、弹窗 300–450ms；滚动进场 600–900ms。超过 1 秒的动画只用在「完成」这种值得庆祝的时刻。
- **滚动进场**：小幅上移（16–24px）加淡入，只播一次。不要每次滚回来都重播。
- **悬停**：卡片抬起 2–4px，阴影从 `--sh-card` 过渡到 `--sh-card-h`。图片可以放大到 1.02–1.04，不要更大。
- **按下**：`transform: scale(.985)`，给人「按下去了」的手感。
- **不要做**：大面积光雾、光晕、装饰性的地平线或横线，和与内容无关的动态背景。

## 长任务的进度表现（例如兑换、充值、生成）

- **按钮长成卡片**：点击后按钮原地变形为一张进度卡（转圈 → 展开 → 显示状态行），不要弹出一个新的遮罩。变形前后的尺寸要用同一种单位（比如都用 px），否则过渡会跳。
- **进度要真实**：用真实时钟计算。用户切到别的标签页再回来，进度照常往前走，不能靠动画时长假装进度。
- **分阶段**：把整个过程切成 3–4 个有名字的阶段，每个阶段下面轮播一两句小步骤说明。等待时间比平时长时，换一句安抚文案，比如「比平时稍慢，仍在处理」。
- **把进度画在品牌身上（「刻印」）**：比转圈和进度条更有辨识度的做法，是让进度本身成为主角。比如产品标志先显示成淡线稿，一支发光的笔沿着轮廓一笔笔刻出来，刻到哪里，进度就到哪里；笔头带一点刻屑火花，关键阶段随心跳轻轻一亮。多段轮廓要拆成多条 path 依次刻，否则共用一条虚线时每段都会重置，笔头对不上。
- **档位配色**：同一套动效，不同档位只换点缀色（比如入门青绿、进阶冰蓝、高级紫、顶级香槟金），写成卡片上的 CSS 变量，骨架不变。
- **完成**：要「亮」，不要「炸」。笔头化成光，沿着已经画好的线向外漫开，加少量光尘和一圈柔光涟漪，最后轻轻呼吸。不要实心色块填满、中心火花爆炸、硬边白圈。
- **失败**：已经画好的线退回去，颜色变暗，元素轻晃一下，再用一句话说清后果，比如「次数已退回」。

## 重特效按设备分档

粒子、WebGL、着色器这类特效在好电脑上看不出成本，但在软件渲染、低核数 CPU、老手机上会把帧率拖到 20 帧以下。一律分三档：

| 档位 | 表现 |
|---|---|
| 0 | 原样 |
| 1 | 粒子减半、限到约 30 帧、不画光晕、DPR 固定为 1 |
| 2 | 静态图 |

**起步判定**：拿不到 WebGL、渲染器是软件实现、CPU ≤ 2 核、内存 ≤ 2GB 时，直接从 1 档起步。

**运行中判定**：取帧间隔的中位数，太慢就降一档。**只降不升**，避免在两档之间来回抖。

```js
// 起步档位
function startTier() {
  try {
    const c = document.createElement('canvas');
    const gl = c.getContext('webgl') || c.getContext('experimental-webgl');
    if (!gl) return 1;
    const ext = gl.getExtension('WEBGL_debug_renderer_info');
    const r = ext ? String(gl.getParameter(ext.UNMASKED_RENDERER_WEBGL)) : '';
    if (/swiftshader|llvmpipe|basic render|software/i.test(r)) return 1;
  } catch (e) { return 1; }
  if ((navigator.hardwareConcurrency || 8) <= 2) return 1;
  if ((navigator.deviceMemory || 8) <= 2) return 1;
  if (matchMedia('(prefers-reduced-motion: reduce)').matches) return 2;
  return 0;
}

// 运行中：动画开始 1 秒后，每 40 帧取一次帧间隔中位数，只降不升
function makeTierWatch(getTier, setTier) {
  const buf = []; let last = 0; let warm = performance.now() + 1000;
  return function onFrame(now) {
    if (last && now > warm) {
      buf.push(now - last);
      if (buf.length >= 40) {
        const m = buf.sort((a, b) => a - b)[20]; buf.length = 0;
        const t = getTier();
        if (t === 0 && m > 24) setTier(1);
        else if (t === 1 && m > 45) setTier(2);
      }
    }
    last = now;
  };
}
```

把当前档位写到画布上，例如 `canvas.dataset.lite = tier`，方便调试和验收。

## 必须做的省电措施

- 用 `IntersectionObserver` 观察特效：离开视口时 `cancelAnimationFrame`，回到视口再继续。
- `document.visibilitychange`：标签页被隐藏时暂停。
- `@media (prefers-reduced-motion: reduce)`：关掉进场动画和循环动画，只保留状态切换本身。
- **毛玻璃**（`backdrop-filter`）只用在导航这种小面积元素上。全屏模糊在软件渲染下只有 30 多帧。
- **图片**：首屏图片用 `fetchpriority="high"`，其余图片都加 `loading="lazy"` 和 `decoding="async"`，并且一定写 `width`/`height`，防止加载时页面跳动。
- **字体**：苹果设备直接用系统 SF 和苹方，不下载字体文件；其他设备的中文字体用 `unicode-range` 分片按需加载，并加 `font-display: swap`。

## 怎么测

不要只在开发机上看。用 Chrome DevTools 协议（或者 Performance 面板）模拟：

- CPU 降速 4 倍；
- `--disable-gpu`（模拟软件渲染）；
- 手机尺寸 390 宽、DPR 3，同时 CPU 降速 6 倍。

记录首屏帧数和主线程占用。目标：弱设备上不低于 50 帧，或者至少自动降到能流畅运行的那一档。
