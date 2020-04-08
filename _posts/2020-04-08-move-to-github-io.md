---
title: "专栏搬迁"
date: "2020-04-08 14:07:05 +0800"
---
大约三年之前，我在知乎开了这个专栏，更了几篇文章，但是后来因为种种原因没有继续下去。现在，我觉得是重新开始更新的时候了。作为一个开始，我把文章搬到了 GitHub pages 上，这样一方面可以用大家都喜欢的 markdown 格式来写技术文章了，另一方面专栏也有了自己的站点。

## 为什么要搬到 Github pages

继续使用知乎专栏虽然也没什么不好，但是我觉得一个内容创作者不应该与内容分发平台捆绑在一起，所以应该专门为专栏单独建立一个新站点。这样就算知乎倒闭或者我的账号被封，我也能继续发布我的内容。同时我还能借此机会公开我的插图的原始 svg 文件，并且为专栏设计专门的排版和格式。当然，之后的文章还是会在知乎专栏发布，但是我会先在专栏自己的站点发表然后再转载过去。

## 新站点的技术细节

专栏的新站点是用 [Jekyll](https://jekyllrb.com/){: target="_blank" rel="noopener"} 配合 [GitHub Pages](https://pages.github.com/){: target="_blank" rel="noopener"} 构建的。之所以使用 Jekyll 其实还是因为 GitHub Pages 直接支持 Jekyll。虽然 GitHub Pages 对使用的 Jekyll 插件的限制很死，但是我目前并不需要额外的插件。如果将来有需要的话，我会改成使用 GitHub Action 进行构建的方式。

由于我对自定义样式的要求非常高，所以我没有采用现成的主题，而是自己从头造。要实现主题，我需要自己写几个模板放进 `_layout` 文件夹，并编辑 `_sass` 文件夹里的样式就好。好在我可以直接从我的个人网站复制代码，所以整个过程并不算复杂，只需要稍微学习一下 Jekyll 采用的 [Liquid 模板引擎](https://shopify.github.io/liquid/){: target="_blank" rel="noopener"}就行了。

除此之外还有一些其他小的细节功能：

- “旧文”和“在其他平台阅读”：我希望对最先发表在知乎专栏上的旧文给出一个醒目的标识，同时在文章结尾给出在其他平台上的链接。实现这个功能只需要在每篇文章的 front matter 里面添加自定义元数据，并在模板里进行判断就行。
- 列表分页：把文章列表分拆成若干页需要使用 [jekyll-paginate 插件](https://jekyllrb.com/docs/pagination/){: target="_blank" rel="noopener"}，然后在模板里获取页码和当前页内的文章等信息即可。不过这个插件局限性挺大的，而且不会再添加新功能了，然而对我来说算是够用了。如果需要其它功能，比如对所有标签页表进行分页，那么需要改用 [jekyll-paginate-v2](https://github.com/sverrirs/jekyll-paginate-v2){: target="_blank" rel="noopener"}。当然，现在文章还不够多，所以看不出分页的效果.
- RSS 订阅与 sitemap：分别使用 [jekyll-feed](https://github.com/jekyll/jekyll-feed){: target="_blank" rel="noopener"} 和 [jekyll-sitemap](https://github.com/jekyll/jekyll-sitemap){: target="_blank" rel="noopener"} 实现，这两个插件的使用都十分简单，这里就不作过多介绍。
- Open Graph 元信息支持：使用 [jekyll-seo-tag](https://github.com/jekyll/jekyll-seo-tag){: target="_blank" rel="noopener"} 实现。做这个功能主要是为了能在 telegram 等比较先进的社交平台上能够有链接预览。我只需要在页面的 `<head>` 部分加上 `{% raw %}{% seo title=false %}{% endraw %}` 即可。
- 区分行内图片与单独的图片，以及让链接在新窗口中打开：jekyll 使用的 markdown 语法并非一般的 GitHub 式 markdown，而是 [kramdown](https://kramdown.gettalong.org/syntax.html){: target="_blank" rel="noopener"}，它有一种扩展语法允许你手动添加 HTML 元素的 id、class 以及其他属性。使用这种做法我们便可以手动控制连接和图片的行为了。

大概就是这个样子了。毕竟用 jekyll 搭网站并不是什么难事嘛。

## 最后

专栏搬迁既然已经完成，内容更新应该就会重新开始了。敬请期待。
