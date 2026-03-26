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