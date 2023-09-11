## const

- 基础用法

  用于声明一个不可再被修改的变量：

  ```c++
  const int num = 17;
  num = 33;				// Invalid,会报错
  ```

- 用于指针

  首先，如果将一个指针用`const`声明为常量指针，那么这个指针的指向将不能被改变。但可以通过这个指针来修改被指向的对象：

  ```c++
  int demo = 17;
  int* const p = &demo;	// 这是一个指向非常量对象的常量指针
  p++;					// Invalid
  *p = 33;				// Valid
  ```

  区别于下面这种情况：一个指向`const int`类型变量的指针。

  这种情况下，p的指向可以被改变，但无法通过p来改变demo的值。

  ```c++
  const int *p = &demo;	// 这是一个指向常量对象的非常量指针
  p++; 		 			// Valid
  *p = 32;				// Invalid
  ```

  从上面可以看出，指向`const int`类型的指针也可以用来指向`int`类型的变量，即被指向的变量不需要用`const`声明。

  而当想要用指针指向一个常量对象时，指针也必须得是用`const`声明（此时指针指向一个常量对象，但本身不需要是一个常量指针）。其原因是：如果`demo`被`q`指针指向，那么便可以通过`q`指针修改`demo`，这样将`demo`定义为常量对象就毫无意义了。

  ```c++
  const int demo = 13;
  const int *p = &demo;	// Valid
  int *q = &demo;			// Invalid,可以通过以下代码修改demo的值:
  *q = 17;				// 当然，这行代码无法通过编译器
  ```

- 用于函数

  - 用于参数

    首先，如果想要传入一个`const`类型的实参，那么形参必须也得是`const`类型：

    ```c++
    int sum(int arr[], int num){}
    const int a[10] = {...};
    sum(a, num);					// Invalid
    ```

    因此，当不需要在函数中修改参数时，最好使用`const`定义形参。这样既可接受`const`类型的实参，也可接受非`const`类型的实参。否则，只能接受非`const`类型实参：

    ```c++
    const int a[10];
    int b[10];
    
    int sum(int arr[]);
    sum(a);						// Invalid
    sum(b);						// Valid,在这种方法下,数组b可以被修改
    
    int sum(const int arr[]);
    sum(a);						// Valid
    sum(b);						// Valid,在这种方法下,数组b不可以被修改
    ```

  - 用于整个函数

    在函数声明后面跟上 `const` 关键字，表明这个函数不会修改当前对象（用于成员函数）。

    ```c++
    class tmp{
        void show() const;
    };
    void tmp::show() const{}
    ```

    













