# C++特性分级
- T0特性：  收益明显，学习成本/负面效果/设计缺陷不明显
- T1特性：  收益明显，学习成本/负面效果/设计缺陷明显
- T2特性：收益不明显，学习成本/负面效果/设计缺陷不明显
- T3特性：收益不明显，学习成本/负面效果/设计缺陷明显

# T0特性

## 字符串字面量(C++11)
```
    const char* x = R"(lksjdflkj
lskjdflkjsdf
lsjdfjk
)";
```
## namespace
## 函数重载
## 初始化列表
## for新用法
```
for (auto &item : container)
{
	
}

```
## 类
## 匿名函数
```
auto f = [](int i, int j){return i + j;};
```
## if/switch语句初始化(c++17)
```
if (int a = f(); a != 0) {
	cout << a + 1 << endl;
} else {
	cout << a << endl;
}
```

# T1特性

## 虚函数（学习成本明显/设计缺陷明显）
## 类运算符重载
## 模板（学习成本/负面效果/设计缺陷明显）
C++引入了模板，带来很多问题模板与>>符号不兼容。

## 右值引用（学习成本明显）
为了避免对象拷贝，C++设计出右值引用的概念出来。同时很多相关的工具被设计出来，例如：
- std::move
- std::forward

## 智能指针
## auto（学习成本/设计缺陷明显）
C++引入了auto试图减少代码数量。但是设计之初存在好几个问题。后续版本对该关键字做了多次调整。

``` C++11
std::vector<string> v;
for (auto& iter = v.begin(); iter != v.end(); ++it)
{
}
```

``` C++14
auto fun(int i, int j) 
{
    return i + j;
}
```

``` C++20
auto fun(auto& i, auto& j) 
{
    return i + j;
}

auto fun(auto&& i, auto&& j) 
{
    return i + j;
}
```

# T2特性
## using定义类型别名
## nullptr
nullptr跟NULL作用基本相同，引入nullptr增加程序员的学习负担。
C++引入了函数重载。宏NULL在函数重载中容易出问题，于是C++又引入了nullptr。
## 关键字const、constexpr、final、overrid、default、delete、explicit

# T3特性
