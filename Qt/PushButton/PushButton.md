## 图形化
![](assets/PushButton/file-20260327010145507.png)
Qt提供了很多种功能的按钮，这里我们使用最基础的PushButton就可以了
![](assets/PushButton/file-20260327010312117.png)

当然现在这个按钮我们点击没有其他效果
connect() 函数是Q_OBJECT这个类提供的静态函数，这个函数的作用就是连接信号和槽
这里跟网络编程的connect没联系
```C++
connect()
```