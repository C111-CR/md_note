## vector

- `#include <vector>` 

- ```c++
  // 初始化
  vector<int> v1;				// 空vector
  vector<int> v2(n);			// 有n个值的vector，且每个值都为0
  vector<int> v3(n, val);		// 有n个值的vector，且每个值都为val
  
  vector<int> v4(v2) == vector<int> v4 = v2
  vector<int> v5{a, b, c} == vector<int> v5 = {a, b, c}
  ```

- ```c++
  // 增加and删除元素
  v1.push_back(x);
  v1.pop_back(x);
  ```

  push_back()函数，在不用扩增容量的情况下，时间复杂度是O(1)；但如果需要扩增容量，会将旧vector中所有元素复制到新的内存空间中，时间复杂度是O(n)，并且扩增后的容量为原来的m倍。

  假如从一个空vevtor开始，需要插入n次元素，下面推导其时间复杂度：

  对于第i次插入，其时间代价为：
  $$
  c_i=
  \begin{cases}
  i,~i-1是m的幂\\
  1,~其他情况
  \end{cases}
  $$
  则总代价为：
  $$
  \sum_{i=1}^{n}c_i~=~\sum_{j=0}^km^j+\big(n-(k+1)\big),~~~~~~k = \log_mn向下取整
  $$
  其中复制的次数为k+1（第一次插入从空vector开始也需要扩增，但不需要复制元素，复杂度为1），
  
  $\sum_{j=0}^km^j$ 为复制所需要的总时间代价，$\big(n-(k+1)\big)$ 为不用复制的插入所需要的总时间代价。
  
  有以下推导：
  $$
  \sum_{j=0}^km^j~=~m^0+m^1+...+m^k~=~m^{k+1}-1&~\leq~m^{k+1}
  \tag{1}
  \\
  $$
  
  $$
  m^{k+1}~=~m^k\cdot m\\
  m^k~=~m^{log_mn}~=~n
  \tag{2}
  \\
  $$
  
  即：
  $$
  \sum_{j=0}^km^j~\leq~n\cdot m
  $$
  
  
  又有 $\big(n-(k+1)\big)~\leq~n$ ，所以：
  $$
  \sum_{i=1}^{n}c_i~\leq~n~+~n\cdot m
  $$
  即时间复杂度约为 $O(n~+~n\cdot m)$ ，其中m为扩增容量的倍数。
  
  所以从空vector开始使用push_back()插入若干个元素的平均时间复杂度为 $O(m)$ 
  
  

- ```c++
  // 访问元素
  v2[3] = x;
  
  //使用迭代器
  vector<int>::iterator it = v2.begin();
  it[3] = x;
  ```

- ```c++
  // insert
  // 在任意位置之前插入元素
  v2.insert(v2.begin(), 1);
  
  // 在任意位置之前插入一组元素
  vector<int> v1{1,2,3,4};
  v2.insert(v2.begin(), v1.begin()+1, v1.end());
  
  v2.insert(v2.begin(), {1, 2, 3});
  ```

  ![img](https://img-blog.csdnimg.cn/img_convert/d5c3f1c28583919e9686dce526d94866.png)

- ```c++
  // erase
  v2.erase(v2.begin()+3);
  ```

  `insert` 和 `erase` 的时间复杂度都是 $O(n)$