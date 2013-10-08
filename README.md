Ming's Tech Notes
=================

## C++
### extern用途
1. extern + 变量/函数声明：告诉编译器该变量/函数在后面/其它cpp定义 （#include也可达到同样目的，但extern可加速编译）
2. extern "C"：告诉链接器用C函数规范来链接，以便在C++中调用C库函数
