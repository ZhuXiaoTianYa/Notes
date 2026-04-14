# Cmake简单使用
## 1. CMake 介绍

CMake 是一个开源、跨平台的构建系统，主要用于软件的构建、测试和打包。

CMake 使用平台无关的配置文件 **CMakeLists.txt** 来控制软件的编译过程，并生成适用于不同编译器环境的项目文件。例如，它可以生成 Unix 系统的 Makefile、Windows 下的 Visual Studio 项目文件或 Mac 的 Xcode 工程文件，从而简化了跨平台和交叉编译的工作流程。CMake 并不直接构建软件，而是产生标准的构建文件，然后使用这些文件在各自的构建环境中构建软件。
> 虽然写cmake在生成makefile是绕了个圈子，但是相比于我们写的简单的makefile，cmake在生成的makefile会采用最优解，编译的效率会比我们简单些的高

### CMake 有以下几个特点：

- **开放源代码**：使用类 BSD 许可发布
- **跨平台**：并可生成编译配置文件，在 Linux/Unix 平台，生成 makefile；在苹果平台，可以生成 xcode；在 Windows 平台，可以生成 MSVC 的工程文件
- **能够管理大型项目**：KDE4 就是最好的证明
- **简化编译构建过程和编译过程**：CMake 的工具链非常简单：cmake+make
- **高效率**：按照 KDE 官方说法，CMake 构建 KDE4 的 kdelibs 要比使用 autotools 来构建 KDE3.5.6 的 kdelibs 快 40%，主要是因为 CMake 在工具链中没有 libtool
- **可扩展**：可以为 cmake 编写特定功能的模块，扩充 cmake 功能

## 2. CMake 安装

- **Ubuntu 22.04 安装 cmake**

```
Shell
sudo apt update
sudo apt install cmake
```

- **确定 cmake 是否安装成功**

```
Shell
cmake --version
```

![](assets/Cmake简单使用/file-20260414225938435.png)


## 3. 入门样例 - Hello-world工程
创建hello-world 目录， 并在其目录下创建main.cpp源文件和CMakeLists.txt文件
```Shell
zhutian@VM-0-10-ubuntu:~/repositories/gitee/cpp-wechat-im-microservice/example/cmake$ tree
.
├── CMakeList.txt
└── main.cc

1 directory, 2 files
```
 
 ```C++
 #include <iostream>

using namespace std;

int main()

{
    std::cout << "hello world" << std::endl;
    return 0;
}
 ```
  CMakeList.txt如下
  ```
# 声明所需要的cmake版本
cmake_minimum_required(VERSION 3.0.0)
# 定义项目工程名称(但是不是说生成的可执行程序就一定要叫这个)
project(test)
# 设置生成目标(第一个是生成的程序名称，后面为所依赖的cpp文件)
add_executable(main main.cc)
  ```
  > 与makefile不同，CMakeLists.txt中的CML需要大写，进行文件匹配

```
zhutian@VM-0-10-ubuntu:~/repositories/gitee/cpp-wechat-im-microservice/example/cmake$ mkdir build&&cd build
zhutian@VM-0-10-ubuntu:~/repositories/gitee/cpp-wechat-im-microservice/example/cmake/build$ cmake ..
CMake Deprecation Warning at CMakeLists.txt:2 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (1.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/zhutian/repositories/gitee/cpp-wechat-im-microservice/example/cmake/build
zhutian@VM-0-10-ubuntu:~/repositories/gitee/cpp-wechat-im-microservice/example/cmake/build$ ls
CMakeCache.txt  CMakeFiles  cmake_install.cmake  Makefile
zhutian@VM-0-10-ubuntu:~/repositories/gitee/cpp-wechat-im-microservice/example/cmake/build$ make
[ 50%] Building CXX object CMakeFiles/main.dir/main.cc.o
[100%] Linking CXX executable main
[100%] Built target main
```

- cmake .. 后面跟着的是CMakeLists.txt的所在路径
- 在进行简单的检测后生成makefile文件
- 生成的makefile是根据cmake的优化后生成的
- make就可以编译了
- 我们一般在cmake构建的时候，不会直接在我们的CMakeLists.txt所在目录去编译，这是因为编译的过程中会产生很多中间临时文件，所以一般会建立一个临时文件夹，如Build，在文件夹中cmake .. ，这样在进行清理的时候就可以直接删除这个文件夹
- cmake语句后面不能加; ,加了反而是报错

## 4.常见的cmake选项
### 设置cmake所需版本号
```
  cmake_minimum_required(VERSION 3.1.3) 
```
### 设置项目工程名称 
```
  CMake project(name) 
```
### 普通变量定义及内容设置 
```
  set(variable content1 content2 ...) 
  变量为空给"" 即可
  set(target "main")
```
###  设置C++特性标准
```
  set(CMAKE_CXX_STANDARD 17)
```
### 列表定义添加数据
```
set(variable_name "") 
list(APPEND variable_name content)

set(src_files "")
list(append src_file main.cc test.cc)
```
### 预定义变量

| 预定义变量名称                  | 备注                  |
| ------------------------ | ------------------- |
| CMAKE_CXX_STANDARD       | c++ 特性标准            |
| CMAKE_CURRENT_BINARY_DIR | cmake 执行命令时所在的工作路径  |
| CMAKE_CURRENT_SOURCE_DIR | CMakeLists.txt 所在目录 |
| CMAKE_INSTALL_PREFIX     | 默认安装路径              |
### 字符串内容替换 
```
string(REPLACE ".old" ".new" dest src)

set(src_files "")
list(append src_file main.cc test.cc)
set(obj_file "")
string(REPLACE ".cc" ".o" obj_file ${src_file})
# ${变量名} 就是引用变量中的值
```

### 添加头文件路径 
```
include_directories(path)

# 我们一般不会写include_directories("../test/") 这样的，因为这样的相对路径的起始位置就是在执行命令的地方，这样就很容易不对，我们一般用CMAKE_CURRENT_BINARY_DIR或CMAKE_CURRENT_SOURCE_DIR这样的预定义变量作为相对路径起始位置
```

### 添加链接库
```
target_link_libraries(target lib1 lib2 ...)

target_link_libraries(main -lspdlog -lfmt)
# 准确说这里添加的是动态库,target 是可执行程序的名称，后面库在添加的时候可以加-l 也可以不加，cmake会自己补上
```

### 添加生成目标 
```
add_executable(target srcfiles1 srcfile2 ...)

add_executable(main main.cc)
# 通常这个要放在target_link_libraries前面，先添加生成目标
```

### 错误或提示打印
```
message(FATAL_ERROR/STATUS content)

message(STATUS "普通提示")
message(FATAL_ERROR "致命错误--会导致cmake执行退出")
# cmake过程中可以先检测有没有一些库，针对有没有可以打印提示
```

### 查找源码文件
```
aux_source_directory(< dir > < variable >)
```