## 功能
- 设置微信公众号自定义菜单 生成JSON格式的菜单参数 配合后台通过调用微信接口生成自定义菜单

## 开发
```bash
    # 安装依赖
    npm install

    # 本地开发 开启服务
    npm run dev
```
浏览器访问 http://localhost:8080

## 发布
```bash
    npm run build
```
## 目录结构
```wechatmenu
├── build                      // 构建相关  
├── config                     // 配置相关
├── src                        // 源代码
│   ├── assets                 // 图片等静态资源
│   ├── components             // 全局公用组件
│   ├── App.vue                // 入口页面
│   └── main.js                // 入口 加载组件 初始化等
├── .babelrc                   // babel-loader 配置
├── eslintrc.js                // eslint 配置项
├── .gitignore                 // git 忽略项
├── index.html                 // html模板
└── package.json               // package.json

```

