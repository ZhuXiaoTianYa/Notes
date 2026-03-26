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