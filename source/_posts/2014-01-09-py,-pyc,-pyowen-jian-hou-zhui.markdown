---
layout: post
title: " py、pyc、pyo文件后缀"
date: 2014-01-09 22:32:48 +0800
comments: true
categories: Python
---

#####py是源文件，pyc是编译后的bytes code，而pho是you优化编译后的文件。
#####python执行的源文件并不会生成pyc文件，而是其中import的文件会生成pyc文件。
#####python3.2以后，pyc文件放在所在目录的__pycache__中，名如script_name.cpython-33.pyc，这样可以区别不同版本python编译的pyc。
#####python3.2之前，放在所在目录。
#####手动生成pyc
```
import py_compile
py_compile.compile(r'H:\game\test.py')
############
import compileall
compileall.compile_dir(dirpath)
```
#####手动生成pyo
```
python -O -m py_compile file.py
```
