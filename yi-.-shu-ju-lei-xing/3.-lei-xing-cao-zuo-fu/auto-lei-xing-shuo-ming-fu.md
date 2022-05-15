# auto类型说明符

C++11标准引入, 通过**auto**类型说明符让编译器替我们去分析表达式所属的类型

* 基本用法
  1. `auto i = 2.3;`中由于2.3作为常量通常为double类型, 所以auto为double
  2. 如果`val`为自定义类型, 那么`auto i = val;`中auto将推断为那个自定义类型
  3. 如果使用auto在一条语句中声明多个变量, 像`auto i = 0, *p = &i;`其中`i`和`*p`的类型必须相同, 例中i为int类型, p为指向int类型的指针, 相同. 如果像`auto sz = 0, pi = 3.14;`这样就是错误的, 因为sz为int类型, 而pi为double类型
*   常量const auto一般会忽略掉顶层const, 同时底层const会保留下来, 如:

    ```
    int i = 0;
    const int ci = i, &cr = ci;
    auto b = ci;                // b是一个int变量, ci的顶层const特性被忽略
    auto c = cr;                // c是一个int变量, 因为真正参与初始化的是ci
    auto d = &i;                // d是一个int类型指针
    auto e = &ci;               // e是一个指向整数常量的指针, 即无法更改指针指向的值, 但可以更改指针指向, 即底层const保留
    ```
* 初始值为引用时 当初始值为引用时, 真正参与初始化的其实是引用对象的值, 此时编译器以引用对象的类型作为auto的类型 如果要声明一个auto引用, 那么必须加上"&"声明符
*   auto类型的引用 当创建一个类型为auto类型的引用时, 初始值中的顶层常量属性仍然保留, 如:

    ```
    int i = 0;
    const int ci = i;
    auto &g = ci;           // 首先, ci中的顶层const属性保留, 所以g这个引用一定具有底层const属性, 等同于 const int &g = ci;
    const auto &j = 42;     // 但当auto引用绑定一个字面值时, 必须用const前缀
                            // 像 auto &j = 42; 这种就是错误的
    ```
