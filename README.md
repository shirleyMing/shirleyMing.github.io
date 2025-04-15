## shirley的个人博客

`develop`分支保存着博客项目源码，博客作者只需要在`develop`分支上创作

`master`分支保存着展示在页面上的博客内容，可以理解为项目最终的包的内容，由hexo命令直接推送到`matser``

### 发布步骤
```bash
# 切换到develop分支
# 清除缓存文件，建议写完文章后执行一次
hexo clean 
# 生成静态文件（生成 public 文件夹，写完文章执行）
hexo g                       g->generate
# 本地预览
hexo s                       s->server
# 部署到远程站点
hexo d                       d->deploy

# 或者直接执行
hexo clean && hexo g && hexo d
```
