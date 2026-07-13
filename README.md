# gym_managment_system 健身房管理系统
前后端分离健身房综合管理平台，分为管理员、会员双角色，覆盖会员/员工/器材/课程管理、课程预约、在线聊天完整业务。

## 技术栈
### 前端 frontend
- Vue3 + TypeScript + Vite
- Vue Router 路由管理
- 原生页面样式

### 后端 gym-management-system
- SpringBoot 2.5.3
- MyBatis 持久层框架
- MySQL 8.0
- Lombok 简化实体类
- Maven 项目构建

## 项目目录
```
gym_managment_system
├── frontend                 # Vue3 前端工程
├── gym-management-system    # SpringBoot 后端服务
├── gym_management_system.sql# MySQL 数据库初始化脚本
├── .gitignore               # Git忽略文件配置
└── README.md                # 项目总文档
```

## 系统功能
### 管理员端
1. 会员管理：会员信息新增、编辑、删除、课时管理
2. 员工管理：健身房员工信息CRUD
3. 器材管理：健身器材录入、状态维护
4. 课程管理：创建/编辑课程，配置教练、课时、时间
5. 选课订单：查看全部会员课程报名记录

### 会员端
1. 个人中心：查看、修改个人信息，查看剩余课时
2. 课程选课：浏览全部课程，在线报名
3. 我的课程：查看已报名课程，支持自主退课
4. 在线聊天：会员与后台简易对话功能

## 部署运行教程
### 1. 数据库初始化
1. 本地安装 MySQL 8.0，执行建库语句
```sql
CREATE DATABASE gym_management_system DEFAULT CHARACTER SET utf8mb4;
```
2. 导入项目根目录 `gym_management_system.sql` 脚本，自动生成全部业务数据表

### 2. 启动后端
1. 打开 `gym-management-system/src/main/resources/application.yml`
2. 修改数据库账号、密码为本地MySQL配置
3. Maven加载依赖，运行启动类 `GymManagementSystemApplication`
4. 后端默认端口：8080

### 3. 启动前端
1. 终端进入 `frontend` 文件夹
```bash
npm install
npm run dev
```
2. 打开前端本地开发地址，进入登录页面

## 默认测试账号
| 角色 | 账号 | 密码 |
| ---- | ---- | ---- |
| 管理员 | 1001 | 123456 |
| 会员 | 202009867 | 123456 |

## 注意事项
1. 数据库统一使用 `utf8mb4` 字符集，避免中文乱码
2. 后端8080端口占用时，可在 `application.yml` 修改服务端口
3. 登录基于Session会话，关闭页面后登录失效
4. 前端打包后可放入后端static目录，实现一体化部署

## SQL语句优化（解决中文乱码、特殊字符存储报错，数据库字符规范）
1. 开头统一执行 `SET NAMES utf8mb4;`，全局指定字符集
2. 每张建表语句 `CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci
3. 防重复建表逻辑（每张表前置 `DROP TABLE IF EXISTS xxx;`，导入脚本时无需手动删旧表，不会报 “表已存在” 主键冲突。）
4. 外键安全优化

```
SET FOREIGN_KEY_CHECKS = 0; -- 临时关闭外键校验，方便批量删表建表
-- 全部建表插入完成
SET FOREIGN_KEY_CHECKS = 1; -- 恢复外键约束校验
```
 5.主键规范优化（所有业务主键统一设置 `NOT NULL`，无空主键脏数据；自增主键`class_order_id`、`equipment_id`规范设置`AUTO_INCREMENT`。）
## 索引优化
1.字符串索引截取前 100 位  字段名(100)
2.业务逻辑优化
- 会员登录、检索：按姓名 / 手机号查询，建立索引避免全表扫描；
- 筛选课程：按课程名、教练快速检索；
- 订单查询：课程 + 会员联合索引，关联查询大幅提速；
- 器材维护：按状态筛选损坏 / 正常设备；
- 员工管理：按职务筛选教练、保洁人员。
