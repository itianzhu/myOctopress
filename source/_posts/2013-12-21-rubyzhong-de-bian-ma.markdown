---
layout: post
title: "Ruby中的编码"
date: 2013-12-21 22:46:04 +0800
comments: true
categories: Ruby encoding 编码
---
###编码的种类
1. 源文件的编码
2. 变量的编码
3. I/O的编码
<hr>
###查看本机的所有编码
```
encodings = Encoding.list.each.with_object({}) do |enc, full_list| 
 full_list[enc.name] = [enc.name]
end
Encoding.aliases.each do |alias_name, base_name|
  fail "#{base_name} #{alias_name}" unless encodings[base_name] encodings[base_name] << alias_name
end
```
<hr>
###文件的编码
- 2.0后的Ruby中，文件默认是UTF-8编码，之前是ASCII
- 在文件的最前面写上如下代码,Ruby会在其中搜索编码字符串



```
#coding: utf-8
```
如果前面有shebang，先写shebang，第二行写coding


```
#!/usr/local/rubybook/bin/ruby
#  encoding:  utf-8
```
- Mac OS中得命令行默认是UTF8
- 每个文件的编码都可以不同
- ASCII-8BIT就是所谓的binary
<hr>
###变量编码


1、变量的编码默认是文件的编码
```
# encoding: utf-8
"中".encoding # => <Encoding:UTF-8>
```
如果文件中字符不能用标注的编码解析(即那段二进制编码在此编码中不代表字符)，则会报错，比如
```
# encoding: ascii
"中".encoding # => <Encoding:UTF-8>
aa.rb:2: invalid multibyte char (US-ASCII)
aa.rb:2: invalid multibyte char (US-ASCII)
```
2、变量的编码转换，就是把相同字符转换成不同的编码表示，二进制表示会改变
```
'a'.encoding.name # => UTF-8
'a'.encode('ascii').encoding
 => #<Encoding:US-ASCII>
```
如果新的编码中，没有此字符，则会报错，可以如下解决
```
# encoding: utf-8
pi = "pi = π"
puts pi.encode("iso-8859-1", :undef => :replace, :replace => "??")
# => pi = ??
```
3、强制转换。是指强制用一种编码去解释一段二进制序列，而不是转换成新的编码，文件的二进制是不会改变的
```
# encoding: ascii-8bit
str = "\xc3\xa9" # e-acute in UTF-8 str.encoding # => #<Encoding:ASCII-8BIT> str.force_encoding("utf-8")
str.bytes.to_a # => [195, 169] str.encoding # => #<Encoding:UTF-8>
```
从binary文件读取的时候，也可以使用ecoding的第二个参数表示实际编码
```
# encoding: ascii-8bit
original = "ol\xe9" # e-acute in ISO-8859-1 original.bytes.to_a # => [111, 108, 233] original.encoding # => #<Encoding:ASCII-8BIT> new = original.encode("utf-8", "iso-8859-1") new.bytes.to_a # => [111, 108, 195, 169] new.encoding # => #<Encoding:UTF-8>
```
<hr>
###I/O编码
#####标准I/O的编码和命令行的编码是一样的，MacOS的bash默认是UTF-8，从命令行输入的字符是UTF-8，输出到命令行的字符，只有UTF-8能够完全正确  

#####文件I/O的编码的方法另外还有：  
-  external_encoding，外部编码，即输入编码  
-  internal_encoding，内部编码，即输出编码
输入编码默认与bash相同，如果文件实际不是这个编码，则会解释不对，但是不会改变二进制表示，可以这样改变
```
f = File.open("iso-8859-1.txt", "r:iso-8859-1") 
puts f.external_encoding.name
line = f.gets
puts line.encoding
puts line
produces:
       ISO-8859-1
       ISO-8859-1
       ol?
```
输出编码默认与输入编码一样，可以这样改变
```
f = File.open("iso-8859-1.txt", "r:iso-8859-1:utf-8") 
puts f.external_encoding.name
line = f.gets
puts line.encoding
puts line
produces:
       ISO-8859-1
       UTF-8
       olé
```
可以在命令行改变输入、输出编码
```
$ echo $LANG
$ LANG=ja_JP.sjis ruby -e 'p Encoding.default_external.name'
$ ruby -E sjis:iso-8859-1 -e 'p Encoding.default_internal.name'
$ ruby -E :utf-8
```