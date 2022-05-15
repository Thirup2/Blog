# decltype类型指示符

为了满足: 希望从表达式的类型推断出要定义的变量的类型, 但是不想用该表达式的值初始化变量.

* 作用: 选择并返回操作数的数据类型
* 用法
  1. `decltype(f()) sum = x;`编译器将函数f的返回类型作为sum的类型
  2. `decltype(sum) n = x;`编译器返回变量类型
  3. `decltype(sum+0) m =x;`编译器返回表达式运算结果对应的类型
* 注意点
  1.  如果decltype使用的表达式是一个变量, 则decltype完全返回该变量的类型(包括顶层const和引用在内):

      ```
      const int ci = 0, &cj = ci;
      decltype(ci) x = 0;         // x的类型是const int
      decltype(cj) y = x;         // y的类型是const int &, y绑定到变量x
      decltype(cj) z;             // 错误: z是一个引用, 必须初始化
      ```
  2. 有些表达式奖项decltype返回一个引用类型
     1. 当括号内是一个引用类型时
     2.  当表达式是一个解引用操作时

         ```
         int i = 42, *p = &i, &r = i;
         decltype(*p) c;     // 这条语句decltype将返回引用类型, 所以必须初始化, 所以这条语句错误
         ```

         该如何理解呢, 乍看`*p`是一个int类型, 所以decltype应该返回int类型, 但其实`*p`实际上是一个对i的引用
     3.  当括号内的表达式再加上了一对括号, 那么decltype返回的结果永远是引用类型

         ```
         decltype(i) d;      // decltype返回int类型
         decltype((i)) e;    // decltype返回int &, 必须初始化
         ```
