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

### 使用Fluid主题配置
参考官方文档[Hexo Fluid 用户手册](https://hexo.fluid-dev.com/docs/start/#%E4%B8%BB%E9%A2%98%E7%AE%80%E4%BB%8B)

修改网站风格配置文件参考[配置指南](https://hexo.fluid-dev.com/docs/guide/#%E5%85%B3%E4%BA%8E%E6%8C%87%E5%8D%97)
