## 图形化
分为单行输入框 ：QLineEdit和多行输入框：QTextExit
![](assets/QLineEdit/file-20260327004115257.png)

![](assets/QLineEdit/file-20260327004242144.png)

## 代码

```C++
#include "widget.h"
#include "ui_widget.h"

Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);
    QLineEdit* edit=new QLineEdit(this);
    edit->setText("hello world");
}

Widget::~Widget()
{
    delete ui;
}

```

![](assets/QLineEdit/file-20260327004349123.png)