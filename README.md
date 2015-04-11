# Mindspop's Blog

## 博客源起
上大学的时候就折腾过独立博客，当时博客一直托管在`GoDaddy`的服务器上，还购买了域名，用的是`WordPress`模板，后来域名和空间都到期，博客荒废了1年多。大学毕业之后从事`产品设计`相关工作，在工作半年多后，对前端产生兴趣，遂自学前端，打算重开博客记录点技术方面的内容，同时为了减少博客空间的管理成本，就把博客托管在`GitHub`上。

## 博客搭建过程
博客搭建过程介绍：

* 本博客是基于`Hexo`技术搭建的 「从`Jekyll`叛逃到 Hexo，一切都是因为 Node.js」
* 主题是来源是 [NexT.Mist](https://github.com/iissnan/hexo-theme-next)，并对主题做了点修改。在此感谢原作者 [IIssNan](http://weibo.com/iissnan)
* 文章内容格式主要遵循原主题，仅修改了`List Item`样式
* 评论模块采用了`Duoshuo`
* 在搭建过程遇到的几个坑：
  1. GitHub 错误提示邮件：The submodule `xxx` was not properly initialized with a `.gitmodules` file。 `StackOverflow`上有解决方案
  2. 在解决问题 1 后，GitHub 还是发邮件说 build failure。后来排查原因是没有通过`Hexo deploy`方法部署站点到 GitHub。Hexo 在部署到 Github 时，需要 Deploy 生成并上传静态文件，这点和 Jekyll 有所区别，因为 Github 原生支持 Jekyll
  3. Duoshuo 接口请求评论信息时返回错误信息。最后排查发现是在多说后台配置域名时，把 mindspop 拼成 minsdpop


### 参考网站
1. [http://beiyuu.com/github-pages/](http://beiyuu.com/github-pages/)
2. [https://pages.github.com/](https://pages.github.com/)
3. [http://hexo.io/](http://hexo.io/)
4. [https://github.com/iissnan/hexo-theme-next](https://github.com/iissnan/hexo-theme-next)
5. [https://github.com/iissnan/hexo-theme-next/wiki](https://github.com/iissnan/hexo-theme-next/wiki)