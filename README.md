# lab3-data-visualization

## 项目说明
本项目为HCI lab3-data-visualization项目，制作一个看板用以展示数据<br/>
本项目使用的主要图表插件：echarts

## Build Setup

```bash
# 克隆项目
git clone git@github.com:sfffffa/data_visualization.git

# 进入项目目录
cd lab3-data-visualization

# 安装依赖
npm install

# 建议不要直接使用 cnpm 安装以来，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 启动服务
npm run dev
```

浏览器访问 [http://localhost:9528](http://localhost:9528)

## 发布

```bash
# 构建测试环境
npm run build:stage

# 构建生产环境
npm run build:prod
```

## 其它

```bash
# 预览发布环境效果
npm run preview

# 预览发布环境效果 + 静态资源分析
npm run preview -- --report

# 代码格式检查
npm run lint

# 代码格式检查并自动修复
npm run lint -- --fix
```
## 本项目参考框架

```bash
# 克隆项目
git clone git@github.com:PanJiaChen/vue-element-admin.git

```