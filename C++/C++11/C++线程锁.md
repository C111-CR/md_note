## C++线程锁

- semaphore（信号量）, mutex（互斥锁）, condition_variable（条件变量）

  - mutex和condition_variable是由C++给出的用于支持线程互斥与线程同步，而信号量(semaphore)是在Windows和Linux库中给出的，信号量可以通过mutex和condition_variable实现。

  - mutex使用方法

    - `std::mutex mtx` 创建

    - `mtx.lock()`与`mtx.try_lock()` （`mtx.unlock()` 解锁）

      互斥量可能有三种情况：未被锁住，被其他线程锁住，被当前线程锁住

      第一种情况，两者都直接给互斥量上锁；第二种情况，`lock`会使当前线程一直阻塞在请求互斥量的状态，而`try_lock`会返回false，不会阻塞；第三种情况，会产生死锁。

    - 以上都是直接使用mutex，而C++有两个封装类来操作mutex

      - `std::lock_guard`：当创建`lock_guard`时，其构造函数需要传入一个mutex互斥量，在构造时就会尝试对互斥量上锁；而当程序离开其作用域，执行其析构函数时，就会完成互斥量的解锁。

        ```C++
        std::mutex mtx;
        void func(){
        	std::lock_guard<std::mutex> lock(mtx); // 这是一个模板类
        	// 此时这个互斥量已经被上锁
        	...
            return ;
            // 当程序离开这个函数时，lock_guard执行析构函数，就会解锁互斥量
        }
        ```

      - `std::unique_lock`：同`lock_guard`相比，更灵活。

        - 创建时可以不加锁（当然，默认还是创建即加锁），并且可以随时随地加锁解锁。
        - 析构时自动解锁
        - 与条件变量配合使用
        - 不能复制构造，可以使用`std::move`进行移动构造

        ```c++
        using namespace std;
        std::mutex mtx;
        std::unique_lock<mutex> locker(mtx);		// 创建，并给mtx加锁
        std::unique_lock<mutex> locker(mtx, std::adopt_lock); 
        	// 第二个参数std::adopt_lock 
        	// 表示mtx已经被上锁，构造时就不会去上锁(当然，mtx要真的是被上锁了，不然会报异常)
        std::unique_lock<mutex> locker(mtx, std::try_to_lock);
        	// std::try_to_lock 会尝试上锁，没锁住也会返回
        std::unique_lock<mutex> locker(mtx, std::defer_lock);
        	// std::defer_lock	构造时初始化一个不上锁的mutex（当然，mtx不能已经被锁住）
        ```

        ```c++
        locker.lock();
        locker.try_lock();
        locker.unlock();
        std::mutex *mtx = locker.release();	
        	// 区别于解锁，release说明unique_lock释放mutex的所有权了，返回原始mutex的指针
        ```

  - condition_valiable使用方法

    ​	`std::condition_variable cond` 

    - wait操作

      ```c++
      void wait( std::unique_lock<std::mutex>& lock );
      	// 做两件事：给lock解锁，程序阻塞到这一行直到别的线程调用cond.notify_one()唤醒它
      	// 被唤醒后做一件事：不断尝试获取lock直到获取到lock，然后往下执行
      template< class Predicate >
      void wait( unique_lock<mutex>& lock, Predicate pred );
      eg:
      cond.wait(locker, [this] {if (!xxx) return true;
                                else return false;
                               }) ;
      	// 当lambda表达式返回true，则直接返回（没有解锁互斥量）
      	// 当lambda表达式返回false，则给locker解锁，直到被cond.notify_one()唤醒，
      	// 		而唤醒后，也会不断尝试获取locker，直到获取到之后还要再判断lambda表达式，
      	// 		若还是false，则继续重复那个步骤：给locker解锁，休眠，直到被唤醒，重复...
      ```

    - notify操作

      `notify_one()` 通知一个线程的wait()

      `notify_all()` 通知所有线程的wait()





















