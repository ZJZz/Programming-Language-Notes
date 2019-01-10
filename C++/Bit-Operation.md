# 位运算专题

[C++中位运算的使用方法](https://blog.csdn.net/a1351937368/article/details/77746574/)

## 位运算操作封装

在使用位向量表示集合或进行状态压缩时，有个常用操作就是取得一个整数中某一位或者连续几位对应的int值。如果一个题目中多处调用，会增加出错的可能，针对这种情况封装了一个位运算的操作类:

```c++
template<typename TI> //TI可以是支持位操作的任何类型，一般是int/long long
struct BitOP
{
	//反转pos开始，长度为len的区域
	inline TI flip(TI op, size_t pos, size_t len = 1)
	{
		return op^(((1<<len)-1)<<pos);
	}

	//设置从pos开始，长度为len的区域对应的整数值
	inline TI& set(TI& op, size_t pos,int v, size_t len =1)
	{
		int o = ((1<<len)-1);
		return op = (op&(~(o << pos))) | ((v&o)<<pos);
	}

	//取得从pos开始，长度为len的区域对应的整数值
	inline int get(TI op, size_t pos, size_t len = 1)
	{
		return (op >> pos) & ((1<<len)-1);
	}

	//输出整数的二进制表示
	ostream& outBits(ostream& os, TI i)
	{
		if (i) outBits(os, (i >>1)) << (i & 1));
		return os;
	}
}
```

如果是32位整数位运算可以使用`BitOp<long>`来调用，64位可以使用`BitOp<long long>`来调用
