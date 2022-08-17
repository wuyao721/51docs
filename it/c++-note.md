# <center> C++学习笔记

# C++11/14/17 核心技术

# 编程思想
## 类
## 继承
## 多态
### 模版
### 虚函数
### 函数/运算符重载

# 关键字
## namespace
## using
``` C++17
using InflightExtHttpRequestPtr = std::shared_ptr<InflightExtHttpRequest>;
```

## auto & decltype
```
  for (auto& matchNode : target_rematch_vec_) {
    iRet = xxx(hit, matchNode, httpReq, hscratch);
  }
```

## 匿名函数
```
  auto handler = [](uint32_t id, uint64_t from, uint64_t to, uint32_t flags,
                    void* context) -> int32_t {
  };
```

## cast

# stl相关

## 零拷贝
### 右值引用
```
std::move
```
### emplace
### string_view

## 智能指针
### make_shared
```
hsdb = std::make_shared<HsDatabase>(db);
std::make_unique<HsScratch>(p);
```

## algorithm
## 并发

## 结构化绑定
```
std::tuple<int, double> func() {
    return std::tuple(1, 2.2);
	}
	
int main() {
    auto[i, d] = func(); //是C++11的tie吗？更高级
    cout << i << endl;
    cout << d << endl;
}

void f() {
    map<int, string> m = {
        {0, "a"},
        {1, "b"},  
    };
    for (const auto &[i, s] : m) {
        cout << i << " " << s << endl;
    }
}

int main() {
    std::pair a(1, 2.3f);
    auto[i, f] = a;
    cout << i << endl; // 1
    cout << f << endl; // 2.3f
    return 0;
}
																		
int main() {
    std::pair a(1, 2.3f);
    auto& [i, f] = a;
    i = 2;
    cout << a.first << endl; // 2 
}
```

## if-switch语句初始化
## 折叠表达式
## std::any
## file_system



# c语言和c++的区别

结构里面定义结构，在两种语言里是不一样的。

``` C++
struct a{
    struct b{
        int m;
    }*j;
}i;

int main()
{   
    struct b c;
    i.j = &c;
    return 0;
}
```


# 问题
## 问题1
```
gcc -O3

union {
    uint64_t v;
    struct {
        uint32_t r2;
        uint8_t  r1;
        uint8_t  v2;
        uint16_t v1;
    };
} u;


void iii()
{
    u.v1 += 1;
    u.v2 += 1;
    u.v += 1;
}

00000000004005f0 <iii>:
  4005f0:	66 83 05 56 0a 20 00 	addw   $0x1,0x200a56(%rip)        # 60104e <u+0x6>
  4005f7:	01 
  4005f8:	80 05 4e 0a 20 00 01 	addb   $0x1,0x200a4e(%rip)        # 60104d <u+0x5>
  4005ff:	48 83 05 41 0a 20 00 	addq   $0x1,0x200a41(%rip)        # 601048 <u>
  400606:	01 
  400607:	c3                   	retq   
  400608:	0f 1f 84 00 00 00 00 	nopl   0x0(%rax,%rax,1)
  40060f:	00 


void iii()
{
    u.v += 1;
    u.v1 += 1;
    u.v2 += 1;
}

00000000004005f0 <iii>:
  4005f0:	48 8b 05 51 0a 20 00 	mov    0x200a51(%rip),%rax        # 601048 <u>
  4005f7:	48 83 c0 01          	add    $0x1,%rax
  4005fb:	48 89 c2             	mov    %rax,%rdx
  4005fe:	48 89 05 43 0a 20 00 	mov    %rax,0x200a43(%rip)        # 601048 <u>
  400605:	48 c1 e8 28          	shr    $0x28,%rax
  400609:	48 c1 ea 30          	shr    $0x30,%rdx
  40060d:	83 c0 01             	add    $0x1,%eax
  400610:	83 c2 01             	add    $0x1,%edx
  400613:	88 05 34 0a 20 00    	mov    %al,0x200a34(%rip)        # 60104d <u+0x5>
  400619:	66 89 15 2e 0a 20 00 	mov    %dx,0x200a2e(%rip)        # 60104e <u+0x6>
  400620:	c3                   	retq   
  400621:	0f 1f 44 00 00       	nopl   0x0(%rax,%rax,1)
  400626:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
  40062d:	00 00 00 
```
