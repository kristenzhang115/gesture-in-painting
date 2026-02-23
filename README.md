# Vibe Hand Art — 完整项目指南

## 📁 项目文件结构

```
/Users/zyx/Desktop/test/
├── vibe-data-extractor.html    # 阶段 1：数据提取工具
├── vibe-main-app.html          # 阶段 2-3：主应用（自动加载 data.json）
├── data.json                    # 古典绘画手势数据库
├── images/                      # 古典绘画图片文件夹
│   ├── rembrandt-anatomy.jpg
│   ├── michelangelo-creation.jpg
│   └── ...（其他图片）
└── README.md                    # 本文件
```

## 🚀 快速开始

### 步骤 1：准备古典绘画数据

1. **使用数据提取工具**：
   - 打开 `vibe-data-extractor.html`
   - 上传古典绘画图片（例如 Rembrandt 的作品）
   - 点击"检测关键点"让 MediaPipe 识别手部轮廓
   - 填入作品名称（例如 "Rembrandt - The Anatomy Lesson"）
   - 点击"导出 data.json"下载数据

2. **收集图片**：
   - 在项目根目录创建 `images/` 文件夹
   - 将古典绘画高清图片放入该文件夹
   - 记住文件名（将在 data.json 中引用）

### 步骤 2：整理 data.json

编辑 `data.json`，格式如下：

```json
[
  {
    "artwork": "rembrandt-anatomy.jpg",
    "landmarks": [
      { "x": 0.0, "y": 0.0, "z": 0.0 },
      ...（21 个关键点）
    ]
  },
  {
    "artwork": "michelangelo-creation.jpg",
    "landmarks": [ ... ]
  }
]
```

**重要**：
- `artwork` 字段是图片文件名（不需要路径，系统自动添加 `./images/` 前缀）
- 如果图片是完整 URL（如 `https://...`），也支持直接使用
- `landmarks` 数组必须有 21 个点（MediaPipe 标准）
- 每个点包含 `x, y, z`（归一化坐标）

### 步骤 3：启动主应用

1. 在浏览器中打开 `vibe-main-app.html`
2. **允许摄像头权限**（浏览器会提示）
3. 应该看到：
   - ✓ 加载消息："已加载 N 条记录"
   - 实时摄像头画面（镜像模式）
   - 绿色连线 + 粉红发光点显示实时手部检测

### 步骤 4：体验匹配

做出手势：
- 摄像头实时追踪你的手部 21 个关键点
- 系统自动与 data.json 中的古典绘画手势比对
- 当手势相似度 > 80% 且持续 1 秒，古典绘画淡入显示
- 改变手势或移开手 → 图像淡出

## 🎨 文件说明

### vibe-data-extractor.html
**用途**：从古典绘画图片提取手部关键点

**功能**：
- 上传图片
- MediaPipe 自动检测 21 个手部关键点
- 可视化确认关键点位置
- 导出归一化的 JSON 数据

**导出格式**：
```json
{
  "artwork": "artwork_name",
  "sourceFile": "original_filename",
  "imageSize": { "width": 1280, "height": 800 },
  "detectedHands": [
    {
      "handedness": "Right",
      "landmarks": [ { "x": ..., "y": ..., "z": ... }, ... ]
    }
  ],
  "meta": { "generator": "Vibe Hand Art — Data Extractor", "version": "1.0" }
}
```

### vibe-main-app.html
**用途**：实时手势识别和古典绘画匹配

**功能**：
- 自动加载 `data.json`（无需手动上传）
- 实时摄像头手部追踪
- 余弦相似度匹配算法
- 1 秒持续确认机制
- 平滑淡入淡出效果显示匹配的艺术作品

**UI 控制**：
- **镜像开/关**：切换摄像头镜像模式
- **FPS 显示**：实时性能监控
- **状态信息**：加载状态和匹配进度
- **匹配信息**：正在匹配的作品和相似度百分比
- **艺术信息卡**：显示匹配成功的作品名和相似度

## 🔧 自定义配置

### 调整相似度阈值

编辑 `vibe-main-app.html` 第 308 行：
```javascript
const SIMILARITY_THRESHOLD = 0.8; // 改为 0.75（75% 更容易匹配）或 0.85（85% 更严格）
```

### 调整持续时间

编辑第 307 行：
```javascript
let matchHoldDuration = 1000; // 改为 500 表示 0.5 秒，2000 表示 2 秒
```

### 调整图片透明度

编辑 `vibe-main-app.html` 第 44 行的 CSS：
```css
#artworkLayer.active {
  opacity: 0.85; /* 改为 0.5（半透明）或 1.0（完全不透明） */
}
```

## 🐛 故障排除

| 问题 | 解决方案 |
|------|--------|
| 加载失败："data.json 找不到" | 确保 `data.json` 与 `vibe-main-app.html` 在同文件夹 |
| 图片不显示 | 检查 `images/` 文件夹是否存在，文件名是否正确（区分大小写） |
| 摄像头黑屏 | 检查浏览器权限设置，或尝试 HTTPS 协议 |
| 手势检测不稳定 | 确保光线充足，手在摄像头全景内 |
| 匹配太敏感 | 增加 `SIMILARITY_THRESHOLD` 值 |

## 📚 技术栈

- **MediaPipe Hands**：实时手部检测（21 个关键点）
- **Canvas API**：实时视频渲染和绘制
- **Cosine Similarity**：归一化手势匹配算法
- **CSS Animation**：平滑的淡入淡出效果

## 🎯 下一步扩展

- [ ] 添加音效反馈（匹配成功时播放音乐）
- [ ] 支持多手同时匹配
- [ ] 录制和分享匹配视频
- [ ] Web 端实时数据库（云存储古典绘画库）
- [ ] AR 效果叠加
- [ ] 手势识别精度改进（Procrustes 分析）

---

**创建日期**：2026 年 2 月 23 日  
**项目**：Vibe Hand Art - 交互式艺术装置
