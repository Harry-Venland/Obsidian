## 编译器cc gcc g++ CC的区别
- gcc全称是GNU Compiler Collection，原名为Gun C语言编译器，因为原本只能处理C语言，但gcc很快地扩展，包含很多编译器(C、C++、Objective-C、Ada、Fortran、Java)，可以说gcc是GNU编译器集合。  
- g++是C++编译器。  
- cc是Unix系统的C compiler，一个古老的C编译器。而Linux下cc一般是一个符号连接，指向gcc；可以通过`ls -l /usr/bin/cc`来简单查看，该变量是make程序的内建变量，默认指向gcc。cc符号链接和变量存在的意义在于源码的移植性，可以方便的用gcc来编译老的用cc编译的Unix软件，甚至连makefile都不用修改，而且便于Linux程序在Unix下编译。  
- CC则一般是makefile里面的一个名字标签，即宏定义，表示采用的是什么编译器（如：CC = gcc）
