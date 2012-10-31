---
layout: post
title: "如何制作自己的gem （二）"
date: 2012-10-31 11:17
comments: true
categories: 
---

接上篇，[如何制作自己的GEM (一)](http://i3zhe.github.com/blog/2012/10/29/ru-he-zhi-zuo-zi-ji-de-gem/)

* [写测试](#writing-tests)
* [给gem写文档](#document-code)
* [总结](#wrapup)
<a id="writing-tests"></a>
<h2>写测试</h2>

泰斯特你的gem是贼拉的重要！它不仅帮助你确认你的代码是可以运行的，而且它也帮助别人了解你的gem是顺利工作的。当衡量一个gem时，ruby开发者倾向于通过查看一套可靠的测试（或者说由于缺少测试）来作为信任这段代码的主要理由。

Gems支持在代码包里添加测试文件，所以当下载完gem后我们就可以开始测湿了。有一个致力于此的社区已经出现，它的名字叫[GemTesters](http://test.rubygems.org/)，这里有详细的文档介绍在不同的Ruby架构和解释器中运行gem测湿。

一句话不割：请测试你的GEM！

`Test::Unit`是Ruby内置的测湿框架。网上狼哇多的[教程](https://github.com/seattlerb/minitest/blob/master/README.txt)介绍如何使用它。当然还有许多其他的测试框架可用。Rspec是很流行的一个。说白了，不管你用哪个，测湿就行！

下面咱们给女神加点测湿。需要再加几个文件：一个是Rakefile，还有一个全新的test文件夹：
```
% tree
.
├── Rakefile
├── bin
│   └── nvshen
├── nvshen.gemspec
├── lib
│   ├── nvshen
│   │   └── translator.rb
│   └── nvshen.rb
└── test
    └── test_nvshen.rb
```

Rakefile可以让你自动跑测湿：
```
% cat Rakefile
require 'rake/testtask'

Rake::TestTask.new do |t|
  t.libs << 'test'
end

desc "Run tests"
task :default => :test
```

现在执行`rake test`或者就`rake`就能跑测湿啦。呵呵，下面是一个女神的基本测湿文件：
```
% cat test/test_nvshen.rb
require 'test/unit'
require 'nvshen'

class NvshenTest < Test::Unit::TestCase
  def test_english_hello
    assert_equal "hello nvshen",
      nvshen.hi("english")
  end

  def test_any_hello
    assert_equal "hello nvshen",
      nvshen.hi("ruby")
  end

  def test_spanish_hello
    assert_equal "你好 女神",
      nvshen.hi("chinese")
  end
end
```

最后执行测湿：
```
% rake test
(in /Users/qrush/Dev/ruby/nvshen)
Loaded suite
Started
...
Finished in 0.000736 seconds.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips

Test run options: --seed 15331
```

绿灯通过！好吧，其实取决与你的shell配色。想学习更多的好例子，最佳实践就是去github瞎溜达并且读别人的代码。

<hr>
<a id="document-code"></a>
<h2>给代码写文档</h2>

大多数gems默认用RDoc来生成文档。有大量好的[教程](http://docs.seattlerb.org/rdoc/RDoc/Markup.html)传授如何用RDoc来写文档。下面是一个简单示例：
```
# The main Nvshen driver
class Nvshen
  # Say hi to the world!
  #
  # Example:
  #   >> Nvshen.hi("chinese")
  #   => 你好 女神
  #
  # Arguments:
  #   language: (String)

  def self.hi(language = "english")
    translator = Translator.new(language)
    puts translator.hi
  end
end
```

另一个出色的文档选择是[YARD](http://yardoc.org/)，因为当你推送gem时，RubyDoc.info会通过你的gem自动生成YARDocs。 YARD向后兼容RDoc，并且有一篇文章[很好的文章](http://rubydoc.info/docs/yard/file/docs/GettingStarted.md)介绍了他们的区别以及用法。

<hr>
<a id="wrapup"></a>
<h2>总结</h2>

有了一个构建RubyGem的基本理解，我们希望你将去亲自实践。

<hr>
<a id="credits"></a>
<h2>演员表</h2>

这篇教程借鉴了[Gem Sawyer的Modern Day Ruby Warrior](http://rubylearning.com/blog/2010/10/06/gem-sawyer-modern-day-ruby-warrior/)，由[Jerry Huo](http://i3zhe.github.com)翻译改编。如有巧合，纯属雷同！


