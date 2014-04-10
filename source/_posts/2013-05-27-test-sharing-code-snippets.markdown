---
layout: post
title: "Test sharing code snippets"
date: 2013-05-27 16:54
comments: true
categories: other
---

Update the `Gist plugin` usage 		*2014-04-10*


Sharing Code Snippets

<!-- more -->

*Backtick Code Blocks*

Syntax:(insert enter before and after the two ``````)

```
``` [language] [title] [url] [link text]
code snippet ```

```

**Example(plain):**
```
``` $ sudo make me a sandwich ```

```

```
$ sudo make me a sandwich
```

Example With Syntax Highlighting a Caption and Link

``` 
```ruby Discover if a number is prime http://www.noulakaz.net/weblog/2007/03/18/a-regular-expression-to-check-for-prime-numbers/ Source Article
class Fixnum
  def prime?
    ('1' * self) !~ /^1?$|^(11+?)\1+$/
  end
end```
```


``` ruby Discover if a number is prime http://www.noulakaz.net/weblog/2007/03/18/a-regular-expression-to-check-for-prime-numbers/ Source Article
class Fixnum
  def prime?
    ('1' * self) !~ /^1?$|^(11+?)\1+$/
  end
end
```


More:
http://octopress.org/docs/blogging/code/


####Codeblock
#####Syntax

{% codeblock asdf lang:objc %}
NSObject *obj = [NSObject new];
NSObject *obj = [[]NSObject alloc] init];
{% endcodeblock %}

#### Gist

2462760为ID，如果是多个文件的话，可在后面跟上文件名。(但现在好像不太能用啊)

``` 
{% gist 9828745 %} 
```



