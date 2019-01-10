# 快速上手

## Best Practices

 - Input arguments to function are either values or `const` reference. We never allow non-`const` references, except when required by convention, e.g. `swap()`.

 - We use pointers to pass output arguments to function. (This motivated by not wanting readers to have to go to the function source code to see if an argument is updated by the called function.)

 - We use `dequeue<bool>` for Boolean arrays, rather than `vector<bool>`. The latter is not an STL container, and does not really hold `bools`. For example, `bool *pb = &A[0];` will not compile if `A` is of type `vector<bool>`.

## C++11 constructs

- The `auto` attribute assigns the type of a variable based on the initializer expression.

- The enhanced range-based for-loop allows for easy iteration over elements.

- The `emplace_front` and `emplace_back` methods add new elements to the beginning and end of container. They are more computationally efficient than `push_front` and `push_back`, and are variadic, i.e., takes a variable number arguments. The `emplace` method is similar and applicable to containers where there is only one way to insert(e.g., a stack or a map).

  [emplace_back() 和 push_back 的区别](https://blog.csdn.net/xiaolewennofollow/article/details/52559364)

  [string和vector中的pop_back push_back的练习](https://blog.csdn.net/arctic_fox_cn/article/details/80288095)

- The `array` type is similar to ordinary arrays, but supports `.size()` and boundary checking. (It does not support automatic and dynamic resizing.)

- The `tuple` type implements an ordered set.

- Anonymous functions("lambdas") can be written via the `[]` notation.

  [C++ 中的 Lambda 表达式](https://msdn.microsoft.com/zh-cn/library/dd293608.aspx)

- An initializer list uses the `{}` notation to avoid having to make explicit calls to constructors where building list-like objects.


## 编程技巧

### 排序性能问题

STL提供的`sort`函数使用更方便，不需要做指针类型转换。传入一个`functor`对象的版本比直接使用函数快。

### 整数输入

在将整数输入后直接插入到一个集合或者数组中，一般的做法是建立一个临时变量，使用cin或者scanf输入之后，再将这个临时变量插入到集合中。这样可以封装读取的函数并且这样调用：

```c++
int readint()
{
	int x;scanf("%d", &x);return x;
}
vector<int> vc;
vc.push_back(readint());
```

### for循环宏定义

```c++
#define _for(i,a,b) for(int i=(a); i<(b); ++i)
#define _rep(i,a,b) for(int i=(a); i<=(b); ++i)

vector b;
_for(i,1,a.size()) {...}
```

### STL容器内容调试输出

封装了两个泛型函数使用C++的IO流对集合进行输出:

```c++
template<typename T>
ostream& operator<<(ostream& os, const vector<T>& v)
{
	for(int i=0; i < v.size(); i++) os<<v[i]<<" ";
	return os;
}

template<typename T>
ostream& operator<<(ostream& os, const set<T>& v)
{
	for(typename set<T>::iterator it it = v.begin(); it != v.end(); it++) os<<*it<<" ";
	return os;
}

// 使用方法

vector<int> a; a.push_back(1); a.push_back(2); a.push_back(3);
cout << a;

set<string> b; b.insert("1"); b.insert("2"); b.insert("3");
cout << b;
```

### 二维几何运算类

许多涉及位置计算的题目中，需要模型物体位置并且进行移动和转向，避免每次直接用x和y坐标分别计算，可以构建几何操作类，复用向量的移动、旋转等逻辑

```c++
struct Point
{
	int x,y;
	Point(int x = 0; int y = 0):x(x),y(y) {}
	Point& operator=(Point& p): {x = p.x; y = p.y; return *this;}

};

typedef Point Vector;

Vector operator+(const Vector& A, const Vector& B) {return Vector(A.x+B.x, A.y+B.y); }
Vector operator-(const Point& A, const Point& B) {return Vector(A.x-B.x, A.y-B.y); }
Vector operator*(const Vector& A, int p) {return Vector(A.x*p, A.y*p); }
bool operator==(const Point& A, const Point& B) { return a.x == b.x && a.y == b.y; }
bool operator<(const Point& p1, const Point& p2) { return p1.x < p2.x || (p1.x == p2.x && p1.y < p2.y); }
istream& operator>>(istream& is, Point& p) { return is>>p.x>>p.y; }
```

### 内存池

有时需要动态分配对象。一般的做法是直接用数组开辟空间，但是未必容易事先估计出需要开辟的空间大小，在逻辑控制中还要维护一个变量进行分配和释放，如果是多种对象都要动态分配，则更加繁琐。基于vector容器和C++的内存分配机制，编写了一个内存池：

```c++
template<typename T>
struct Pool
{
	vector<T*> buf;
	T* createNew()
	{
		buf.push_back(new T());
		return buf.back();
	}

	void dispose()
	{
		for(int i = 0; i < buf.size(); i++) delete buf[i];
		buf.clear();
	}
};

//使用方法如下:

struct Node{...};
struct Node2{...};

Pool <Node> n1Pool;
Pool <Node2> n2Pool;

//要分陪内存构造新对象时：
Node *p = n1Pool.createNew();
Node2 *p2 = n2Pool.createNew();
```
然后在每次需要释放时直接用dispose方法即可，不需要再维护各种中间变量。

### 泛型参数的使用

如果同一个题目中需要在两个不同的部分都用到Dijkstra算法，这时候一般的做法就是定义多个MAXSIZE变量，但是会比较繁琐，可以引入C++的泛型参数来解决这个问题:

```c++
template<int MAXSIZE>
struct Dijkstra
{
	int n,m,d[MAXSIZE], p[MAXSIZE];
}
...
```

使用时，就可以通过下面的方式来指定不同的MAXSIZE:

```c++
Dijkstra<MAXK *MAXN> sd;
Dijkstra<MAXN> pd;
```


[newline and endline differences](http://stackoverflow.com/questions/7324843/why-use-endl-when-i-can-use-a-newline-character)

[Enum Naming Convention- Plural](http://stackoverflow.com/questions/1405851/enum-naming-convention-plural)
