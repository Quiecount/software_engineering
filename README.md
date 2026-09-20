# 宿舍报修与进度追踪系统

> 校园宿舍报修平台，面向学生、维修人员、管理员三类用户，实现报修提交、工单派发、维修进度追踪与数据统计的全流程闭环管理。

## 技术栈

| 层级 | 技术 |
|------|------|
| 前端 | Vue 3 + Vue Router + Pinia + Element Plus + ECharts |
| 后端 | Python 3 + Flask + Flask-SQLAlchemy |
| 数据库 | MySQL 8.0 |
| 认证 | PyJWT + bcrypt |
| 部署 | Nginx + Gunicorn |

## 系统架构

```
┌─────────────┐     HTTP/HTTPS     ┌─────────────┐     SQL     ┌─────────┐
│   Vue 前端   │ ──────────────────→ │  Flask 后端  │ ──────────→ │  MySQL  │
│  (端口 5173)  │ ←────────────────── │  (端口 5000)  │ ←────────── │ (3306)  │
└─────────────┘     JSON/REST       └─────────────┘            └─────────┘
```

## 功能模块

### 学生端

- 一键报修提交（类型、楼栋房间、描述、图片上传、预约时段）
- 工单列表与状态筛选（待处理/处理中/已完成）
- 工单详情与状态时间线追踪
- 维修完成后评价（速度评分 + 质量评分 + 文字评价）
- 个人中心与报修历史统计

### 维修人员端

- 工单工作台（待接单 / 进行中 / 已完成三栏）
- 接单 / 拒单操作
- 维修进度更新（方案填写、维修结果、材料记录、照片上传）
- 工作量统计（本月接单数、完成率、平均评分）

### 管理端

- 用户与权限管理（启用 / 禁用、工种与区域分配）
- 工单调度中心（工单池、手动派单、转派）
- 公告管理（发布 / 编辑 / 删除，支持有效期）
- 数据统计看板（报修类型饼图、每日趋势折线图、楼栋排行柱状图、维修效率排行）
- Excel 报表导出

## 项目目录结构

```
backend/
├── app/
│   ├── __init__.py          # Flask 应用工厂
│   ├── config.py            # 配置文件
│   ├── models/              # 数据模型（6张表）
│   ├── blueprints/          # 路由蓝图（auth / orders / admin / announcements）
│   ├── utils/               # 工具函数（JWT / 响应格式 / 文件上传 / 加密）
│   └── static/              # 上传文件存储
├── migrations/              # 数据库迁移
├── requirements.txt
└── run.py

frontend/
├── src/
│   ├── api/                 # API 请求封装
│   ├── components/          # 公共组件（OrderCard / StatusTimeline / RatingDialog）
│   ├── layouts/             # 布局组件（Student / Worker / Admin）
│   ├── router/              # 路由配置与权限守卫
│   ├── stores/              # Pinia 状态管理
│   ├── views/
│   │   ├── student/         # 学生端页面
│   │   ├── worker/          # 维修人员端页面
│   │   └── admin/           # 管理端页面
│   ├── App.vue
│   └── main.js
├── package.json
└── vite.config.js

database/
├── schema.sql               # 建表 SQL
├── init_data.py             # 测试数据初始化脚本
└── migrate.sql              # 版本迁移脚本

docs/                        # 项目文档
deploy/                      # 部署配置（Nginx / Gunicorn / systemd）
tests/                       # 测试相关（Postman 集合 / pytest）
```

## 数据库设计

共 6 张表：

| 表名 | 说明 |
|------|------|
| `user` | 用户表（学生 / 维修人员 / 管理员） |
| `repair_order` | 工单表（状态：submitted → assigned → repairing → completed → rated） |
| `order_image` | 工单图片表（报修图 / 维修后图） |
| `order_progress` | 工单进度记录表 |
| `rating` | 评价表（速度评分 + 质量评分 + 文字评价） |
| `announcement` | 公告表 |

## API 概览

| 模块 | 主要接口 |
|------|---------|
| 认证 | 注册、登录、获取/更新个人信息 |
| 工单 | 提交报修、列表查询、详情、派单、接单、拒单、进度更新、完成、评价、图片上传 |
| 管理 | 用户管理、工单调度、统计报表、Excel 导出 |
| 公告 | 公告列表、发布、编辑、删除 |

## 快速开始

### 环境要求

- Python 3.8+
- Node.js 16+
- MySQL 8.0+

### 后端启动

```bash
cd backend
pip install -r requirements.txt
# 修改 app/config.py 中的数据库连接配置
python run.py
```

### 前端启动

```bash
cd frontend
npm install
npm run dev
```

### 数据库初始化

```bash
# 执行建表脚本
mysql -u root -p < database/schema.sql
# 插入测试数据
python database/init_data.py
```

## 团队分工

| 成员 | 角色 | 核心职责 |
|------|------|----------|
| A | 项目统筹 / 产品经理 | 需求分析、数据库设计、API 文档、项目管理、部署运维、文档撰写 |
| B | 后端开发 | Flask 后端全部开发（32 个接口）、数据模型、JWT 鉴权、文件上传 |
| C | 前端开发 | Vue 前端全部开发（三端全部页面与组件）、接口联调 |
| D | 测试工程师 | 测试用例设计、接口测试、功能测试、自动化测试、性能测试、验收报告 |

## 开发里程碑

| 阶段 | 时间 | 目标 |
|------|------|------|
| 第 1 周 | 第 1-7 天 | 需求确认、数据库建表、后端搭建与核心 API、前端基础建设 |
| 第 2 周 | 第 8-14 天 | 后端全部 API 完成、前端学生端与维修端页面开发、接口联调 |
| 第 3 周 | 第 15-21 天 | 前端管理端页面开发、ECharts 图表、全流程联调、接口测试 |
| 第 4 周 | 第 22-28 天 | Bug 修复、性能测试、回归测试、文档编写、部署上线 |

## 非功能性需求

- 页面加载 ≤ 2s，API 响应 ≤ 500ms
- 支持 200 用户同时在线
- 密码 bcrypt 加密存储，接口 JWT Token 鉴权
- 兼容 Chrome / Firefox / Edge，适配 PC 与移动端
