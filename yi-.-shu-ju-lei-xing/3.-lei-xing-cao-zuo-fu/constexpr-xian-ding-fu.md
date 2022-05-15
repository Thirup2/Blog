# constexpr限定符

*   常量表达式: 指值不会改变且在编译过程就能得到计算结果的表达式 一个对象或表达式是否是常量表达式由它的数据类型和初始值共同决定, 如:

    ```
    const int max_files = 20;       // max_files是常量表达式
    const limit = max_files + 1;    // limit是常量表达式
    int staff_size = 27;            // staff_size不是常量表达式
    const int sz = get_size();      // sz不是常量表达式, 其值直到运行时才能获取到
    ```

    由于在一个复杂系统中, 很难分辨一个初始值到底是不是常量表达式, 所以C++11添加了**constexpr**类型
*   constexpr变量 将变量声明为**constexpr**类型以便由编译器来验证变量的值是否是一个常量表达式, 声明为constexpr的变量一定是一个常量, 而且必须用常量表达式初始化, 也就是说, 如果用非常量表达式进行初始化, 编译器会报错, 如上的例子则不会报错

    ```
    constexpr int mf = 20;          // 20是常量表达式
    constexpr int limit = mf + 1;   // mf + 1是常量表达式
    constexpr int sz = size();      // 只有当size是一个constexpr函数时, 才是一条正确的声明语句
    ```
* 字面值类型 在声明constexpr时用到的类型必须是"字面值类型"
  1. 算数类型, 引用和指针都属于字面值类型
  2. 自定义类, IO库, string类型不属于字面值类型, 所以不能被定义成constexpr
* constexpr与指针
  1. 在constexpr声明中如果定义了一个指针, 限定符constexpr仅对指针有效, 对指针所指的对象无关, 而用`const int * p = &r;`中的const则是限定的指针所指的对象
  2. 能作为指针字面值的必须是**nullptr**或者**0**, 或者是存储与某个固定地址中的对象
