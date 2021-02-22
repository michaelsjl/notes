---
tags: [C++]
title: C++ 学习笔记
created: '2021-02-18T03:07:32.648Z'
modified: '2021-02-20T05:40:45.091Z'
---

# C++ 学习笔记

strlen 计算字符串的长度，以结束符 0x00 为字符串结束。
sizeof 计算的则是分配的数组 str[20] 所占的内存空间的大小，不受里面存储的内容改变。

方法重载：同一类中的相同的方法名，参数和返回值均可不同。
方法重写：之类对父类已经实现的方法重新定义。

register是寄存器变量，寄存器是CPU的存储单元，可以直接访问而不需要通过总线，比访问内存快得多，因此频繁使用的变量可以定义为寄存器类型的。 C++11中， register 关键词的用法被废弃（deprecated），虽然在C++11/14中尚能使用 register 关键词声明变量，但不保证在以后还会支持这个用法。事实上，有人已经正式向C++标准会提议删除 register 关键词。目前，C++标准会尚未作出最终决定。 

1，对于x86，栈的增长方向是从大地址到小地址
2，对于函数调用，参数的入栈顺序是从右向左
3，函数调用入栈顺序是  右边参数-->左边参数-->函数返回地址

异常 	描述
std::exception 	该异常是所有标准 C++ 异常的父类。
std::bad_alloc 	该异常可以通过 new 抛出。
std::bad_cast 	该异常可以通过 dynamic_cast 抛出。
std::bad_exception 	这在处理 C++ 程序中无法预期的异常时非常有用。
std::bad_typeid 	该异常可以通过 typeid 抛出。
std::logic_error 	理论上可以通过读取代码来检测到的异常。
std::domain_error 	当使用了一个无效的数学域时，会抛出该异常。
std::invalid_argument 	当使用了无效的参数时，会抛出该异常。
std::length_error 	当创建了太长的 std::string 时，会抛出该异常。
std::out_of_range 	该异常可以通过方法抛出，例如 std::vector 和 std::bitset<>::operator。
std::runtime_error 	理论上不可以通过读取代码来检测到的异常。
std::overflow_error 	当发生数学上溢时，会抛出该异常。
std::range_error 	当尝试存储超出范围的值时，会抛出该异常。
std::underflow_error 	当发生数学下溢时，会抛出该异常。


1）QString转int
直接调用toInt()函数
例：
QString str("100");
int tmp = str.toInt();

或者：
bool ok;
QString str("100");
int tmp = str.toInt(&ok);

注：ok表示转换是否成功，成功则ok为true，失败则ok为false。

2）int转QString
QString::number();
例：
int tmp = 100;
QString str = QString::number(tmp);

