---
layout: post
title: "【翻译】如何制作自己的GEM"
date: 2012-10-29 14:00
comments: true
categories: 
---

原文链接： [猛击这里](http://guides.rubygems.org/make-your-own-gem/)

今天这个会，我们将学习如何把你的ruby代码打包成外卖（pia！开会开出瘾了？外卖个头，你个吃货！ 是gem啊！）

* [介绍](#intro)
* [处女gem](#first_gem)
* [包含更多文件](#more-files)
* [添加可执行文件](#executable)
* [写测试](#writing-tests)
* [给gem写文档](#document-code)
* [总结](#wrapup)

<hr>
<a id="intro"></a>
<h2 id="intro">介绍</h2>

感谢RubyGems内嵌的一个工具，让我们创建和发布gem变得非常简单。下面我们来制作一个简单的“hello 女神”gem，大家回家以后可以随便耍耍！ 我们要做的这个gem的代码已经在[github](https://github.com/qrush/hola)安家了，需要的猛击。
<hr>
<a id="first_gem"></a>
<h2 id="first_gem">处女gem</h2>

我将从一个ruby文件开始来创建这个女神gem，还有她的闺蜜gemspec。你愿意的话可以给这个gem起个新名字来发布（或许是波多野结衣神马的，你懂的）。查阅并参考一下官方[样式向导](http://guides.rubygems.org/patterns/#consistent-naming)的基本推荐来命名你的Gem。
```
% tree
.
├── nvshen.gemspec
└── lib
    └── nvshen.rb
```

Gem的代码主要放置在`lib`目录下。通常的原则是让一个ruby文件和你的gem名字相同，因为当`require 'nvshen'`运行时，她会自动被加载。她的闺蜜文件是负责设置你的gem代码和API。

`lib/nvshen.rb`里的代码是非常柔弱无骨的。它只是保证你可以看到女神gem会有些输出：
```
    % cat lib/nvshen.rb
    class Nvshen
      def self.hi
        puts "Hello Nvshen!"
      end
    end
```

gemspec文件定义了gem的内容，作者，还有gem的版本。它也是你通往[RubyGems.org](http://www.rubygems.org)的介绍信。你在一个gem上所能看到的所有信息(象[jekyll](http://rubygems.org/gems/jekyll))都来自这个闺蜜文件-gemspec。
```
% cat hola.gemspec
Gem::Specification.new do |s|
  s.name        = 'nvshen'
  s.version     = '0.0.0'
  s.date        = '2012-12-29'
  s.summary     = "Nvshen!"
  s.description = "A simple hello nvshen gem"
  s.authors     = ["Jerry Huo"]
  s.email       = 'hjzdhr@163.com'
  s.files       = ["lib/nvshen.rb"]
  s.homepage    =
    'http://rubygems.org/gems/hola'
end
```

脸熟吧？这个闺蜜gemspec也是ruby系的，所以你可以写脚本来生成文件名和更改版本号。这个闺蜜妹子还有很多内置参数，感兴趣的请猛击[这里](http://guides.rubygems.org/specification-reference)。

当你创建完这个gemspec文件后，你就可以通过它构建一个gem了。然后在本地安装这个gem来进行测试。

```
% gem build nvshen.gemspec
Successfully built RubyGem
Name: nvshen
Version: 0.0.0
File: nvshen-0.0.0.gem

% gem install ./nvshen-0.0.0.gem
Successfully installed nvshen-0.0.0
1 gem installed
```

这个迷烟测试还没有结束：最后一步就是`require`此gem并调戏她：
```
% irb
>> require 'nvshen'
=> true
>> Nvshen.hi
Hello world!
```
*如果你使用的ruby版本低于1.9.2，你需要使用`irb -rubygems`，或者运行irb之后再require rubygems库*

现在你可以和ruby社区的其他人一起分享女神这个gem。假设你已经有了一个账户， 把你的gem发布到RubyGems.org只需要一个命令。按下面提示在你的电脑上设置你的RubyGems账户：
```
$ curl -u YOUR_USER_NAME https://rubygems.org/api/v1/api_key.yaml >
~/.gem/credentials
Enter host password for user 'YOUR_USER_NAME':
```

设好这一步，你就可以推出你的新Gem了：
```
% gem push nvshen-0.0.0.gem
Pushing gem to RubyGems.org...
Successfully registered gem: nvshen (0.0.0)
```

很短的时间之内（通常不超过一年），任何人都可以安装你的gem了。你可以在[RubyGems.org](https://rubygems.org/gems/hola)上看到她，或者通过RubyGems.org上在任何计算机上得到她。
```
% gem list -r nvshen

*** REMOTE GEMS ***

nvshen (0.0.0)

% gem install nvshen
Successfully installed nvshen-0.0.0
1 gem installed
```

用Ruby和RubyGems分享代码实在是太轻松了。
***
<a id="more-files"></a>
<h2>包含更多文件</h2>
把所有东西放在一个文件里会影响扩展。我们再让女神丰满点。
```
% cat lib/nvshen.rb
class Nvshen
  def self.hi(language = "english")
    translator = Translator.new(language)
    puts translator.hi
  end
end

class Nvshen::Translator
  def initialize(language)
    @language = language
  end

  def hi
    case @language
    when "chinese"
      "你好 女神"
    else
      "hello nvshen"
    end
  end
end
```

女神变得有点臃肿。让我们把`Translator`分解成单独的文件。如前面提到的，gem根目录那个文件是负责加载gem的代码。gem的其他文件通常放置在`lib`文件夹下与gem同名的目录底下。我们可以把这个gem分解成这样：
```
% tree
.
├── nvshen.gemspec
└── lib
    ├── nvshen
    │   └── translator.rb
    └── nvshen.rb
```

`Translator`现在位于`lib/nvshen`目录下，我们可以通过`require`方式在`lib/nvshen.rb`里轻松的调用它。`Translator`的代码并没有多大差异：
```
% cat lib/nvshen/translator.rb
class Nvshen::Translator
  def initialize(language)
    @language = language
  end

  def hi
    case @language
    when :chinese
      "你好 女神"
    else
      "hello nvshen"
    end
  end
end
```

但现在`nvshen.rb`里需要增加一点代码来加载`Translator`:
```
% cat lib/nvshen.rb
class Nvshen
  def self.hi(language = :english)
    translator = Translator.new(language)
    translator.hi
  end
end

require 'nvshen/translator'
```

接下来试一试，启动`irb`：
```
% irb -Ilib -rnvshen
irb(main):001:0> Nvshen.hi(:english)
=> "hello nvshen"
irb(main):002:0> nvshen.hi(:chinese)
=> "你好 女神"
```

这里我们需要用到一个奇怪的命令行标志`-Ilib`. 通常RubyGems会自动替你引用`lib`目录，所以终端用户通常不需要为配置加载路径而担忧。 然而，如果你是在RubyGems之外的环境运行代码，你就需要自己配置这些东西。可能有人会在代码里操作`$LOAD_PATH`，但那通常被视为反面示范。在这个[向导](http://guides.rubygems.org/patterns)里有很多反面示例（当然也有正面示例）。

如果你在gem里添加了许多文件，千万要记得在发布前把他们加到gemspec的file数组里。因此，许多开发者通过[Hoe](http://seattlerb.rubyforge.org/hoe/), [Jeweler](https://github.com/technicalpickles/jeweler), [Rake](http://rake.rubyforge.org/classes/Rake/GemPackageTask.html), [Bundler](http://railscasts.com/episodes/245-new-gem-with-bundler) 来自动化此步骤，或者直接使用动态gemspec。

从这开始，再通过添加更多代码来增加更多目录都是相同的步骤。尽可能合理的分割ruby文件。使你的项目井然有序将帮助你自己和未来的维护者减少头疼和麻烦。
<hr>
<a id="executable"></a>
<h2>添加可执行文件</h2>
Gem除了可以提供ruby代码的库，还可以曝光一个或多个可执行文件到shell的`PATH`上。可能最知名的例子就是`rake`了。另一个非常有用的是`prettify_json.rb`, 包含在`JSON`的gem里。 它可以把JSON格式化成更可读的形式（ruby1.9众也内置了它）。例子如下：
```
% curl -s http://jsonip.com/ | \
  prettify_json.rb
{
  "ip": "24.60.248.134"
}
```

给gem添加可执行文件的步骤非常简单。你只需要把那个文件放在gem的`bin`目录底下，然后把它加到gemspec的可执行文件的列表里。咱们来给女神gem添加一个。第一步创建文件并让她可以执行：
```
% mkdir bin
% touch bin/hola
% chmod a+x bin/hola
```

可执行文件本身需要一个`[shebang](http://www.catb.org/jargon/html/S/shebang.html)`来制定是运行什么程序。下面的女神的文件示例：
```
% cat bin/nvshen
#!/usr/bin/env ruby

require 'nvshen'
puts Nvshen.hi(ARGV[0])
```

她的作用就是加载女神gem，然后把命令行第一个参数作为语言的变量来输出结果。下面是运行示例：
```
% ruby -Ilib ./bin/hola
hello world

% ruby -Ilib ./bin/hola chinese
你好 女神
```

最后，为了使你发布gem的时候，可执行文件被包括其中，你需要把它加到gemspec中：
```
% head -4 nvshen.gemspec
Gem::Specification.new do |s|
  s.name        = 'nvshen'
  s.version     = '0.0.1'
  s.executables << 'nvshen'
```

推送新gem，你就可以发布自己的命令行工具。需要的话，你可以添加更多的可执行文件，记得也把他们加到gemspec里面的`executables`的数组。

*注意：你推送一个新发布时，需要加上新的版本号，更多有关Gem版本的资料，请猛击[Pattern Guide](http://guides.rubygems.org/patterns/#semantic-versioning)*

***未完待续（稍后更新测试）***




