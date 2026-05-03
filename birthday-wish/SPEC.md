# 生日祝福网站 - SPEC.md

## 1. Concept & Vision

一个充满《你的名字》电影美学的生日祝福网站。开场以流星雨动画拉开序幕，配合日落黄昏的唯美背景，营造浪漫梦幻的氛围。流星带着文字碎片缓缓坠落，最终汇聚成一句温暖的生日祝福。整体风格追求新海诚式的细腻光影与浪漫诗意，让每位访客都能感受到独特的视觉体验与真挚祝福。

## 2. Design Language

### 2.1 Aesthetic Direction
- 参考：新海诚《你的名字》《天气之子》电影画风
- 特点：细腻光影、唯美色调、梦幻云彩、真实感与幻想交织

### 2.2 Color Palette
```
Primary:     #FF6B9D (玫瑰粉 - 温暖生日氛围)
Secondary:   #C44569 (深玫红 - 强调色)
Accent:      #FFD93D (金黄 - 流星光芒)
Background:  #1A1A2E (深蓝夜空)
Sky Gradient: #FF7E5F → #FEB47B → #FFCF9F → #87CEEB (黄昏到夜空)
Cloud:       rgba(255, 255, 255, 0.6) (半透明白云)
Mountain:    #2D3A4A (深蓝灰山脉)
Text:        #FFFFFF (纯白)
Text Glow:   #FFD700 (金色光晕)
```

### 2.3 Typography
- 主标题：`Noto Serif SC`, serif - 优雅庄重
- 正文/祝福语：`Noto Sans SC`, sans-serif - 清晰易读
- Fallback: system-ui, sans-serif

### 2.4 Spatial System
- 流星文字间距：动态计算，确保不重叠
- 祝福语间距：16px - 24px
- 容器最大宽度：800px
- 响应式断点：768px

### 2.5 Motion Philosophy
- 流星：拖尾效果 + 柔和光晕，模拟真实流星
- 文字：逐个弹出，fade-in + translateY，timing stagger 300ms
- 云朵：缓慢飘动，营造宁静感
- 按钮：hover时微微放大 + 光晕增强
- 祝福语切换：fade transition 500ms

## 3. Layout & Structure

### 3.1 Page Flow
```
[阶段1: 流星动画]
  └→ Canvas 全屏流星 + 背景
  └→ 文字碎片跟随流星坠落

[阶段2: 祝福呈现]
  └→ 背景静止（云/山不动）
  └→ 流星继续飞舞
  └→ 中央大字祝福语 + 闪烁星光效果

[阶段3: 交互表单]
  └→ 淡入输入框："祝 ___ 生日快乐"
  └→ 确认按钮

[阶段4: 祝福语展示]
  └→ 随机祝福语卡片
  └→ 刷新按钮
```

### 3.2 Background Layers (从后到前)
1. 渐变天空（黄昏到夜空）
2. 远山剪影
3. 中景云层
4. 近景云层
5. 流星画布
6. 文字层

### 3.3 Responsive Strategy
- 移动端：文字尺寸缩小30%，云朵数量减少
- 流星数量：桌面60+，移动端30+

## 4. Features & Interactions

### 4.1 流星动画
- Canvas渲染，60fps流畅动画
- 流星随机大小（2-6px），随机速度
- 流星头部带有圆形光晕
- 流星尾部渐变透明拖尾
- 偶尔出现大型流星（你的名字经典场景）

### 4.2 文字弹出效果
- 默认显示文字：「愿这漫天星辰，化作最温柔的祝福，点亮你生命中每一个美好的瞬间」
- 文字作为"光点"跟随流星轨迹
- 到达底部后文字片段fade-in并定位
- 全部文字出现后汇聚成完整句子

### 4.3 输入表单
- Placeholder: "请输入姓名"
- 最大长度：20字符
- 回车或点击按钮提交
- 提交后淡出，显示祝福语

### 4.4 随机祝福语
预设祝福语库（20+条）：
- "生日快乐！愿你的每一天都如星辰般闪耀"
- "在这特别的日子里，愿幸福与你形影不离"
- "愿你被世界温柔以待，生日快乐！"
- ...（更多祝福语）
- 刷新按钮：随机切换一条新祝福
- 切换动画：fade out → fade in

### 4.5 边界情况
- 未输入姓名点击确认：抖动提示 + "请输入姓名"
- 输入为空格：同样提示
- 祝福语重复：确保连续两次不重复

## 5. Component Inventory

### 5.1 MeteorCanvas
- 全屏Canvas，position: fixed
- z-index: 1
- 流星对象池，避免GC

### 5.2 Background
- CSS渐变 + SVG/CSS绘制云和山
- z-index: 0

### 5.3 TextReveal
- 文字容器：flex居中
- 单字span，带fade-in动画
- z-index: 10

### 5.4 InputForm
- 玻璃拟态卡片
- 半透明背景 + backdrop-blur
- 输入框 + 按钮
- 状态：default, focus, error, submitted

### 5.5 BlessingCard
- 祝福语展示区
- 刷新按钮（旋转动画）
- 祝福语淡入淡出

### 5.6 Button
- 圆角pill形状
- 渐变背景
- hover: scale(1.05) + 光晕
- active: scale(0.98)
- disabled: opacity 0.5

## 6. Technical Approach

### 6.1 Stack
- 纯HTML5 + CSS3 + Vanilla JavaScript
- 无框架依赖，轻量化
- 单HTML文件，内联CSS和JS

### 6.2 Canvas Meteor System
```javascript
class Meteor {
  x, y, length, speed, opacity
  trail[] // 拖尾坐标点
  charIndex // 对应文字索引
  hasLanded // 是否已落地
}
```
- requestAnimationFrame驱动
- 流星数量动态管理

### 6.3 Animation Timing
- 页面加载：流星开始
- 1秒后：开始文字跟随
- 5秒后：文字汇聚完成
- 6秒后：表单淡入
- 表单提交后：祝福语展示

### 6.4 Performance
- Canvas离屏优化
- CSS transform替代top/left
- will-change提示
- 移动端降级粒子数量
