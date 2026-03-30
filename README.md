# Vue3 + Element Plus 动画登录页

这是一个使用 Vue3 + Element Plus 复刻 [katavii/animated-login](https://github.com/katavii/animated-login) 项目的眼睛转动特效登录页面。

## 项目特点

- 🎭 **可爱的卡通人物**：四个不同颜色和高度的卡通人物（紫色、黑色、橙色、黄色）
- 👀 **眼睛跟随鼠标**：眼睛瞳孔会实时跟随鼠标位置转动
- 👁️ **眨眼动画**：自动随机眨眼效果
- 🔐 **密码输入特效**：
  - 输入用户名时，人物会互相"对视"
  - 输入密码时，人物会"偷看"
  - 显示密码时，所有人物都会看向密码框

## 核心实现细节

### 1. 使用 requestAnimationFrame 实现高性能动画

```typescript
const tick = () => {
  // 计算鼠标位置与眼睛中心的角度和距离
  // 更新所有瞳孔位置
  updatePupils()
  // 继续下一帧
  rafId = requestAnimationFrame(tick)
}
```

眼睛转动效果使用 `requestAnimationFrame` 实现 60fps 流畅动画，通过计算鼠标位置与眼睛元素中心点的角度和距离，得出瞳孔应该移动的方向和距离。

### 2. 使用 GSAP 的 quickTo 实现平滑过渡

```typescript
quickTo = {
  purpleSkew: gsap.quickTo(purpleSkew, 'value', { duration: 0.3, ease: 'power2.out' }),
  // ... 其他动画
}
```

GSAP 的 `quickTo` 方法提供了高性能的数值过渡动画，避免了每帧手动计算插值，同时保持了平滑的缓动效果。

### 3. 数学计算：角度和距离

```typescript
const calcEyePos = (el: HTMLElement, maxDist: number) => {
  const r = el.getBoundingClientRect()
  const cx = r.left + r.width / 2  // 眼睛中心X
  const cy = r.top + r.height / 2  // 眼睛中心Y
  const dx = mouseRef.x - cx       // 鼠标与中心的X距离
  const dy = mouseRef.y - cy       // 鼠标与中心的Y距离
  const dist = Math.min(Math.sqrt(dx ** 2 + dy ** 2), maxDist)  // 限制最大距离
  const angle = Math.atan2(dy, dx) // 计算角度
  return {
    x: Math.cos(angle) * dist,     // 瞳孔X偏移
    y: Math.sin(angle) * dist      // 瞳孔Y偏移
  }
}
```

通过三角函数计算瞳孔移动位置，使用 `Math.atan2` 获取角度，然后用 `Math.cos` 和 `Math.sin` 分解到 X/Y 方向，最后限制最大移动距离确保瞳孔不会移出眼球。

## 项目结构

```
animated-login-vue/
├── src/
│   ├── components/
│   │   └── AnimatedCharacters/
│   │       ├── index.vue      # 主要动画组件
│   │       ├── Pupil.vue      # 瞳孔组件（纯色圆点）
│   │       └── EyeBall.vue    # 眼球组件（白底+瞳孔）
│   ├── views/
│   │   └── Login.vue          # 登录页面
│   ├── App.vue                # 根组件
│   ├── main.ts                # 入口文件
│   └── style.css              # 全局样式
├── package.json
├── tsconfig.json
└── vite.config.ts
```

## 快速开始

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build
```

## 技术栈

- **Vue 3** - 渐进式 JavaScript 框架
- **Element Plus** - Vue 3 UI 组件库
- **GSAP** - 专业级 JavaScript 动画库
- **TypeScript** - 类型安全的 JavaScript 超集
- **Vite** - 下一代前端构建工具

## 效果预览

1. 眼睛实时跟随鼠标移动
2. 人物身体随鼠标位置倾斜
3. 随机自动眨眼
4. 输入用户名时人物互相看
5. 输入密码时人物偷看
6. 显示密码时所有人物看向密码框
