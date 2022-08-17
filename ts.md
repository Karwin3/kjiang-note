## 1. ts 安装

```i
npm install -g typescript
tsc -V
```



## 2. vscode 自动编译

```json
// 生成配置文件tsconfig.json
tsc --init

// 修改 tsconfig.json 配置
"outDir":".js",   // 编译文件输出目录
"strict":false    // 严格模式

// 启动监视任务
终端 => 运行任务 => 监视tsconfig.json
```

