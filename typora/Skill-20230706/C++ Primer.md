# C++ Primer

## Chapter 1 初见





## Chapter 2 C++ 基础



### 2.1 内置类型

1. **类型转换**

```C++
w_char    // 宽字符
char16_t  // Unicode 字符
char32_t  // Unicode 字符
```

计算机存储的是二进制补码，正数补码不变，-正数的补码：正数的反码加 1

切勿混用 signed 和 unsigned 类型，signed 和 unsigned 运算时，signed 会转换成 unsigned来算2.

```C++
unsigned int a=10;
unsigned int b=42;
cout<<a-b;   // 出错，结果还是正值
// 所以，for 循环中 int i 是 singned int
```

2. **字面值常量**



```C++
L'a'    // char32_t 'a'
u8"hi"  // UTF-8( 仅用于字符串字面常量 )
10L      // long int 10 
42ULL  // unsigned long long
3.14L  // long double
1E-3F  // float 1*10^-3
int *ptr  // nullptr 是指针的字面值
```

### 2.2 变量

初始化：创建变量时获得了一个特定的值

```C++
std::string book("holly poter");  // 使用构造函数初始化
```

```C++
int i = 0;
int i = {0};
int i{0};
int i(0);  // 全部都是初始化

double pi = 3.14;
int i = {pi};  //  == i{pi}   // 报错
int i = pi;  //  == i(pi)     // OK 强制类型转换
```

标准库中，会出现 __两个下划线开头，一个下划线加大写，来命名

```C++
__Register
_Handler
// 常规使用
index; 
class Sales_item;
studentLoan;
studentLoan;
```



```C++
int i =1;
int main()
{
	int i =0;
	cout<<i;   // 0
	cout<<::i;  // 1
}
```

### 2.3 复合类型

**C++11 新增了右值引用，但是 左值引用是最常用的。**

定义引用时必须要初始化，且引用不能重新绑定变量

```C++
double dval =3.14;
int &refVal = dval;  // Error
int *ptr = &dval;   // Error
double *ptrD = &dval;    //OK
ptr = ptrD;     // Error
```

**引用不是对象，而指针是对象，因此会有指针的指针，指针可以赋值和拷贝，没有指向引用的指针**

空指针，以下三种方式定义

```C++
int *ptr = nullptr;  
int *ptr = 0;
int *ptr = NULL;  // #include <cstdlib>  因为 NULL 是预处理，在C中常用
```

对于空指针 if( ptr ) 为 false

**void * 可以存放任意对象的地址，最常用于函数的返回值（也说明该函数可以操作多种数据类型）**

```
int *&ptr;  // 是一个对指针的引用
```

### 2.4 const 限定符

1. const修饰的变量只在该文件内可见，如果需要在多个文件中可见

```C++
extern const int month =12;  // 1.cpp
extern const int month;      // 2.cpp
```

---

2. 常量的引用： const  + 引用 

```C++
const int ci =1024;
const int &ri = ci;  // OK, ri 是 ci的引用，不允许通过 ri 来修改 ci 的值
ri =42;  // ERROR
int &r2 = ci;   // ERROR
```

常量的引用，之前常规的引用的类型需要和 被引用的对象一致，但常量的引用不一样

常量的引用，初始化时允许用任意表达式作为初始值，只要该表达式的结果能够转发为引用的类型即可。

```c++
int i =42;
int &r0 = i;         // 常规的引用
const int &r1 = i;   // 常规的引用，i的值可以变，但是不能通过 r1来改变
const int &r2 = 42;    // 常量的引用 OK
const int &r3 = r1*2;  // 常量的引用 OK
int &r4 = r1*2;   // ERROR
```

常量的引用

```C++
double dval = 3.14;
const int &ri = &dval;  // OK, ri 是  3 引用

// 编译器内部会生成临时的变量 const int temp = dval; const int &ri = temp;
```

---

**3. 常量指针**：指向常量的指针，不运行通过指针来修改指向对象的值

```C++
int i =10;
const int *pt = *i; // 常量的指针，不允许通过 pt 来改变 i
```

**指针常量**：指针指向的位置不能改变

```C++
int *const pt = *i;　
```

---

4. 顶层 const 与 底层 const

顶层const表示指针本身是个常量（不能改变指向的位置）

```C++
int *cont ptr;
int const ci;
```

底层const表示指针指向的对象是一个常量

```C++
const int *ptr;
cont int &r =ci;
```

顶层const也可以用于所有的类型变量，表示任意类型的对象是常量

而底层const与指针和引用等复合类型有关

---

5. constexpr 和常量表达式

常量表达式：是编译时就具有具体的值

带const不一定是常量表达式

```C++
int t =1;  // 是变量，不是常量表达式
const int i =20;  // 是常量表达式
const int j=i+1;  // 是常量表达式
const int sz=getsize();  //  不是常量表达式，只有在执行时候才有值
```

**第二种方法定义常量表达式**

```C++
constexpr int mf = 20;  // 是常量表达式，但是必须放在函数体外
constexpr int j=i+1;    // 是常量表达式
constexpr int sz =getsize();  // 因为不是常量表达式，会报错
```

**如果需要定义常量表达式，使用  constexpr**

constexpr 与指针

```C++
const int *p = nullptr;  // 指向常量的指针
constexpr int *p2 = nullptr;  // 指针常量，指向的地址不能变

//
int j =0;
constexpr int *p3 = &j;  // OK  但要求很严格，要求 int j =10 定义在所有函数之外
//

//
constexpr const int *p4 = &i;  //  == const int * const p4
```



---

### 2.5 类型别名

两种方式

-  传统的 typedef

  ```C++
  typedef unsigned long pid_t;
  
  //
  typedef doube wages;
  typedef wages base, *p;  // double == wages == base, p == double *
  ```

- 别名声明 using

  ```C++
  using SI = Sales_item;  // SI 是 Sales_item 的同义词
  ```

  指针、常量和类型别名

  **指针这样的复合类型用到类型别名里，就会出现意想不到的结果**

  **不能直接简单的替换**

  ```C++
  char *pstring;
  const pstring cstr = 0;  //  ??  == const char * cstr  ?? 错误
  //  == char * const cstr = 0;
  ```

  

---

**auto 类型说明符**

使用 auto 必须要给初始值

```c++
auto i=0, *ptr = &i;  // OK , auto == int
auto sz = 0, pi = 3.14;   // ERROR
```

---

**decltype 指示符**

decltype 分析表达式并得到他的类型，但是不会计算表达式的值

```C++
decltype( func() ) sum = x; // sum 的类型就是函数 func() 的返回值类型
// 实际上不会调用运行 func()
```

```c++
const int ci =0, &cj=ci;
decltype(ci) x =0;   // const int
decltype(cj) y = x;  //const int &
decltype(cj) z;      // ERROR ,为初始化
```

decltype 和引用

引用是作为其所指对象的同义词，但是在decltype 处是例外

```c++
int *ptr;
decltype (*ptr) x;   / x 是 int & 类型
decltype (ptr) y;    // y是 int *
```

decltype(  (  variable ) ) z;  // z 永远是引用类型

---

自定义数据类型：结构体、类

```C++
struct Sales_item
{
	std::string name[20];
    int sales;
    double price;
};
```



## Chapter 3 字符串、向量和数组

### 3.1 命名空间的 using 声明

.h 头文件中不要使用 using 声明



## Chapter 4 表达式

### 4.1 基础

- 一元运算符：作用于一个运算对象，如 取地址& 和取值 *

- 二元运算符：作用于两个运算对象，如 + - * 、  ==

  只有  &&   ||   ,    ?:     明确是左边先运行

- 三元运算符：如条件表达式 ？，

> 重载运算符：当运算符作用域类类型的运算对象时，用户可以自定义其含义
>

- 左值：
- 右值：

左值可以位于赋值运算符的左侧，右值则不能

当一个对象被用作右值时，使用的是对象的值（内容）；当被用作右值时，用的是对象的身份（在内存中的位置）

```c++
cout<<i<<" "<<++i;  // 结果不可预料，视编译器而异
int i = f()+g();    // f() 与 g（）的调用顺序不确定
&&   ||   ,    ?:   // 明确是左边先运行
```

### 4.2 算术运算符

(-m)/n  =  -(m/n)

m/(-n) =  -(m/n)

(-m)%n  =  -(m%n)  // 如 -21 % -8 = -5

m%(-n) =  m%n     // 如 21% -5 = 1

### 4.3 逻辑和关系运算符

s被声明成了 对常量的引用：`for (const auto &s : text) ` 因为 text 的元素是 string 对象，可能非常大，所以 将s声明成 引用类型 可以避免对元素的拷贝；因为不需要对 string 对象做写操作，所以s被声明成 对常量的引用

```c++
if(a == b<c)   // if a==1 or a==0
```



### 4.4 赋值运算符



```
int k = 3.13;   //OK
int k = {3.14}; // ERROR
```





### 4.5 递增和递减运算符

除非必要，否则不用递增和递减的后置版本。因为后置版本效率低

### 4.6 成员访问运算符

**迭代器** vector<string>::iterator 

```
vector<string>::iterator iter;
*iter++;    // OK
(*iter)++;  //ERROR 没有字符串++
*iter.empty(); // ERROR
iter->empty(); // OK
++*iter;       // ERROR ++没有字符串
iter++->empty();  // OK  == iter->empty(); iter++;
```



### 4.7 条件运算符

: ?   三元运算符

```C++
(grade>90)?"great":(grade>60)?"good":"bad";
== (grade>90)?"great": ((grade>60)?"good":"bad") ;
```

```
#include <vector>
{
	vector <int> ivect ={1,2,3,4,5,6,78,9,10};
	for (auto &val : ivect)
		val = (val%2==0)?val:(2*val);
	for (const auto &val : ivect)
		cout<<val<<'\t';

}
```



### 4.8 位运算符





### 4.9 sizeof 运算符

```c++
sizeof(type)
sizeof expression

Sales_data *p;
sizeof *p;  // OK 因为 *p 并不执行
sizeof data.revenue;  //  == sizeof Sales_data::revenue;
```



```C++
#include <vector>
{
	vector<int> ivect = {1,2,3,4,5};  //sizeof ivect 返回固定值（视编译器），无论元素多少个
}
```



### 4.10 逗号运算符



**从左向右依次求值，逗号表达式的结果是最右边表达式的值**

### 4.11 类型转换

```c++
int ia[10];
int *ptr = ia;  // ia转换成指向数组首元素的指针
```

当数组被用作 decltype 关键字的参数，或者作为取地址符 &，sizeof 以及  typeid 等运算符的运算对象时，上述转换不会发生。

```c++
sizeof(ia);  //  == 数组占用内存大小
decltype(ia) ib;   // ib 也是数组
typeid     //
```

```c++
int *ptr = 0;  // ptr 指针初始化为空指针
if(ptr)    // 判断是否为空指针
    
int i;
const int &j = i;   // OK
const int *p = &i;  // OK
int &r =j; int *q = p;  // ERROR
```

**强制类型转换 static_cast, dynamic_cast,const_cast 和 reinterpret_cast**

```c++
cast-name <type> (expression);
cast-name : static_cast, dynamic_cast,const_cast 和 reinterpret_cast
```

1. static_cast: 任何具有明确定义了的类型转换，只要不包含底层const，都可以使用

   ```c++
   double d;
   int i = static_cast<int>(d);
   void *p =&d;
   double *dp = static_cast<double *>(p);  // 将 p 强制转换回去 double *，前提是必须保证正确
   ```

   

2. dynamic_cast: 危险不使用

3. **const_cast: 只能改变运算对象的底层 const，即把常量转换为非常量，只有 指针和引用 可用**

   ```c++
   const char *pc;
   const char a;
   char *q = pc;  // ERROR
   char *p = const_cast<char *>(pc);  // OK,可以通过 p来修改pc的值，不推荐使用
   char &m = const_cast<char &>(a);  
   char c; 
   char&m = const<const char &>(c);  // const_cast 也可以添加 const属性
   ```

   **const_cast 常常用于函数重载的上下文中**

4. reinterpret_cast: 危险不使用







## Chapter 5 语句

### 5.1 简单语句

switch 内部的变量定义

```C++
switch(flag)
{
	case 1:
	 	int i;    // OK
	 	int j =1; // ERROR
	 	break;
	case 0:
		cout<<&i;
}
switch(flag)
{
	case 1:
	{
	 	int i;    // OK
	 	int j =1; // OK
	 	break;
	 }
	case 0:
		cout<<&i;  // ERROR undefined 
}
```



```
#include <vector>
int main(void)
{
	vector <int >v1 ={1,2,3,4};
	vector <int >v2 ={1,2,3,4,5,6};
	auto it1 = v1.cbegin();
	auto it2 = v2.cbegin();
	while(it1!=vi.cend() && it2!= v2.cend() )
	{
		if(*it1 != *it2)
			{
				cout <<"no";
				break;
			}
			++it1;
			++it2;
	}
	if(it1 == v1.cend() )
		cout<<"v1 is pre of v2";
	if(it2 == v2.cend() )
		cout<<"v2 is pre of v1";

return 0;
}


```

范围 for 语句

```c++
for (declaration:expression)  // expression 必须为一个序列，如数组，vector，string等类型的类，他们的特                               // 定是拥有能返回迭代器的 begin 和 end 成员
	statement;
// declaration 定义一个变量，序列中的每个元素都得能转换成该变量的类型，使用 auto 确保类型相容
```

```c++
vector <int> v ={0,1,2,3,4,5,6,7};
for (auto &r : v)  // 对于 v 中每个元素
	r *= 2;
// 每次迭代，都会重新定义 r，定义 r 为当前元素的引用

for(auto beg = v.begin,end = v.end; beg!=end; ++beg)
{
    auto &r = beg;
    beg *= 2;
}
```

**在范围 for 中，不能增加 vector对象的元素，因为预存了 end() 的值。**

```c++
#include <string>
using namespace std;
int main(void)
{
	string str1,str2;
	do{
	cout<<"enter two string:"<<end;
	cin>>str1>>str2;
	if(str1.size()>str2.size)
		cout<<str1;
	else if (str1.size()<str2.size)
		cout<<str2;
	else cout<<"equal";
	}
	while(cin);
}
```

5.5 跳转

goto，return，break，continue

### 5.6 try 语句块和异常处理



异常是指纯在于运行时的反常行为，这些行为超出了函数正常功能的范围。典型的异常包括失去数据库连接以及遇到意外输入等。

异常处理机制为程序中异常检测和异常处理这两部分的协作提供支持，异常处理包括

- **throw 表达式**：异常检测部分使用throw表达式来表示它遇到了无法处理的问题，即throw引发raise了异常
- **try 语句块**：异常处理部分使用 try，以一个或多个catch子句结束，catch子句处理异常，也成为异常处理代码
- 一套异常类，用于在throw表达式和相关的catch子句之间传递异常信息

```c++
int main(void)
{
	cout<<"enter two integer";
	cin>>n1>>n2;
	if(n2!=0)
		cout<<n1/n2;
    else return -1;
	return 0;
}
```

**throw 抛出异常**

标准库异常类型：runtime_error 运行时检测出的问题

​										exception 最常见的问题

​										overflow_error 运行时错误：计算上溢   

​										underflow_error 运行时错误：计算下溢

```c++
int main(void)
{
	cout<<"enter two integer";
	cin>>n1>>n2;
	if(n2!=0)
		cout<<n1/n2;
    else throw runtime_error("cannot be zero");  // 创建一个匿名的 runtime_error 对象
	return 0;
}
```

**try 处理异常**

```c++
try {
	program-statement
} catch (exception-declaration){
	handler-statement
} catch (exceptiion-declaration){
	hadler-statement
}  // ...
```

```c++
int main(void)
{
    int n2,n1;
	cout<<"enter two integer";
	while(cin>>n1>>n2)
    {
        try{ 	if(n2!=0)
                cout<<n1<<"/"<<n2<<"="<<n1/n2<<endl;
                else throw runtime_error("cannot be zero");  // 创建一个匿名的 runtime_error 对象
           } catch (runtime_error err)
        {
            cout<<"请确认错误信息："<<err.what()<<endl;  //  .what() 返回异常的文本信息（字符串）
            cout<<"是否重新输入"<<endl;
            char ch;
            cin>>ch;
            if( ch!='y' && ch != 'Y')
                break;
        }
        cout<<"enter two integer";
    }
	return 0;
}
```



## Chapter 6 函数

函数的返回值不能是数组和函数，但是可以返回指向数组和函数的指针

自动对象：只存在于块执行期间的对象

局部静态对象：在程序的执行路径第一次经过对象定义语句时初始化，生命周期贯穿函数调用及之后的时间如 static

```c++
siez_t count_calls()  // 记录函数被调用次数
{
	static size_t ctr = 0;  // 调用结束后，变量仍有效
	return ++ctr;
}
```

**一个函数不会被用到，那么它可以只有声明没有定义。**

**必须为每一个虚函数都提供定义，不管它是否被用到了。**

```c++
void print(vector<int>::const_iterator beg,
		   vector<int>::const_iterator end);
```

分离式编译

### 6.2 参数传递



```c++
void reset(int *ip)
{
	*ip = 0;  // 实参指向对象的值改变了
	ip = 0;   // 只改变了ip的局部拷贝，实参未被改变
}
int i =42;
reset(&i);  // 只改变了i的值，i的地址没变
```

**拷贝大的类类型对象或者容器对象时，一般通过引用形参访问该类型的对象。**



```c++
void func(const int i);  
void func(int i);  // ERROR ,重复定义，const int i的const会被忽略
```

**形参中使用引用时，尽量使用 const 类型的引用。**

```c++
void find(const char ch); // 传递参数时，可以传 char j; 也可以 const char j;
```

vecter类型的迭代器

```c++
vector <int>::iterator 是一种类型

vector <int>::iterator a;
vector<int> vec(10); 
a = vec.begin();
a = vec.end();
```

---

数组形参

```c++
void print(const int *);   // 三种方法一样的
void print(const int[]);
void print(const int[10]);  // 10没有作用，不推荐
```

```c++
void print(const char *cp)
{
	if(cp)  // 判断是否为空指针
		while(*cp)  // 判断是否为空字符
			cout<<*(cp++)
}
```

---

数组的引用作为形参（很少用）

```c++
void print(itn (&arr)[10])  // arr 是具有10个整数数组的引用
{
	for(auto elem:arr)
		cout<<elem;
}
// void print(const int &arr[10]);  // false，arr是数组名，里面有10个元素，每个元素都是引用
```

---

多维数组作为形参

```c++
void print(int (*matrix)[10], int rowSize);   //  == void print(int matrix[][10], int rowSize)
// matrix 是指向含有10个整数的数组的指针，指向数组的首元素，该数组的元素是由10个整数构成的数组

int *matrix[10]   // matrix 是一个数组，数组中含有10个指针作为元素
```

---

如何交换指针的地址？？

```c++
void swapAddress(int *pt1,int *pt2)  // false,针对形参做的操作，并没有改变实参
{
	int *temp = pt1;
	pt1 = pt2; pt2 = temp;
}

void swapAddress2(int *(&pt1), int*(&pt2))  // pt1，pt2是指针的引用
{
	int *temp = pt1;
	pt1 = pt2; pt2 = temp;
}

```





### 6.3 main 处理命令行选项

main 函数的参数

```c++
int main(int argc,char *argv[]){}  // argv 是一个数组，元素是指向字符的指针，即字符串数组

./a.out hi world
// 此时，argc =3，argv[0] = "./a.out"
    			 argv[1] = "hi'"
    			 argv[2] = "world"
atoi() 函数可以把字符串转换成 int
```

### 6.4 含有可变形参的函数

函数处理不同数量参数的函数，C++ 11 有两种方法

1. **intializer_list**：**全部实参类型相同，个数未知**

2. **编写一种特殊函数，即可变参数模板**

   **intializer_list的用法，参数都是常量，不可在调用函数中修改**

```C++
#include <initializer_list>
void error_msg(initializer_list <string> i1)
{
	for(auto beg = i1.begin(); beg!=i1.end(); ++beg)
		cout<<*beg<<' ';
}
error_msg( {"one", "two", "three"} );
```

```C++
void error_msg(initializer_list <string> i1)  // 等效于上面
{
	for(const auto &elem:i1)
		cout<<elem<<' ';
}
```

计算参数列表中整数的总和

```c++
int sum(initialzer_list<int> ilist)
{
	int sum = 0;
	for(const auto &elem:ilist)  // 使用 const &的目的是，避免赋值拷贝占用时间和内存，同时不修改实参
		sum += elem;
    return sum;
}
```

---

省略符作为形参，只用于与C函数交互的接口程序。很少用

```c++
void foo(...);
void foo(parm,...);
```

### 6.3 返回类型和 return 语句

无返回值的函数可以加 return; 程序运行到此直接结束。

**不要返回局部对象的指针和引用，因为函数终止局部变量的引用将指向不再可用的空间**

```c++
const string &func()
{
	string temp;
	return temp;  // ERROR
	return "hi";  // ERROR  "hi"是局部临时量
}
```

左值：可以被赋值，可以取地址操作

调用一个返回引用的函数得到左值，其他返回类型是右值

列表初始化返回值

```C++
vector<string> process()
{
	return {};  // 返回空 vector 对象
	return {"1"};  // OK
	return {"3"," ","2"};  // OK,因为函数返回和初始化赋值是一样的操作
}
int integer = {1};  // OK  return {1};  OK
```

main 函数不能调用自己

```c++
vector<int> v1 = {1,2,3,4,5};
print(v1,0);
int print(vector<int> vint,int index)
{
	if(index < vint.size())
	{
		cout<<vint[index];
		print(vector<int> vint,index+1);
	}
}
```

---

**返回数组的指针**

typedef int arrT[10];  // arrT 是一个类型别名，表示的类型是含有10个整数的数组

using arrT = int [10];   // 与上面等效

arrT *func();   // 函数返回一个指向含有10个整形数组的指针

```c++
int arr[10];
int *ptr1[10];  // ptr1 是一个数组
int (*ptr2)[10] = &arr;  // ptr2 是数组的指针
int *ptr3 = arr;
```

方法0：使用类型别名

```
typedef int arr[10];
arr *func(int i);
```

方法一：定义返回数组的指针的函数

```c++
int (*func (int i) ) [10];
```

方法二：尾置返回类型

```c++
auto func(int i) -> int(*)[10];
```

方法三：使用 decltype

```c++
int odd[] = {1,2,3,4,5};
decltype(odd) * func(int i)
{}
```

返回数组的引用，即将 * 改为 &

---

### 6.4 函数重载

**函数的形参个数不同或者形参类型不同的同名函数，称为函数重载。与函数的返回值无关**

main 函数不能重载

```c++
Record lookup(const Account&); // 函数重载  == Record lookup(const Account &acct); // 函数重载
Record lookup(const Phone&);   // 函数重载   因为函数声明时候可以不用写形参名称
Record lookup(const Name&);    // 函数重载
```

```c++
Record lookup(const Account&);
bool lookup(const Account&);  // 错误，函数重复定义

typedef  Account T;
Record lookup(const Account&);
Record lookup(const T&);   // 错误，函数重复定义
```

```c++
Record lookup(Phone);
Record lookup(const Phone);   // 错误，函数重复定义

Record lookup(Phone *);
Record lookup(Phone * const);  // 错误，函数重复定义
```

**如果形参是某种类型的指针或引用，则可以通过 const 来实现函数重载**

```c++
Record lookup(Phone &);
Record lookup(const Phone &8);   // 新函数，作用于常量引用

Record lookup(Phone *);
Record lookup(const Phone *);   // 新函数，作用于指向常量的引用
```

---

const_cast 和 重载

const_cast 可以去 const 化

```c++
const string &shortStr(const string &str1,const string &str2)
{
	return (str1.size()<=str2.size())?str1:str2;
}
// 函数可以接收 非常量的string，但是返回的结果仍然是 const string 类型
// 解决方法
string &shortStr( string &str1, string &str2)
{
    // 首先，添加 const,使得可以调用上面函数
    auto &r=shortStr( const_cast<const string &>(str1), const_cast<const string &>(str2));
	return const_cast<string &>(r);  // 去 const 化
}

```

---

重载与作用域

```c++
void print(const string&);
void print(double);
void print(int);
void func()
{
	print("hi");  // 调用 void print(const string&)
	print(3);     // 调用 void print(double)
	print(3.14);  // 调用 void print(int)
}
```

```c++
void print(const string&);
void print(double);

void func()
{
	void print(int);  // 因此，不要在函数内放函数声明
	print("hi");  // 错误 void print(int) 把前面函数声明覆盖掉
	print(3);     // 调用 void print(int)
	print(3.14);  // 调用 void print(int)
}
```

### 6.5 特殊用途

---

**默认参数：函数声明中提供默认值（函数定义中不用加），从右往左提供，因此，默认参数一般放在形参右侧**

---

**内联函数和 constexpr 函数**

- 内联函数可避免函数调用的开销，一般用于规模较小，流程直接，频繁调用的函数

- constexpr 函数：**能用于常量表达式的函数，函数形参和返回值都是字面值类型，且函数体中有且只有一个return**

  ```c++
  oncstexpr int new_sz(){return 42;}  // OK
  constexpr int foo = new_sz();  // OK,foo是一个常量表达式  ==constexpr int foo = 42;
  ```

  constexpr函数是隐式的内联函数

内联函数 和 constexpr 函数通常定义在头文件 .h 中

**assert 预处理宏**断言

预处理宏其实是一个预处理变量，行为类似于内联函数

```c++
#include <cassert>
assert (expr);  // 如果 expr 为假，则终止程序；否则什么都不做
```



---

**NDEBUG 预处理变量**

如果定义了 NDEBUG，则 assert 不起作用

```C++
g++ -D NDEBUG assert.cpp  // 与在程序中 #define NDEBUG 一样效果
./a.out
```

使用 NDEBUG 在程序中添加调试代码

```c++
void func()
{
	...
	#ifndef NDEBUG
		cout<<"输出调试信息";
		cerr<<__func__;  // 输出存放函数的名字
	#endif
}
```

### 6.6 函数匹配

```c++
void func(int);
void func(int,int);
void func(double,double i =2.2);
func(2.2);    // 调用第三个
func(3,2.2);  // 第二个月与第三个一样匹配，二异性-，故报错
```

### 6.7 函数指针

**函数指针：指向函数的指针**

```c++
bool lengthComp (const string &,const string &);  // 函数声明
bool *pf (const string &,const string &);        // 函数声明，pf 函数返回 bool 类型的指针
bool (*pf) (const string &,const string &);      // 函数指针，定义函数指针
// 函数指针赋值
pf = 0;  // OK,不指向任何函数
pf = lengthComp;
pf = &lengthComp;   // 与上面等效
// 使用函数指针调用函数
bool b1 = pf("hi","world");  // == lengthComp ("hi","world");
bool b1 = (*pf)("hi","world");  // 与上面等效
```

---

重载函数的指针

```c++
void ff(int *);
void ff(unsigned int);

void (*pf)(unsigned int) = ff;  // 指向的函数是 void ff(unsigned int)
```

---

函数指针作为形参

形参看起来是函数类型，实际上是函数的指针

```c++
void useBigger( bool (*pf)(const string &,const string &));
void useBigger( bool pf(const string &,const string &));   // 与上面等效
// 使用
useBigger( lengthComp);
```

---

**使用 typedef 为函数指针起别名**

```c++
// 函数类型,Func,Func1
typedef bool Func(const string &, const string &);
typedef decltype(lengthComp) Func1;  // 与上面等效

// 函数的指针,Func2,Func3
typedef bool (*Func2)(const string &, const string &);
typedef decltype(lengthComp) *Func3;  // 与上面等效


// 使用类型别名
void useBigger( Func );
void useBigger( Func2 );  // 与上面等效，因为形参看起来是函数类型，实际上是函数的指针
```

---

**返回指向函数的指针**

最简单的方法是使用类型别名

```c++
using F = int(int*,int);  // 函数类型
using PF = int(*)(int*,int); // 函数指针类型
// 使用
PF func1(int);  // func 返回指向函数的指针
F *func2(int);  // 与上面等效
F func2(int);   // 错误，不能返回函数
```

更复杂的方法

```c++
int (*fun3(int))(int *,int);
int func4(int) -> int (*)(int *,int); // 与上面等效
// 返回函数的指针，函数类似于 int (*)（int *,int）
```

---

将 auto 和 decltype 用于函数指针类型

decltype 作用于一个函数时，返回的是函数的类型

```c++
int func5(int);
decltype (func5) *func6(const string &); // func6 函数返回一个函数指针，指向类似于int func5(int)的函数 
```

---

练习：编写函数声明，接受int参数，返回类型是int；然后声明一个 vector 对象，元素是指向该函数的指针

```c++
int func(int, int);  int func2(int, int);
using pf = int (*)(int, int);
vector<pf> a;
// 其他方法：
vector<int (*)(int, int)> a = {func,func2};
vector<decltype(func) *> a;
// 通过 vector 使用里面的对象
for (auto funcPtr:a)
    funcPtr(22,11);
```



## Chapter 7 类

**类的基本思想是：数据抽象(data abstraction)和封装(encapsulation)，数据抽象是一种依赖于接口(interface) 和实现 (implementation)分离的编程**

**class 与 struct 几乎一样，唯一区别：struct 的访问权限默认的 public，而 class 则相反。**

---

**友元函数**：其他类或者其他函数访问它的private成员

```c++
class Sales_date
{
	public:
	friend void func();  // 定义时写 void func()
};



```

---

**构造函数**：类通过一个或几个特殊的成员函数来控制器对象的初始化过程

构造函数名字和类名相同，没有返回类型。构造函数不能声明为const

当创建一个const对象时，直到构造函数完成初始化过程，对象才取得const属性。因此，构造函数在const对象的构造过程可以向其写值。

**默认构造函数：不带任何参数或者所有参数都具有默认值**

```c++
class Sales_date
{
	string bookNo;
	unsigned int units_sold =0;
	double price;
	double revenue = 0;
	public:
    Sales_data(){}  // == Sales_data()=defaule;
	Sales_data(const string &s):bookNo(s){}
	Sales_data(const string &s,unsigned int n,double p):bookNo(s),units_sold(n),price(p){}
};
```

编译器会默认生成以下函数：构造，拷贝，赋值和析构

**内置基本类型可以使用默认的拷贝赋值，但是当成员数据里面有指针时（动态内存分配），默认的就不行**

---

class 内定义类型别名，类型别名出现在类开始的地方，因为要先定义后使用。

```c++
class　Screen
{
	public:
		typedef std::string::size_type pos;
	private:
		pos cursor = 0;
};
```

内联函数的定义一般写在 .h 文件中，内联函数的定义可以直接跟在声明后面，也可以在 .c 和 .h 中定义

---

可变数据成员 mutable

```c++
private:
	mutable size_t access_ctr;  // 即使在一个 const 对象内也能被修改
	std::vector<Screen> screens{Screen(24,80,' ')};
public:
	void some_member() const;
	
void Screen::some_member() const  // const 成员函数不能修改成员变量
{
	++access_ctr;
}
```

---

返回对象的引用

```c++
public:
	void some_member() const;
	Screen &display() const;  //ERROR
	const Screen &display() const;  // OK, const成员函数要是返回引用，必须返回const引用
```

---

**一个类的成员变量不能是类自己的类型，然而类出现即认为是声明过了，因此类允许包含定义指向 该类的指针或引用作为成员变量**

---

**友元类**：一个类中想要操作另一个类中的数据

```c++
class Screen
{
	friend class Window_mgr;  //Window_mgr 的成员函数可以访问 Screen 类的私有部分
};
```

**友元成员函数**：

```c++
void Window_mgr::clear();
class Screen
{
	friend void Window_mgr::clear();  // Window_mgr::clear() 函数可以访问 Screen 类的私有部分
};             // // Window_mgr::clear() 函数需要在前面先有声明
```

---

类的作用域：使用作用域访问运算符 ：：

```c++
class　Screen
{
	public:
		typedef std::string::size_type pos;
		pos func();
	private:
		pos cursor = 0;
};

// .c 中
Screen::pos Screen::func()
{}
```



**编译器编译完全部声明后才会处理成员函数的定义**

```C++
class　Screen
{	public:
		void func1(){ func2() };  // OK
		void func2(){ height = 0; };  // OK
 	private:
 		int height;
};
```

### 7.5 构造函数

**列表初始化：效率高，给const和&引用的数据变量赋值（必须使用列表初始化），或者成员是某种未提供默认构造函数的类的类型**

委托构造函数

没有创建任何构造函数时 ，编译器才会合成默认构造函数

默认构造函数：不带参数或所有参数都带有默认值的构造函数

隐式的类类型转换

```c++
class Sales_data
{
private:
	string bookNo;
public:
	Sales_date(const string &s):bookNo(s){}
	combine(const Sales_data);
};
int main()
{
	string null_book = ;
	Sales_date item;
	item.combine(null_book);  // OK,会先创建一个无名类的对象Sales_date(const string &s):bookNo(s){}
							  // string 隐式转换为 Sales_date 类型
    item = null_book;  // string 隐式转换为 Sales_date 类型
    item = string("9-99");  // OK, string 隐式转换为 Sales_date 类型
    item = "9-99";  // FALSE, "9-99" ->string->Sales_data  转换了两次
}
```

**只有一个形参的构造函数才会发生隐式转换**

**因为有了构造函数 Sales_date(const string &s):bookNo(s){}，所以才允许隐式转换**

抑制构造函数定义的隐式转换 explicit

```c++
explicit Sales_date(const string &s):bookNo(s){}  // 类外定义时不用加 explicit
// 但是依然可以显式的转换
item.combine(null_book);  // FALSE 隐式
item.combine(Sales_data(null_book));  // OK 显式
item.combine(static_cast<Sales_data>(null_book)); // OK 显式
```

---

**聚合类使得用户可以直接访问其成员**，并且具有特殊的初始化语法形式。

集合类满足以下条件：

- 所有成员都是public
- 没有定义任何构造函数
- 没有类内初始值（类内初始值，如 class a{ int num = 0}; ）
- 没有基类，也没有virtual 函数

---

**字面值常量类**

1100， true，2.3， 类（100，200） 都是字面值

字面值常量类：（1）数据成员都是字面值的聚合类，

​									（2）或者非聚合类，但满足以下条件

- 数据成员必须是字面值类型
- 类至少有一个constexpr 构造函数
- 如果数据成员中含有类内初始值，则内置构造函数的初始值必须是一条常量表达式；或者如果成员属于某种类类型，则初始值必须使用成员自己的 constexpr 构造函数
- 类必须使用析构函数的默认定义，该成员负责销毁类的对象。

第一种字面值常量类

```c++
class Point
{
public:
	int x;
	int y;
};

// 使用
const Point start(100,200);
```

第二种：

```c++
class Point
{
private :
int x; int y;
public:
constexpr Point(int a,int b):x(a),y(b){}
};

// 使用
constexpr Point start(100,200);

```

### 7.6 类的静态变量

**静态数据成员不属于任何一个类的对象，只和类关联，他们不是由构造函数初始化，静态数据成员只能初始化一次，一般在类的外部初始化**

静态成员函数不能声明为 const，不包含 this 指针

```c++
class Account
{
    static double interestRate; // 要在类的外部初始化, const类型是例外
    
public:
	static void rate(double);  //外部定义函数时不用加 static
}

double Account::interestRate =9.9;
void Account::rate(double n)  //外部定义函数时不用加 static
{
    interestRate=n;
}
// 使用静态成员函数
Account::rate(3.14);
Account ac1;
ac2.rate(2.2);
Account *ac2;
ac2->rate(2.2);  // 通过指针或引用调用静态成员函数
```

在类内定义常量

1. 类内使用枚举
2. 使用 static const

```c++
class Test
{
enum{Months = 12};
static const int MONTH = 12;
}
```

---

静态数据成员的类型可以是该类的类型，而普通成员变量（除指针外）不行

```c++
class Bar
{
	private:
		static Bar mem1;  // OK，静态成员可以是不完全类型
		Bar *mem2;  // OK，指针成员可以是不完全类型
		Bar mem3;   // FALSE,数据成员必须是完全类型
}
```



## Chapter 8 I/O库

I/O库包括 iostream，fstream，sstream

```c++
istream  // 输入流类型，提供输入操作
ostream  // 输出流类型
cin      // 一个 istream 对象，从标准输入读取数据
cout     // 一个ostream 对象
cerr     // 一个ostream对象，通常用于输出程序错误信息，写入到标准错误
```

| 头文件   | 类型                                           |
| -------- | ---------------------------------------------- |
| iostream | istream从流读取数据,ostream，iostream读写流    |
| fstream  | ifstream从文件读取数据,ofstream，fstream文件流 |
| sstream  | istringstream从string读取数据，ostringstream   |

通常可以将一个派生类对象当作其基类对象来使用。

IO 对象没有拷贝或赋值操作，但是可以以引用方式传递和返回流

```
ofstream out1,out2;
out1 = out2;  // error
out2 = print(out2); // error
```

IO库中有一些条件状态

```c++
int ival;
cin>>ival;  // 当输入的不是int类型时，cin会进入错误的状态，一旦发生错误，后续的IO操作都失败

// 通常使用 while一直判断cin 的状态
while(cin>>word)
```

查询流的状态

```c++
cin.clear();  // 使 cin 有效
cin.clear(cin,rdstate() & ~cin.failbit & ~cin.badbit);  // 清除多个错误位
```



### 8.2 文件输入输出



### 8.3 string 流











## Chapter 9 顺序容器







## Chapter 10 泛型算法



## Chapter 11 关联容器







## Chapter 12 动态内存







## Chapter 13 拷贝控制





## Chapter 14









## Chapter 15







## Chapter 16









## Chapter 17

































