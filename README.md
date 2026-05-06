# 家庭菜谱规划工具

一款面向中国家庭的智能菜谱规划工具，支持智能生成和手动输入两种模式，帮助用户轻松规划每周饮食、生成采购清单、控制家庭餐饮预算。

## 功能特点

- **双模式输入**：智能生成 + 手动自定义
- **精细化偏好**：荤素数量、肉类偏好、菜系选择、口味禁忌
- **智能拆分**：菜单→采购清单自动分解
- **每日/每周切换**：灵活查看采购清单
- **导出打印**：支持导出可打印的采购清单Word版本

## 技术栈

- HTML5 + Tailwind CSS
- Vanilla JavaScript
- 纯前端，无需后端

## 快速开始

```bash
# 克隆仓库
git clone https://github.com/你的用户名/meal-planner.git

# 进入目录
cd meal-planner

# 本地预览（需要安装 http-server）
npx http-server -p 8080
```

然后访问 http://localhost:8080

## 部署方式

### 方式1：GitHub Pages（免费）

1. 将代码推送到GitHub仓库
2. 进入仓库 Settings → Pages
3. Source 选择 `main` 分支和 `/(root)` 目录
4. 访问 `https://你的用户名.github.io/meal-planner`

### 方式2：阿里云服务器 + Nginx

参考 `DEPLOY.md` 获取详细部署指南。

## 目录结构

```
meal-planner/
├── index.html              # 原型预览入口
├── pages/                  # 页面文件
│   ├── home.html          # 首页
│   ├── smart-plan.html    # 智能规划
│   ├── manual-add.html    # 手动添加
│   ├── shop-list.html     # 采购清单
│   ├── preference.html     # 口味偏好
│   └── recipe-detail.html  # 菜谱详情
├── docs/                  # 文档
│   └── prd.md            # 产品需求文档
└── README.md
```

## 许可证

MIT License
