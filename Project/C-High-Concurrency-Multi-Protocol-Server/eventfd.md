eventfd：一种事件通知机制
创建一个文件描述符用于实现事件通知
eventfd本质就是在内核中管理一个计数器
每当创建eventfd就会在内核中创建一个计数器（结构）
这个结构体较小，效率比较高
每当向eventfd中写入一个数值--用于表示事件的通知次数
可以用read进行数据读取，读取到的数据就是通知的次数

假设每次给eventfd中写一个1，就表示通知了一次，连续写三次之后，再去read读取出来的数字就是3，读取后计数清零
```C++
       #include <sys/eventfd.h>

       int eventfd(unsigned int initval, int flags);
       initval:计数初始值
       flags:EFD_CLOEXEC - 禁止进程复制
	         EFD_NONBLOCK - 启动非阻塞属性
	    返回值：返回一个文件描述符用于操作
```
eventfd也是通过read/write/close进行操作的
注意：read&write进行IO的时候数据只能是一个8字节数据
```C++
#include <sys/eventfd.h>
int main()

{

    int efd = eventfd(0, EFD_CLOEXEC | EFD_NONBLOCK);

    if (efd < 0)

    {

        return 1;

    }

    uint64_t val = 1;

    write(efd, &val, 8);

    write(efd, &val, 8);

    write(efd, &val, 8);

    uint64_t num = 0;

    read(efd, &num, 8);

    std::cout << num << std::endl;

    return 0;

}
```
