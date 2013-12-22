---
layout: post
title: "Ruby调试"
date: 2013-12-22 22:16:47 +0800
comments: true
categories: Ruby Debug
---
开始调试
```
$ ruby -r debug test.rb
```
支持的命令，参考http://www.zenspider.com/Languages/Ruby/QuickRef.html#debugger
```
b[reak] [file:|class:]<line|method
b[reak] [class.]<line|method
                           set breakpoint to some position
wat[ch] expression         set watchpoint to some expression
cat[ch] exception          set catchpoint to an exception
b[reak]                    list breakpoints
cat[ch]                    show catchpoint
del[ete][ nnn]             delete some or all breakpoints
disp[lay] expression       add expression into display expression list
undisp[lay][ nnn]          delete one particular or all display expressions
c[ont]                     run until program ends or hit breakpoint
s[tep][ nnn]               step (into methods) one line or till line nnn
n[ext][ nnn]               go over one line or till line nnn
w[here]                    display frames
f[rame]                    alias for where
l[ist][ (-|nn-mm)]         list program, - lists backwards
                           nn-mm lists given lines
up[ nn]                    move to higher frame
down[ nn]                  move to lower frame
fin[ish]                   return to outer frame
tr[ace] (on|off)           set trace mode of current thread
tr[ace] (on|off) all       set trace mode of all threads
q[uit]                     exit from debugger
v[ar] g[lobal]             show global variables
v[ar] l[ocal]              show local variables
v[ar] i[nstance] object    show instance variables of object
v[ar] c[onst] object       show constants of object
m[ethod] i[nstance] obj    show methods of object
m[ethod] class|module      show instance methods of class or module
th[read] l[ist]            list all threads
th[read] c[ur[rent]]       show current thread
th[read] [sw[itch]] nnn    switch thread context to nnn
th[read] stop nnn          stop thread nnn
th[read] resume nnn        resume thread nnn
p expression               evaluate expression and print its value
h[elp]                     print this help
everything else            evaluate
empty                      repeats the last command
```
或者使用[ruby-debug](http://bashdb.sourceforge.net/ruby-debug.html)等