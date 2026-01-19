数组去重、对象处理、日期格式化、滚动监听、请求取消、性能采集、调试日志…… 这些需求十年前存在，今天依然存在。这篇文章整理了50个在2025年依然被严重低估的JS / Web原生特性。它们全部零依赖、可直接落地，覆盖你日常开发中最常遇到的场景。

## 1. Set：数组去重 + 快速查找，比 filter 快 3 倍
Set天生唯一值，查找O(1)，告别filter+indexOf的O(n²)冗余循环，一行代码即可转数组，性能翻倍。
```javascript
const uid = [...new Set([101, 102, 102, 103])];
// [101, 102, 103]
```

## 2. Array.at(-1)：负索引取末尾，告别 length-1
支持负数倒序定位，不用再写繁琐的arr[arr.length-1]，DOM事件取最后一个元素尤其方便。
```javascript
const last = ['a', 'b', 'c'].at(-1);
// 'c'
```

## 3. Object.entries + fromEntries：对象数组互转，过滤空值神器
先拆键值对数组，过滤后再拼回对象，比reduce拼接更直观，URL参数清洗场景一步完成。
```javascript
const clean = Object.fromEntries(
  Object.entries({a: 1, b: '', c: 3}).filter(([, v]) => v)
);
// { a: 1, c: 3 }
```

## 4. ?? 与 ??=：只认 null/undefined，0 和空字符串不再被误伤
空值合并仅对"真正空"生效，表单数字0、空字符串都能保留，设置默认配置时更安全。
```javascript
const count = 0 ?? 10;
// 0

const cfg = {timeout: 5000};
cfg.retries ??= 3;
// { timeout: 5000, retries: 3 }

const oldCfg = {timeout: 5000, retries: 2};
oldCfg.retries ??= 3;
// {timeout:5000, retries:2}
```

## 5. structuredClone：深拷贝循环引用零依赖
JSON无法搞定的Map、Set、循环引用它都能复制，不用再加载lodash.cloneDeep，同步一行搞定。
```javascript
const copy = structuredClone({a: 1, b: {c: [2] } });
// { a: 1, b: { c: [2] } }
```

## 6. Intl.NumberFormat：原生货币千分位，替代 accounting 体积 0
内置本地化规则，自动加千分位、货币符号，压缩后零体积，报表、购物车金额展示直接调用。
```javascript
new Intl.NumberFormat('zh-CN', {style: 'currency', currency: 'CNY'}).format(1234);
// ¥1,234.00
```

## 7. Intl.DateTimeFormat：本地化长日期，替代 moment 一条语句
无需引moment全家桶，浏览器自动按区域输出年月日，SEO友好且支持多语言切换。
```javascript
new Intl.DateTimeFormat('zh-CN', {dateStyle: 'long'}).format(new Date('2025-12-22'));
// 2025年12月22日
```

## 8. element.closest：向上找祖先，DOM 结构随便改也不崩
事件委托时根据选择器取最近父级，层级调整无需改JS，比parentNode循环更健壮。
```javascript
const list = e.target.closest('.list');
// <div class="list">...</div>
```

## 9. IntersectionObserver：懒加载 + 曝光埋点，告别 scroll 卡顿
异步回调不阻塞主线程，图片无限滚动、广告曝光统计都能用，省去节流防抖逻辑。
```javascript
new IntersectionObserver(es =>
  es.forEach(e => e.isIntersecting && loadImg(e.target))
).observe(img);
// 进入视口时触发 loadImg
```

## 10. fetch + AbortController：请求随时取消，路由切换无竞态
信号控制器一处abort，所有监听请求立即停止，防止切换页面后旧数据覆盖新状态。
```javascript
const ac = new AbortController();
fetch(url, {signal: ac.signal });
ac.abort();
// 请求被取消
```

## 11. URL + URLSearchParams：解析拼接查询串，正则直接退休

## 12. PerformanceObserver：无侵入采集 LCP，一键上报 Web-Vitals
浏览器原生暴露性能指标，脚本零成本注入，页面加载速度可直接上报监控平台。
```javascript
new PerformanceObserver(list =>
  list.getEntries().forEach(sendMetric)
).observe({type: 'LCP', buffered: true});
// 输出 LCP 数值
```

## 13. requestIdleCallback：空闲时打日志，主线程零阻塞
浏览器闲下来才执行，适合埋点、日志，不抢占渲染资源，首帧体验更丝滑。
```javascript
requestIdleCallback(() => sendBeacon('/log', data));
// 浏览器空闲时发送
```

## 14. BroadcastChannel：同源多标签实时广播，localStorage 轮询说再见
无需storage事件兼容，postMessage即时同步，登录态、主题色切换秒级生效。
```javascript
const bc = new BroadcastChannel('auth');
bc.postMessage({login: true});
// 其他标签页收到 {login:true}
```

## 15. Page Visibility：切后台自动暂停，省电又省流量
视频、动画、轮询检测到隐藏即停止，移动端续航显著提升，用户切回再自动恢复。
```javascript
document.addEventListener('visibilitychange', () =>
  document.hidden ? video.pause() : video.play()
);
// 后台暂停，前台恢复
```

## 16. navigator.share：一键唤起系统分享面板，兼容微信微博
HTTPS下调用直接弹出原生分享，文案、链接、文件一次带走，不再自己拼分享SDK。
```javascript
navigator.share?.({title: '好文', url: location.href });
// 弹出系统分享
```

## 17. navigator.wakeLock：直播演示锁屏常亮，再也不黑屏
视频会议、PPT展示时保持屏幕常亮，结束自动释放，用户不用手动改系统设置。
```javascript
await navigator.wakeLock.request('screen');
// 屏幕保持常亮
```

## 18. EyeDropper：浏览器级吸管取色，设计师直呼内行
一键拾取屏幕任意像素色值，返回标准十六进制，做主题吸色、在线PS必备神器。
```javascript
const { sRGBHex } = await new EyeDropper().open();
// '#ff5722'
```

## 19. File System Access：读写本地真实文件，WebIDE 即开即用
用户授权后可保存到原始路径，断点续写、本地Git管理都能在前端完成，体验媲美桌面。
```javascript
const [fh] = await showOpenFilePicker();
const text = await (await fh.getFile()).text();
// 文件内容字符串
```

## 20. globalThis：跨环境全局统一，浏览器 Worker Node 全搞定
一套代码跑遍window、self、global，再也不用判断运行环境再挂变量，库作者最爱。
```javascript
const root = globalThis;
// 浏览器下为 window
```

## 21. Array.from：快速生成范围数组
给定长度+映射规则，无需再写for循环，游戏排行榜、分页序号一行搞定。
```javascript
const range = Array.from({length: 5}, (_, i) => i);
// [0, 1, 2, 3, 4]
```

## 22. Object.keys/values：安全遍历对象
只遍历自身属性，跳过原型链，比for...in更纯粹，配合数组方法更顺手。
```javascript
Object.keys({a: 1, b: 2});
// ['a', 'b']
```

## 23. Object.assign：被低估的浅拷贝
合并状态、克隆配置时比展开运算符更早出现，兼容老浏览器也能用。
```javascript
Object.assign({}, {a: 1}, {b: 2});
// { a: 1, b: 2 }
```

## 24. ??= 批量兜底
只在null/undefined时赋值，已有值零侵入，配置对象默认值更安心。
```javascript
cfg.timeout ??= 5000;
// 不覆盖旧值
```

## 25. Intl.RelativeTimeFormat：相对时间"几分钟前"
内置语言规则，中文自动输出"分钟前""小时后"，社交 Feed 时间戳神器。
```javascript
new Intl.RelativeTimeFormat('zh').format(-3, 'minute');
// "3 分钟前"
```

## 26. Intl.PluralRules：自动单复数
英文、法语等plural规则一键判断，告别if/else硬编码，多语言文案更地道。
```javascript
['zero', 'one', 'other'][new Intl.PluralRules('en').select(1)];
// 'one'
```

## 27. Intl.Segmenter：原生中文分词
按字、词、句分割，搜索高亮、文本统计场景直接可用，无需再引NLP库。
```javascript
[...new Intl.Segmenter('zh').segment('JavaScript好玩')].map(i => i.segment);
// ['JavaScript', '好玩']
```

## 28. BigInt：任意精度整数
超过Number安全范围的ID、订单号都能精确计算，金融、区块链场景必备。
```javascript
9007199254740993n + 1n;
// 9007199254740994n
```

## 29. Math.sign / clamp / hypot：简易数学工具
符号、取整、斜边长度一次调用，UI动画、游戏碰撞检测少写很多工具函数。
```javascript
Math.sign(-5);
// -1
```

## 30. queueMicrotask：比 Promise 更快插微任务
同步代码后立即执行，比setTimeout 0更精准，适合框架内部批量DOM更新。
```javascript
queueMicrotask(() => updateDOM());
// 同步后立刻更新
```

## 31. ResizeObserver：精准监听元素尺寸
比window.resize更细粒度，图表自适应、虚拟滚动容器高度变化时即时重绘。
```javascript
new ResizeObserver(([e]) => chart.resize(e.contentRect.width)).observe(chartDom);
// 尺寸变化时触发
```

## 32. element.matches：选择器匹配
事件委托里判断点击来源，比tagName/classList逐层判断更可靠，一行搞定。
```javascript
e.target.matches('.del-btn');
// true / false
```

## 33. element.toggleAttribute：布尔属性开关
disabled、checked、selected一键切换，省去if/else-setAttribute冗余代码。
```javascript
btn.toggleAttribute('disabled');
// 状态切换
```

## 34. scrollIntoView：平滑滚动返回顶部
无需额外引滚动库，行为平滑，移动端也能保持60帧，用户体验更丝滑。
```javascript
document.querySelector('#top').scrollIntoView({behavior: 'smooth'});
// 平滑滚动到顶部
```

## 35. element.animate：关键帧动画
Web Animations API原生支持，简单淡入淡出、缩放可直接写，减少GSAP体积。
```javascript
el.animate([{opacity: 0}, {opacity: 1}], {duration: 300});
// 淡入动画
```

## 36. CSS.supports：运行时检测兼容性
做渐进增强再也不用引Modernizr，一行判断即可决定是否加载兜底样式或脚本。
```javascript
CSS.supports('aspect-ratio', '1');
// true / false
```

## 37. ReadableStream：边下载边渲染
大文件分片读取，内存不会暴涨，视频预览、日志流实时输出都能用原生解决。
```javascript
const reader = response.body.getReader();
// 分块读取
```

## 38. BackgroundFetch：PWA 断网续传
下载任务切到后台仍继续，断网恢复自动重连，课程视频提前缓存不占用主线程。
```javascript
await registration.backgroundFetch.fetch('vid', ['/course.mp4']);
// 后台下载
```

## 39. CacheStorage：可控缓存
比localStorage更大更专业，图片、脚本、JSON都能存，离线应用首选存储层。
```javascript
const cache = await caches.open('v1');
await cache.add('/api/config');
// 缓存成功
```

## 40. navigator.storage.estimate：剩余空间心中有数
提前告知用户磁盘余量，避免写入失败，大型游戏、离线包下载场景体验更友好。
```javascript
const { usage, quota } = await navigator.storage.estimate();
// 已用 / 总量
```

## 41. navigator.onLine + 事件：网络状态一行搞定
弱网提示、离线缓存切换、请求重试策略都能基于它实时响应，无需再引三方探测库。
```javascript
addEventListener('offline', () => toast('网络已断开'));
// 离线提示
```

## 42. ReportingObserver：捕获浏览器警告
deprecated API、CSP违规自动上报，提前发现潜在兼容问题，运维监控零成本接入。
```javascript
new ReportingObserver(reports => reports.forEach(r => sendToMonitor(r))).observe();
// 上报警告
```

## 43. WebCodecs：硬件级音视频解码
4K 60帧流畅播放，CPU占用直降，在线剪辑、云游戏场景性能飞跃，替代FFmpeg.wasm。
```javascript
const decoder = new VideoDecoder({output: f => ctx.drawImage(f, 0, 0), error: console.error });
decoder.configure({codec: 'vp09.00.10.08'});
// 硬解开始
```

## 44. Clipboard API：异步读写剪贴板
无需execCommand，支持Promise，复制链接、邀请码一步到位，HTTPS下安全又简单。
```javascript
await navigator.clipboard.writeText('邀请码 9527');
// 写入成功
```

## 45. Promise.allSettled：批量请求不"一损俱损"
即使部分接口失败，也能拿到成功结果，前端容错更从容，比Promise.all实用得多。
```javascript
const res = await Promise.allSettled([fetchA(), fetchB()]);
const ok = res.filter(r => r.status === 'fulfilled');
// 成功数组
```

## 46. for...of：能 break 的遍历
比forEach灵活，支持continue / break，遍历NodeList、Set、Map一视同仁，代码更清晰。
```javascript
for (const n of nums) { if (n > 10) break; }
// 命中即退出
```

## 47. URLSearchParams 直接转对象
```javascript
const params = Object.fromEntries(new URLSearchParams(location.search));
// { page: '2' }
```

## 48. console.table：表格调试神器
数组对象一键成表，字段对齐、排序自带，定位数据异常比log更直观，调试效率翻倍。
```javascript
console.table(await fetch('/api/users').then(r => r.json()));
// 表格展示
```

## 49. debugger：源码级断点
比console.log更精准，可逐行查看调用栈，配合sourcemap直击问题，调试时少打无数日志。
```javascript
function debug() { debugger; }
// 命中断点
```

## 50. ??= 全局兜底配置
批量赋默认值时只补空位，已有属性零侵入，项目初始化、合并用户配置时安全又简洁。
```javascript
config.timeout ??= 5000;
config.retries ??= 3;
// 无覆盖
