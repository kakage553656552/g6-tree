# 组织架构图

使用 Vue 3 和 G6 实现的组织架构图展示组件。

## 功能特点

- 展示树形组织结构
- 支持缩放和平移操作
- 节点悬停显示详细信息
- 响应式布局，适应不同屏幕大小

## 技术栈

- Vue 3.x
- Vite
- G6 图形可视化引擎

## 开发

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 预览生产构建
npm run preview
```

## 项目结构

```
tree/
├── src/
│   ├── assets/        # 静态资源
│   ├── components/    # 组件目录
│   │   └── OrganizationTree.vue  # 组织架构图组件
│   ├── App.vue        # 应用主组件
│   └── main.js        # 入口文件
├── index.html         # HTML 模板
├── vite.config.js     # Vite 配置
└── package.json       # 项目配置
```

## 自定义数据

在 `OrganizationTree.vue` 中修改 `data` 常量即可自定义组织架构树数据。数据格式为：

```js
{
  id: 'unique-id',
  label: '节点名称',
  children: [
    // 子节点...
  ]
}
``` 