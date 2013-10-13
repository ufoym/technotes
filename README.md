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

## 虚函数
* 构造函数不可以为virtual，析造函数有时必须为virtual
  * 虚函数在运行时动态绑定，动态绑定需要知道动态类型才能进行绑定。而一个类的对象在没有运行构造函数前，其动态类型不完整，因而无法进行动态绑定
  * delete指向子类对象的基类指针时，如果析构函数不是virtual，则只会调用基类的析构函数，从而造成内存泄漏

## 类型转换
* const_cast<type>(varible)
  * 去掉变量的const或volatile属性
* static_cast<type>(varible)
  * 类似C的强制转换，但功能上有所限制(如：不能去掉const属性)    
  * 常用于基本类型转换，void指针与目标指针转换
* dynamic_cast<type>(varible)
  * 有条件转换，运行时进行类型安全检查(如果失败则返回NULL)    
  * 用于基类与子类之间的类型转换(必须要有虚函数)
* reinterpret_cast<type>(varible)
  * 仅仅重新解释二进制内容 
  * 常用于不同类型的指针转换
  
```
class Base {
public:
     virtual void fun() {}
};
class Zero: public Base {
public:
     virtual void fun() {}
};
int main() {
     Base *pb = new Base;
     (dynamic_cast<Zero*>(pb)) -> fun();     //由于实际指向的是基类，所以此转换失败，返回NULL，导致运行时错误
     delete pb;
     return 0;
}
```

## 不用XXX，实现YYY
* 不用循环，判断一个数X是否为2的N的次方(N >= 0)
  ```X & (X - 1)     //最后结果为0，则是2的N次方```
  分析：2，4，8的二进制式为10, 100, 1000，当其与1, 3, 7的二进制式相与时，其结果为0
* 不用判断语句，找出两个数中的最大值
  ```((a + b) + abs(a - b)) / 2```
* 不用中间变量，交换两者的值
  ```
  a = a ^ b;     //得到a与b的哪些位不相同（即一共有多少位不同）
  b = a ^ b;     //去掉b的不同位，换上a的不同位，从而得到a
  a = a ^ b;
  ```

## 指针
* 与引用的差异
  * 初始化：指针可以不初始化；引用必须在定义时初始化
  * 非空性：指针可以为NULL，因而指针可能是无效的；引用不能为空，因而引用总是有效的
  * 内存占用：指针要占用内存，引用不占 （引用只是一个逻辑概念，通常在编译时就优化掉了）
  * 可修改性：指针可以重新指向其它变量；引用不可以
  * 《Effective C＋＋》：在一般情况下，引用和指针是一样的，但是根据条款23：在返回一个对象时，尽量不要用引用，而是用指针

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
4. 当成员变量有指针时，最好考虑是否要写深拷贝函数和赋值函数
5. 在容器中增加/删除元素时，可能会使部分或者全部的迭代器失效
6. C++编译器默认产生的成员函数：构造函数，析构函数，拷贝函数，赋值函数
7. 类特殊变量的初始化
    * 静态成员变量一定要在类外初始化
    * 成员常量一定要在初始化列表初始化
    * 静态成员常量一定要初始化，初始化时可以直接在声明时写上，也可以像一般的静态成员变量那样写（?）
    
    ```
    class Zero {
    public:
         Zero():lin2(1) {}
    private:
         static int lin;
         const int lin2;
         static const int lin3 = 1;
    };
    int Zero::lin = 0;
    ```
8. 宏别忘了括号，例如```#define MIN(a, b) ( fabs((a)-(b))==((a)-(b)) ? (b) : (a) )```
9. C++编译的程序占用的内存
    * stack(栈区): 由OS自动分配释放空间，主要用来存放函数的参数，局部变量等
    * heap(堆区): 一般由程序员分配释放空间， 若程序员不释放，程序结束时可能由OS回收
    * static(全局/静态区): 存放全局变量和静态变量。值得注意的是，初始化和未初始化的这两种变量是分开存放的。
    * 文字常量区: 存放常量字符串
    * 程序代码区: 存放函数的二进制代码


# Algorithm
