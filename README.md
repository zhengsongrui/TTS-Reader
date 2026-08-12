# TtsReader

基于 **indexTTS** 的跨端小说阅读器，提供小说文本的语音合成朗读能力。

## 项目简介

TtsReader 是一款使用 [uni-app x](https://uniapp.dcloud.net.cn/)（Vue3 + UTS）开发的小说阅读器应用。应用接入 indexTTS 语音合成服务，可将小说文本转换为自然流畅的语音进行朗读，支持在 App、小程序等多端运行。

## 页面结构

应用仅包含两个页面：

| 页面 | 路径 | 说明 |
| --- | --- | --- |
| 首页 | `pages/index/index` | 应用启动页，展示阅读器入口 |
| 阅读页 | `pages/reader/reader` | 小说内容阅读与 indexTTS 语音朗读页面 |

## 技术栈

- [uni-app x](https://uniapp.dcloud.net.cn/)：跨端应用框架（Vue3 + UTS）
- indexTTS：语音合成（TTS）引擎
- SCSS：页面样式

## 目录结构

```
TtsReader
├── App.uvue            # 应用入口组件
├── main.uts            # 应用初始化
├── manifest.json       # 应用配置
├── pages.json          # 页面路由配置
├── uni.scss            # 全局 SCSS 变量
├── static/             # 静态资源
└── pages/
    ├── index/          # 首页
    │   └── index.uvue
    └── reader/         # 阅读页
        └── reader.uvue
```

## 运行

使用 HBuilderX 打开项目，选择运行目标平台（App / 小程序 / Web）即可预览。
