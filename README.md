# 个人博客网站
这是一个基于Hexo搭建的个人博客网站。

## 简介
该博客网站是使用Hexo框架构建的静态网站，用于展示个人的博客文章、项目和其他相关内容。

## 功能特点
- 使用Hexo进行静态网站生成，具有快速、高效的特点。
- 支持Markdown语法，方便编写博客文章。
- 可自定义主题和样式，使网站更符合个人喜好和风格。
- 支持标签、分类、归档等功能，方便读者浏览和搜索文章。
- 集成评论系统，与读者进行互动和交流。
- 支持部署到各种托管平台，如GitHub Pages、Netlify等。
## 使用说明
1. 克隆或下载本仓库到本地环境。
2. 安装Node.js和npm（如果尚未安装）。
3. 在命令行中切换到仓库所在目录，并运行以下命令安装依赖：
~~~ hexo
npm install
~~~
4. 根据个人需求，修改Hexo的配置文件 _config.yml，包括网站标题、描述、作者等信息。
5. 编写博客文章，将Markdown文件放置在 source/_posts 目录下。
6. 在命令行中运行以下命令生成静态网站：
~~~ hexo
hexo generate
~~~
7. 在命令行中运行以下命令启动本地服务器，预览网站：
~~~ hexo
hexo serve
~~~
8. 访问 http://localhost:4000 查看本地预览。
## 部署
你可以根据需要选择将网站部署到不同的托管平台。以下是一些常见的部署方法：

- 部署到GitHub Pages：将生成的静态文件上传到GitHub仓库，并在仓库设置中选择启用GitHub Pages功能。
- 部署到Netlify：将生成的静态文件上传到Netlify平台，并按照其指导完成部署配置。
- 其他托管平台：根据具体平台的要求和说明，完成网站的部署配置。
## 贡献
欢迎对该项目进行贡献。如果有任何问题、建议或Bug报告，请通过GitHub Issues提出。
