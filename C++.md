# C++
***

推荐书籍：

- 《primer》/《primer plus》（入门必看）；

- 牛客网C++面试真题题库（全面但不深入，可作为知识点提纲阅读）；

- 《深度探索C++对象模型》（详细剖析了类的构造析构，虚函数多态等相关知识，这一块在C++面试中必问）；

- 《STL源码剖析》（建议结合视频阅读，书很难啃，但侯捷课讲得不错）；

- 以上对付面试就够了，更加深入还有《modern effective C++》等书籍。

***

1.动态/静态类型

  - 静态类型：对象在声明时采用的类型，编译期确定。

  - 动态类型：一个指针或引用所指向的类型，运行期确定。
  
  - 静态绑定：绑定的是静态类型，所对应的函数或属性依赖于对象的静态类型，编译期确定。
  
  - 动态绑定：绑定的是动态类型，所对应的函数或属性依赖于对象的动态类型，运行期确定。
  
  - 非虚函数一般为静态绑定，虚函数一定为动态绑定（多态特性）。对象的动态类型可以更改，静态类型无法更改。在继承体系中，只有虚函数使用的是动态绑定，其他的是静态绑定。不要重新定义继承而来的非虚函数（virtual与缺省参数一起使用时注意动态和静态绑定），如下伪代码：
  
  ```C++
  class E
  {
      virtual func(int i=0);
  };
  class F:public E
  {
      virtual func(int i=1);
  };
  int main()
  {
      E* pf=new F();
      E* pe=pf;
      pe->func(); //此时调用子类F的func函数，但是使用基类E的默认参数，即 i=0
      //函数为动态绑定，但缺省参数为静态绑定
  }
  ```

2.拷贝构造函数
  
  - 对于一个类X，如果一个构造函数的第一个参数是下列之一，且没有其他参数或者其他参数都有默认值，那么这个函数就是拷贝构造函数，
  X&, const X&, volatile X&, const volatile X&。
  
  - 一个类中可以存在多于一个的拷贝构造函数。
  
  - 如何防止默认拷贝的发生：声明一个私有拷贝构造，此时当用户试图按值传递时，发生编译错误。（扩展：单例模式、右值引用）
  
  - 当出现类的等号赋值时，会调用拷贝函数，在未定义显示拷贝构造函数的情况下，系统会调用默认的拷贝函数——即浅拷贝，它能够完成成员的一一复制。当数据成员中没有指针时，浅拷贝是可行的。但当数据成员中有指针时，如果采用简单的浅拷贝，则两类中的两个指针将指向同一个地址，当对象快结束时，会调用两次析构函数，而导致指针悬挂现象。所以，这时，必须采用深拷贝。深拷贝与浅拷贝的区别就在于深拷贝会在堆内存中另外申请空间来储存数据，从而也就解决了指针悬挂的问题。简而言之，当数据成员中有指针时，必须要用深拷贝。
  
  关于深浅拷贝、参数传递以及拷贝构造的使用[详见引用](https://www.cnblogs.com/alantu2018/p/8459250.html)。
  
3.空结构体大小为1，枚举为int类型，大小为4。

4.什么时候会生成默认构造函数？

  - 如果一个类没有任何构造函数，但它包含一个成员对象（有默认构造函数），那么该类的隐式默认构造函数就有用（nontrival），编译器会为其合成默认构造函数，该构造会在真正被需要时调用。
  
  - 当一个类声明或继承一个虚函数时
  
  - 当一个类派生自一个集成串链，其中有一个或更多的虚基类。
  
  更多关于bitwise copy semantics的内容详见《深度探索C++对象模型》。
  
5.C++内存管理

  分区：堆、栈、自由存储区、全局/静态存储区、常量存储区。
  
  堆：new分配，delete释放/程序结束后系统回收。
  
  栈：存放局部变量，函数结束后回收。
  
  自由存储区：malloc/free。
  
  全局/静态存储区：C语言中又分为初始化和未初始化区域（.data, .bss）。
  
  常量区：不允许修改。
  
6.堆和栈的区别

  在32位系统中：

  - 堆的大小为4G，栈为1M。
  
  - 堆使用new/delete或malloc/free分配和释放内存，造成空间不连续。栈是一个先进后出的队列，通过编译器静态/动态分配。
  
  - 堆向上，向高地址方向增长。栈向下，向低地址方向增长。
  
  - 堆由C/C++函数库提供，效率较低。栈操作有相应的指令，使用寄存器效率较高。
  
7.野指针

  - 指针未初始化
  
  - 被free或delete后，未置NULL
  
  - 操作超过了变量的作用域范围
  
8.malloc/free，new/delete区别
  
  malloc/free是C++/C的标准库函数，(int*)malloc(sizeof(int)*length);
  
  new/delete是C++的运算符；
  
  如果用new创建对象数组，那么只能使用对象的无参构造函数。
  
9.main函数的返回值->int类型

  若main（）函数中没有显示return，编译器加上return int，返回0则表示成功，非0则函数执行失败。
  
  ```C++
  ./a.out && echo "Yes"
  在返回0的情况下输出：
  Yes
  在非0的情况下无输出
  ```
  
10.debug和release的区别

  调试版/发行版
  
  - 运行方式不同，debug增加调试代码，出错会定位。
  
  - 内存分配不同，debug自动初始化变量，release不会。
         
                debug以32位为单位分配，release以8位为单位。
  
  - assert在release中不编译，应使用verify。
  
  * 多线程情况下，调试会产生竞态（race condition）问题，因为debug包含了一些调试信息，会影响多线程的竞争状态，在有断点或单布运行时，一个线程停了另一个继续，这会破坏程序的并发性。
  
11.STD::bind

  - 普通函数，std::bind(func,_1,2);
  
  - 成员函数
  
  ```C++
  struct foo
  {
    void func();
  };
  foo F;
  std bind(&foo::func,&F,_1,2);
  ```
  
  - 绑定一个引用参数
  ```C++
  vector<string> words;
  ostream &print(ostream& os,const string& s,char c)......
  ostringstream os1;
  for_each(words.begin(),words.end(),bind(print,ref(os1),_1,c);
  ```
  
  ostream不能深拷贝，若希望传给bind一个对象而不拷贝它，使用ref()。
  
12.std::function

  ```C++
  int add(int a,int b)
  {
    return a+b;
  }
  auto mod=[](int a,int b){return a%b;};
  struct divide{int operator()(int a,int b){return a/b;}};
  
  std::function<int(int,int)> a=add;
  std::function<int(int,int)> b=mod;
  std::function<int(int,int)> c=divide;
  ```
  
13.指向成员函数的指针

  ```C++
  struct foo
  {
    void f();
    void g();
  };
  void apply(foo* foo1,foo* foo2,void(foo::*fun))
  {
    (foo1->*fun)();
    (foo2->*fun)();
  }
  int main()
  {
    foo foo1{1};
    foo foo2{2};
    apply(&foo1,&foo2,&foo::f);
    apply(&foo1,&foo2,&foo::g);
  }
  ```

14.函数调用，栈变化
  
  1. 在栈帧里开好被调函数形参所需空间
  
  2. 返回地址压栈（回收1中为形参开辟空间指令的地址）；
  
  3. 进入被调函数，调用函数栈底地址压栈；
  
  4. 在新函数的当前栈帧内为局部变量分配空间后，局部变量压栈；
  
  5. return之后：
  
    1. 返回值给eax寄存器；
    
    2. 回收局部变量空间（esp指向调用函数栈的栈顶）；
    
    3. 提前存好的外部函数的栈底地址进入ebp寄存器；
    
    4. 返回地址填入eip寄存器，回收开辟空间的指令地址；
    
    5. 回收形参空间。
    
  - debug版本会压入临时对象。
  
15.内存泄漏

  - new,delelte导致：堆中没释放/类的动态申请，析构没释放。
  
  - 没正确清除嵌套对象的指针。
  
  - 释放数组时，delelte没加[]，会被认为指向对象。
  
  - 指针数组，不仅要释放对象空间，还要释放每个指针的空间。
  
  - 缺少拷贝构造函数：按值传递会调用拷贝构造，引用的话不会调用。两次释放相同内存呢导致堆崩溃。当类中有指针变量成员，在进行隐式拷贝时，以逐个成员拷贝，两个对象会拥有指向同一个动态分配的内存空间的指针，释放第一个对象时，析构函数会释放该对象的内存空间，释放第二个对象时，析构函数会释放相同的内存，从而导致内存泄漏。所以，当类中有指针成员变量时，要么显示构造和重载赋值运算符，要么禁用拷贝构造函数和重载赋值运算符。
  
  - 缺少重载运算符函数，理由同上。
  
  - 基类的析构函数不为虚函数。
  
  - nonmodifying运算符重载
  
    1. 返回栈上对象的引用或指针，导致返回空引用或指针。
    
    2. 内部静态对象的引用。
    
    3. 返回一个泄露内存的动态分配的对象，解决方式：重载运算符的返回值不是类型的引用，而是类型，即不是int&，而是int。
    
  - 系统资源没有得到正确释放，例如socket,handle...。
  
16.STL空间配置

  [SGI STL空间配置器](https://blog.csdn.net/u012481976/article/details/82811623)
  
  [STL一级空间配置器](https://blog.csdn.net/qq_37964547/article/details/80474316)
  
17.sizeof,strlen区别

  - sizeof计算空间是及分配的字节数，strlen计算字符个数，不包含'\0'
  
  - sizeof是运算符，strlen是函数，且输入参数必须包含'\0'
  
  - sizeof编译时确定，即不能计算动态的（目前新版C++好像可以计算动态了），strlen运行时确定
  
18.如何解决哈希冲突

  1. 开放定址法
    
    - 线性探查
    
    - 二次探查
    
  2. 链地址：哈希桶
  
  3. 公共溢出区
  
  4. rehash
  
19.内置类型的构造

  对内置类型而言，new仅仅表示分配内存，加上（）才调用构造函数
  
  对自定义类型而言，只要一调用new，编译器不仅分配内存而且执行构造
  
20.不能被声明为虚函数的函数

  - 普通函数（非成员函数）
  
    只能被重载，不能被重写，声明为虚函数毫无意义，编译器在编译时会绑定函数
    
  - 构造函数
  
    从语义上考虑，构造函数本来就是为了明确初始化对象成员才产生的，然而虚函数主要是为了在不完全了解细节的情况下也能正确处理对象。另外，虚函数是在不同类型的对象产生不同的动作，现在对象还没有产生，使用虚函数来实现动作是一种悖论。
    
  - 内联函数
  
    内联函数的作用就是在代码中直接展开，减少函数调用花费的代价，虚函数是为了在继承后对象能够准确执行自己的动作，这是不可能统一的。并且，内联函数在编译时被展开，虚函数在运行时才能动态绑定函数。
    
  - 静态成员函数
  
    静态成员函数对于每个类来说只有一份代码，所有的对象都共享这一份代码，没有动态绑定的必要性。
    
  - 友元函数
  
    C++不支持友元函数的继承，对于没有继承特性的函数没有虚函数的说法。
    
21.void fun() const代表什么

  1. 必须为类的成员函数
  
  2. 不能修改成员变量
  
  3. 不能调用非const函数
  
  4. 其他const函数可以调用它（非const也可以）
  
  5. 只能从xonst对象上调用（引用，指针）   //const A a;a.fun();

22.const

  1. 只有字面量初始化的const常量才会进入符号表
  
  2. 使用其他变量初始化的const常量仍为只读变量
  
  3. 被volatile修饰的const常量不会进入符号表
  
  4. const引用的变量与初始化变量相同，初始化变量成为只读变量
  
  5. const引用的变量与初始化变量不同，新生成一个只读
  
  ```C++
    cosnt int x=1;                    //x进入符号表，x=1
    
    const int& rx=x;                  //rx为只读变量,rx=5
    
    int& nrx=const_cast<int&>(rx);    //nrx为rx去只读后的引用，nrx=5
    
    nrx=5;
    
    volatile const int y=2;           //y不进入符号表，y=6
    
    int* p=const_cast<int*>(&y);      //p=6
    
    *p=6;
    
    const int z=y;                    //编译器能否确定它的值来确定它是否为常量，z=7
     
    p=const_cast<int*>(&z);
    
    *p=7;
    
    char c='c';                       //c为变量,c=a
    
    char& rc=c;                       //rc=a
    
    const int& trc=c;                 //c为char，trc为int，故新生成一个只读变量，trc=c
    
    rc='a';
  ```

  - 常量指针
  
    const int* p = &a;
    
    p=p+1;  //正确
    
    *p=16; //错误
    
  - 指针常量
  
    int* const p=&a;
    
    p=p+1;   //错误
    
    *p=16;  //正确

23.new,operator new,placement new

  new的步骤，语句Foo* p = new Foo();为例：
  
  1. Foo* p;
  
  2.void* raw=operator new(sizeof(Foo));
  
  3.try{
  
      p=new(raw) Foo();
      
    }catch(.......){
    
      operator delete(raw);
      
      throw;
      
    }
   
   - operator new默认调用分配内存的代码，尝试得到一段堆的空间，若失败就调用new_handler继续尝试。
   
   - placement new调用对象的构造函数生成类对象
   
   
24.模板函数：提供了一类函数的抽象，它提供了任意类型为参数及返回值，函数模板经过实例化生成的具体函数成为模板函数，函数模板代表了一类函数，模板函数表示具体函数。

25.类型转换函数：将当前类的类型转为其他类型，operator type(){......return data;}

  - type可以是内置类型，类类型，typedef定义的类别名，任何可作为返回类型的（除去void)，一般而言，不会允许转为数组或函数，可以是指针或引用。
  
  - 一般不会更改被转对象，将其定义为cosnt成员。
  
  - 可以被继承，可以是虚函数。
  
  - 可以有多个（类似重载），但若多个要转的目标类型本身就可以互相转换，有时会引发二义问题，如：int,double。
  
26.隐式转换规则：

  ```C++
  struct Exp{explicit Exp(const char*){}};
  Exp e1("abc");    //正确
  Exp e2="abc";     //错误，拷贝初始化不考虑显示构造
  
  struct Imp{Imp(const char*){}};
  Imp i1("abc");    //正确
  Imp i2="abc";     //正确
  
  struct S{S(std::string){}};
  S s("abc");       //正确，const char[4]转为string
  S s="abc";        //错误，const char[4]无法转为S
  S s="abc"s;       //正确，std::string 转为S
  ```
  
  * 引申问题：深拷贝，浅拷贝
  
27.Java,volatile关键字，内存回收机制

  volatile:表示不会只在工作内存工作，会写入主存，保证内存可见性。工作内存修改完后写回主存，一致性只能靠原子性保证。

  [JVM的内存区域和垃圾回收机制](https://blog.csdn.net/qq_34760508/article/details/80930508)
  
28.throw与return的区别

  当f1调用f2时，return直接返回至调用f2的后一条语句，throw会沿函数调用回溯，直到找到直/间接包含对f2调用的try语句，将执行下一个匹配的catch。
  
29.C++11新特性

  1. 原子变量
  
  ```C++
  atomic_int num{0};
  num++;              //原子
  ```
  
  2. lambda表达式
  
  ```C++
  auto fun=[](const char* str){cout<<str<<endl;};
  thread t1(fun,"hello world!");
  thread t2(fun,"hello beijing!");
  ```
  
  3. 可变参数列表
  
  ```C++
  int show(const char* fun,...)
  {
    va_list ap;
    va_start(ap,fun);
    vprintf(fun,ap);
    va_end(ap);
    return 0;
  }
  int main()
  {
    thread t1(show,"%s %d %c %f","hello world",100,'A',3.14);
    return 0;
  ```
  
  4.移动语义move
  
  ```C++
  thread t1([](){cout<<"thread 1"<<endl;});
  cout<<"thread 1 id is"<<t1.get_id()<<endl;
  thread t2=move(t1);
  cout<<"thread 2 id is"<<t2.get_id()<<endl;
  return 0;
  ```
  
30.

- 虚继承

  主要解决在多重继承时，基类可能被多次继承，虚基类主要提供一个基类给派生类。
  
  当基类为虚基类时，只拷贝一次虚基类。若派生类继承自多个基类，有虚和非虚两种，先调用虚基类的构造，在调用非虚基类，最后调用派生类构造。
  
- 虚表

  虚表在编译期确定，而类对象的虚函数指针在运行时确定。
  
  构造中可以用虚函数吗？原理上可以，但是派生类对象构造期间进入基类的构造时，对象类型为基类类型，所以虚函数始终仅仅调用基类的虚函数，毫无多态效果。
  
  一个类构造时，创建虚表，整个类共有。
  
  eg:
  
  class Base
  
  class Derive: Base
  
  虚表表示为：
  
  无继承：Base::f()|Base::g()|Base::h()|Derive::f1()|Derive::f2()|Derive::h1()
  
  继承：Derive::f()|Base::g()|Base::h()|Derive::g1()|Derive::h1()
  
  多继承：
  
  Derive::f()|Base1::g()|Base1::h()|Derive::g()
  
  Derive::f()|Base2::g()|Base2::h()
  
  Derive::f()|Base3::g()|Base3::h()
  
  顺序：
  
  1. 子类调用基类构造
  
  2. 基类设置vptr
  
  3. 基类初始化列表，进行构造
  
  4. 子类设置vptr
  
  5. 子类初始化列表，进行构造
  
