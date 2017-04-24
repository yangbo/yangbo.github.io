---
layout: post
title:  "如何用 Jekyll 创建一个技术博客"
date:   2017-01-01 22:10:00
categories: jekyll blog
---
本 post 在 `_posts` 目录中。去编辑然后 re-build 本站就能看到你所作的改变了。 你有很多种方法 rebuild 本站，但最常用的方法是运行 `jekyll serve`，这会启动一个 web server 并且当有文件改动时，会自动生成你的网站。

添加新的 posts 只需要在目录`_posts`中添加一个文件即可, 文件名要按照 `YYYY-MM-DD-name-of-post.ext` 的约定方式命名, 并且文件内容要包含必要的 front matter, 也就是声明一些 YAML 形式的变量.

Jekyll 对 code snippets 提供了强大的支持, 例如:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

查看 [Jekyll docs][jekyll] 来了解如何用好 Jekyll. Bug 和 新特性请提交到 [Jekyll’s GitHub repo][jekyll-gh]. 如果有问题可以去这里 [Jekyll’s dedicated Help repository][jekyll-help].

# Jekyll Bug

只能发布昨天的 posts，例如当前时间是 2017-01-02 那么 posts 的时间只能是 2017-01-01 的，否则 Jekyll 不会生成。

# Jekyll Theme 推荐

[lanyon](https://github.com/poole/lanyon)

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
