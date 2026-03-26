## 图形化方式

![](assets/Hello%20World/file-20260326231227878.png)
Qt中Display Widgets里面的标签都是显示类的控件，我们用Label就可以控制输出文本，拖入编辑即可
![](assets/Hello%20World/file-20260326231338126.png)
![](assets/Hello%20World/file-20260326231356505.png)
![](assets/Hello%20World/file-20260326231413929.png)
再看.ui文件就会发现xml多了新的内容，qmake会在编译项目的时候基于这个内容生成一段C++代码，通过这个代码构建出界面 
![](assets/Hello%20World/file-20260326231517388.png)
生成的ui_widget.h代码中也在setupUi中设置了label对象和参数
![](assets/Hello%20World/file-20260326231856155.png)
这里也就挂在了对象树上

## 代码方式

```C++
Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);
}
```
一般通过代码来构建界面的时候，通常会把构造界面的代码放到Widget/MainWindow的构造函数中，在Widget w;中执行
![](assets/Hello%20World/file-20260326232502667.png)
qlabel.h是Qt的上古写法，后来响应C++<cstdio>风格有了新的QLabel写法


![](assets/Hello%20World/file-20260326232722946.png)
创建控件最好在堆上创建，在栈上也可以。label对象最好传入this指针，指明label的父对象
QString是在C++还没形成标准时，当时怎么表示一个字符串还没有确定，可以用C风格的字符串（\0）结尾，也可以使用C++的string，但是当时的string还不太好用。于是Qt为了让自己开发变顺畅，就自己造了一套轮子基础类来开发，如：QString，QVector，QList，QMap。虽然后来C++标准也打磨好了，但是Qt已经写好的容器也不会删了，就只能和标准库的共存了

因此，在写Qt的时候，如果需要使用上面的容器类，可以使用标准库的容器，也可以使用Qt自己搞的这一套。但是Qt原生的api中，涉及到的接口用的都是Qt自己的这一套容器

所以后续经常见到QString而比较少遇到std::string，用起来更方便一点点，QString内部对字符编码做了一些处理

```C++
#include "widget.h"
#include "ui_widget.h"
#include <QLabel>

Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);
    QLabel* lable=new QLabel(this);
    lable->setText("Hello World");
    // QLabel label;
}

Widget::~Widget()
{
    delete ui;
}
```

在QString中也提供了C风格字符串作为参数的构造函数，不显示构造QString，代码中C风格的字符串也会隐式构造成QString对象

QString对应的头文件，已经被很多Qt内置类间接包含

![](assets/Hello%20World/file-20260326233637865.png)

![](assets/Hello%20World/file-20260326235101096.jpg)
![](assets/Hello%20World/file-20260326235109116.png)


上述代码在Qt中不会产生内存泄漏，label对象在合适的时候会被析构释放，虽然没有手动写delete

之所以能够把对象释放掉，主要是因为把这个对象挂在了对象树上。前端开发（网页开发）也涉及类似的对象树（DOM），本质上也是一个树形结构（N叉树），通过树形结构把界面上的各种元素组织起来。
此处通过new的方式创建对象，就是为了把这个对象生命周期交给Qt的对象树来统一管理，当父对象的窗口销毁了，那么子节点对象也会跟着销毁，不会造成内存泄漏。
但是如果这里使用栈上创建，就会随着Widget的构造函数结束而销毁，也就看不到文字效果

如果没有挂在对象树上，一定要记得手动释放！



创建一个自定义的类，最主要的目的，是自定义一个析构函数，在析构函数中完成打印。通过在构造函数初始化列表中初始化父类，将父对象指针传递进去，可以将自定义类挂在对象树上


```C++
#ifndef MYLABEL_H
#define MYLABEL_H

#include <QLabel>

class MyLabel : public QLabel
{
public:
    MyLabel(QWidget* parent);
    ~MyLabel();
};

#endif // MYLABEL_H

```

```C++
#include "mylabel.h"
#include <iostream>

MyLabel::MyLabel(QWidget* parent):QLabel(parent) {

}

MyLabel::~MyLabel()
{
    std::cout<<"MLabel 被销毁"<<std::endl;
}

```
所谓继承，本质上是扩展，保持原有功能不变的基础上，给自己的对象扩展出新的功能
通过继承Qt内置类，可以达到对现有的控件进行功能扩展效果，也可以重写控件中的任何功能，不仅仅是析构函数，达到功能扩展目的

![](assets/Hello%20World/file-20260327000350896.png)
我们会发现此处有输出，但是是乱码。
乱码问题出现的原因，有且仅有一个，那就是编码方式不匹配

在计算机中，一个汉字占几个字节？
针对这个问题，只要回答出具体数字就一定是错的。
前提条件：当前的中文编码使用的是哪种方式（字符集）
英文字母都是以ASCII码表表示的，规定了每个字符都有一个对应的数字表示
只是表示英文，一个字节足够了。因为英文字母数量少
但中文中常用的日常字大概是4K多个，算上各种生僻字，总数差不多6w左右
仍然使用一个大表格，给每个汉字分配一个整数即可。对于计算机来说6w多个符号的表格是小问题
但是具体每个汉字都用哪个数字表示是不一定的。
字符集：表示汉字的字符集其实有很多种，不同的字符集表示同一个汉字，使用的数字不同
目前常见的字符集主要是两种：
1. GBK（中国大陆），使用2字节表示一个汉字，Windows简体中文版默认字符集就是GBK
2. UTF-8/utf8 变长编码，表示一个符号，使用的字节数有变化 2-4字节，但在utf8中，一个汉字一般是3字节（Linux中默认）
一个汉字具体的utf8/GBK编码是多少，可以用在线查询网站看

如果字符串本身是utf8编码，但是终端（控制台）使用gbk编码解析，就会出现乱码（拿着utf8的数值去查gbk的表）

代码中的字符串编码方式与文件的编码方式一样（ANSI->GBK）
QtCreator的终端用的字符串编码不一定是用UTF-8
当前表示中文主流的还是UTF-8，因为这不止支持中文，支持绝大多数世界上的语言。
Qt中有一个QSrting，可以帮助我们自动处理编码方式，不只是QString，Qt也专门提供了打日志的工具，也能自动处理编码方式qDebug

QDebug是Qt中的一个类，但我们不会直接使用这个类
qDebug是一个宏，封装了QDebug对象，直接使用qDebug来打日志，自带换行，重载了 移位运算符，C++没有输出运算符

后续在Qt中，想打日志输出调试信息，可以使用qDebug，cout在Windows对于编码处理不太好，但是Linux上用utf8可以

使用qDebug还有一个好处就是，打印的调试日志是可以统一关闭的。输出的日志是在开发阶段，调试程序使用的，如果程序发布是不希望用户看到的。qDebug可以通过编译开关，来实现一键关闭。

调试器很多时候是有局限性的，很多时候是无法使用的。比如有些时候bug是一个概率性问题，出现的概率可能是1%甚至更小，这个时候想调试就要用日志，而不是调试器了。无论是哪种方式，本质上都是观察执行的中间过程和中间结果