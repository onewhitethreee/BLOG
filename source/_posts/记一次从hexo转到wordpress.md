---
uuid: 99825c60-1b01-11f0-b451-b50f355876d6
title: 记一次从hexo转到wordpress
date: 2025-04-16 22:30:13
tags: 
  - hexo
  - wordpress
  - markdown
categories: hexo
---

https://linux.do/t/topic/563664?u=onewhite

从昨天的话题想到，静态博客每一次写完一个新的就都要重新上传，打包，我提出了一个想法，如果有一万篇内容的话甚至更多，那么这个打包的时间会非常的长。在这个求问贴中提出了有没有什么优化方案

答案是有的，从静态换到动态。我选择了wordpress。在这里感谢这位大佬，给我建议了一个wordpress主题。互联网真好

https://linux.do/u/sniao/summary

---

好，回到标题。

从hexo转到wordpress是一个大问题，更本质的问题是要怎么把markdown换到wordpress下，手动？

那也太麻烦了，咱们学计算机的怎么能手动呢，手动不了一点

我开始善用搜索引擎，首先搜索到的结果的是用wordpress中的插件，markdown import啥的，我试了，一点用都没有，草！

继续搜，搜到了新的插件，使用hexo生成的rss来去导入到wordpress中，我又试了，还是屁都没用，草！

好在功夫不负有人心，我在广阔的网络世界中找到了一篇博客

https://blog.1kye.com/277/

他提出了有这么一个接口能够去上传，我试了，可行！

# 感谢Google收录了这篇帖子，感谢这名大佬，感谢我能够搜到。

好，那么虽然能够上传，但是会有一些小bug，比如，我原来的markdown中在头部有这么一个index_img用来标题文章图，在上传的时候并不会去处理，不过问题不大。

还有，我文章内的图片，好在都是图床，而不是本地，这就省下了我巨多的处理时间。谢谢Cloudflare给我提供的图床，**谢谢！**

另外，markdown里的**tags**和**categories**也不会去进行解析，不过问题不大，这都是能解决的。

至于解决方法是什么？那当然是神奇的Claude了！

谢谢Claude！谢谢！！！！

---

最后丢个原来的作者仓库，已经给作者提了PR了，不过看Github主页最后一次活跃在去年。

我还得要说，谢谢互联网，谢谢各位，谢谢赛博菩萨，谢谢 :lark_029:

---

https://github.com/onekyle/HexoToWordPress