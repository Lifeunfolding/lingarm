# lingarm

**LingArm 项目门户** —— 低成本视觉语言动作（VLA）机械臂系统的官方展示站点。

> 让 VLA 走出大算力实验室：极低成本硬件（¥2000 机械臂 + ¥1500 边缘芯片）上的全链路 VLA 实操。

## 站点内容

| 页面 | 说明 |
|------|------|
| `index.html` | 门户主页（入口）：LingArm 定位与初心、项目历程、一体 VLA vs 多模块方案对比、多线算力（Jetson / RTX / K3） |
| `lingarm_portal.html` | 门户主页（别名，与 index.html 内容一致） |
| `pages/model.html` | FrozenVLA-2B 模型卡（HuggingFace `LingArm/LingArm_frozen_preview`） |
| `pages/train.html` | `train/` baseline 训练代码详情 |
| `pages/train_1.1.html` | `train_1.1/` 双路线（Route A 离散化+LoRA / Route B 扩散策略）详情 |
| `pages/project.html` | 方案文档与 2026 追赶规划报告 |
| `DEPLOY.md` | 弹性云 / 静态托管部署指南 |

## 本地预览

```bash
cd "G:/LingArm blueprint/web"
python -m http.server 8080
# 浏览器打开 http://localhost:8080/（或 /index.html）
```

## 部署

纯静态站点，可托管于 GitHub Pages / Vercel / Netlify / 任意弹性云（详见 `DEPLOY.md`）。

## 相关链接

- 🤗 HuggingFace 模型：`https://huggingface.co/LingArm/LingArm_frozen_preview`
