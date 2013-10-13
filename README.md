# C++

## `extern`用途
1. `extern` + 变量/函数声明
   * 告诉编译器该变量/函数在后面/其它cpp定义
   * 注意是声明，不要初始化
   * `#include`也可达到同样目的，但`extern`可加速编译
2. `extern "C"` + 函数
   * 告诉链接器用C函数规范来链接，以便在C++中调用C库函数
   * 因C++支持函数重载，函数被C++编译后在符号库中的名字与C不同

## `static`用途
1. 修饰局部变量
   * 使变量存放在静态数据区，生命周期持续到程序结束，作用域不变（仍然是局部变量）
   * 只在初次运行时进行初始化工作，且只进行一次
2. 修饰全局变量/函数
   * 使变量/函数作用域由原来的整个工程可见（用`extern`即可）变为本源文件可见
3. 修饰类成员变量
   * 使变量为所有对象共享，相当于类范围内的全局变量
   * 必须初始化（例如`int A::s_value = 0;`），且将在`main`前执行
4. 修饰类成员函数
   * 相当于类范围内的全局函数
   * 木有this指针，只能访问类的静态成员
   * 实现不需要`static`修饰

## `const`用途
1. 声明常量     
2. 声明函数形参
3. 声明函数返回值
   * 返回指针时，可防止返回的指针内容被修改
   * 按值返回时则没必要加`const`，例如不要写成`const int foo()`
4. 修饰类成员函数
   * 防止成员函数对成员变量进行修改
   * 只能调用类的其它const成员函数
   * 如果要在const成员函数修改某个成员变量，那么可以给这个变量的声明加上`mutable`

## `sizeof`
* 作用：返回一个对象或者类型所占的内存（不计算类/结构体的static成员）
* 如果类中有虚函数，那么最终结果要多加4Byte（多出一个指向虚函数表的指针）
* 对空类使用sizeof会返回1（占用内存为0，无法实例化，所以编译器为了使空类也能实例化，就给其分配了1Byte）
* 例
  ```
  static int a;         //sizeof(a) = 4
   
  class Zero {
       int a;
       static int b;
       virtual void fun() {}
  };                    //sizeof(Zero) = 8
   
  class Lin {
       virtual void fun() {}
  };                    //sizeof(Lin) = 4
   
  class Child: public Zero {
       int a;
       virtual void fun() {}
  };                    //sizeof(Child) = 12
  ```

## C++风格类型转换
* const_cast<type>(varible): 去掉变量的const或volatile属性
* static_cast<type>(varible): 类似C的强制转换，但功能上有所限制(如：不能去掉const属性)    
  * 常用于基本类型转换，void指针与目标指针转换
* dynamic_cast<type>(varible): 有条件转换，运行时进行类型安全检查(如果失败则返回NULL)    
  * 用于基类与子类之间的类型转换(必须要有虚函数)
* reinterpret_cast<type>(varible): 仅仅重新解释二进制内容 
  * 常用于不同类型的指针转换

## macro
* 别忘了括号
* 返回较小者：```#define MIN(a, b) ( (a) < (b) ? (a) : (b) )```或```#define MIN(a, b) ( fabs((a)-(b))==((a)-(b)) ? (b) : (a) )```

## other
1. `inline`必须与函数定义放在一起才能使函数成为内联，仅将`inline`放在函数声明前面不起任何作用
2. 在声明赋值语句中，变量先声明，然后赋值

   ```
   int i = 1;
   int main() {
        int i = i;     //这里声明的i 覆盖了全局变量的i，
                       //之后的赋值就是局部变量的i 给自己赋值，因而其值是未定义的
        return 0;
   }
   ```
3. x86是小端，低地址存放低字节（例如，0x1234在内存中可能这么存：地址0x4000存0x34，地址0x4001存0x12）


# Algorithm
