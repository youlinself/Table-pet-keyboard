# 桌面宠物键盘应用

一个基于 Electron + TypeScript + Less 的桌面宠物应用，能够监听键盘和鼠标事件，宠物会模仿用户的输入行为。

## 功能特性

- 🎮 **键盘事件监听**：宠物会模仿用户按下的键盘按键
- 🖱️ **鼠标移动响应**：宠物会跟随鼠标移动并做出相应动作
- 🎨 **Canvas 动画**：使用 Canvas 实现流畅的宠物动画效果
- 🐾 **拖拽功能**：支持拖拽移动宠物位置
- 👕 **换肤支持**：可切换不同的宠物皮肤
- 🎯 **全局快捷键**：Ctrl+Shift+P 切换宠物显示/隐藏
- 💫 **透明窗口**：无边框透明设计，不影响其他应用使用

## 技术栈

- **Electron** - 跨平台桌面应用框架
- **TypeScript** - 类型安全的 JavaScript 超集
- **Less** - CSS 预处理器
- **Canvas API** - 2D 图形渲染

## 项目结构

```
src/
├── main.ts              # Electron 主进程
├── preload.ts           # 预加载脚本
├── types/
│   └── index.ts         # 类型定义
└── renderer/
    ├── index.html       # 主页面
    ├── main.ts          # 渲染进程入口
    ├── styles/
    │   └── main.less    # 样式文件
    ├── assets/
    │   └── pet-sprite.png # 宠物精灵图
    └── core/
        ├── App.ts       # 应用主类
        ├── Pet.ts       # 宠物核心类
        └── EventManager.ts # 事件管理器
```

## 安装和运行

### 1. 安装依赖
```bash
npm install
```

### 2. 开发模式
```bash
npm run dev
```

### 3. 构建应用
```bash
npm run build
```

### 4. 打包分发
```bash
npm run dist
```

## 使用说明

1. **启动应用**：运行应用后，桌面会出现一个宠物窗口
2. **键盘交互**：按下任意键，宠物会做出按键动画
3. **鼠标交互**：移动鼠标，宠物会跟随移动
4. **拖拽移动**：点击并拖拽宠物可以移动位置
5. **隐藏/显示**：使用 Ctrl+Shift+P 快捷键切换显示
6. **换肤设置**：点击设置按钮可以更换宠物皮肤

## 核心实现

### 键盘事件监听
```typescript
// 监听键盘按下事件
window.addEventListener('keydown', (event) => {
  const eventData: KeyboardEventData = {
    key: event.key,
    code: event.code,
    shiftKey: event.shiftKey,
    ctrlKey: event.ctrlKey,
    altKey: event.altKey,
    metaKey: event.metaKey,
    timestamp: Date.now()
  }
  
  // 传递给宠物处理
  pet.handleKeyboardEvent(eventData)
})
```

### Canvas 动画系统
```typescript
// 动画循环
private startAnimationLoop(): void {
  const animate = (timestamp: number) => {
    // 更新动画帧
    this.frameTimer += timestamp
    
    if (this.frameTimer >= frame.duration) {
      this.frameTimer = 0
      this.currentFrame++
      
      // 循环或单次播放处理
      if (this.currentFrame >= animation.frames.length) {
        if (animation.loop) {
          this.currentFrame = 0
        } else {
          this.currentFrame = animation.frames.length - 1
          this.setState({ currentAnimation: 'idle' })
        }
      }
    }
    
    this.render()
    requestAnimationFrame(animate)
  }
}
```

### 拖拽功能实现
```typescript
// 开始拖拽
public startDrag(): void {
  this.setState({ 
    isDragging: true,
    currentAnimation: 'drag'
  })
}

// 结束拖拽
public endDrag(): void {
  this.setState({ 
    isDragging: false,
    currentAnimation: 'idle'
  })
}
```

## 边界情况处理

### 1. 重复按键处理
```typescript
private keyState: Map<string, boolean> = new Map()

private handleKeyDown = (event: KeyboardEvent): void => {
  // 防止重复触发
  if (this.keyState.get(event.code)) return
  this.keyState.set(event.code, true)
  // ... 处理按键事件
}
```

### 2. 动画状态管理
- 确保动画播放完成后正确回到 idle 状态
- 处理动画中断和状态切换

### 3. 性能优化
- 使用 requestAnimationFrame 实现平滑动画
- 避免不必要的重绘
- 资源懒加载

### 4. 错误处理
- 全局错误捕获
- 图片加载失败处理
- 动画资源缺失处理

## 自定义扩展

### 添加新动画
1. 在 `Pet.ts` 的 `initAnimations()` 方法中添加新动画配置
2. 准备对应的精灵图资源
3. 在事件处理中触发新动画

### 添加新皮肤
1. 准备新的精灵图文件
2. 在设置面板中添加皮肤选项
3. 实现皮肤切换逻辑

## 开发注意事项

1. **跨进程通信**：使用 `ipcMain` 和 `ipcRenderer` 进行主进程和渲染进程通信
2. **安全考虑**：启用 `contextIsolation` 和禁用 `nodeIntegration`
3. **性能监控**：注意内存泄漏和CPU使用率
4. **用户体验**：确保动画流畅，响应及时

## 许可证

MIT License