---
layout: post
title:  "从现在开始用新版本的Jekyll"
date:   2014-04-21 00:24:03
categories: jekyll update
---

以前用的是老版本的Jekyll，而且由于自己有sinapp的blog，所以这边只是更新自己的产品想法。但是，由于最近sinaapp那边的blog已经被停掉了，并且自己不想交钱，于是转向github。

想想以前在[ITeye]的博客和[SinaApp]的博客还是写了很多内容，让自己的知识变得有条理性，于是决定继续保持做笔记的习惯。

Jekyll这东东看着有点厉害，而且markdown看着也很厉害，周边的小伙伴们也都在努力科研的同时写笔记，咱也不能落下。

努力！

补充说明：

1.如何配置jekyll可以显示latex公式

  首先，把MathJax的javascript放入head标签内

  然后，把_config.yml中的markdown解析器换掉

  最后，在post中，需要显示latex的地方用 双$符号来开始和标示结束即可。


You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
[ITeye]: http://sonyfe25cp.iteye.com
[SinaApp]: http://sonyfe25cp.sinaapp.com
