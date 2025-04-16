

VIM 快捷键

> i 从当前光标进入插入模式      			I 进入插入模式，置光标于句首
>
> a 从当前光标之后进入插入模式		  A 进入插入模式，置光标于句尾





# C++ Primer Plus

C 提供低级硬件访问，OOP 提供了高级的抽象



OOP 面向对象的编程

泛型编程强调独立于特定数据类型：如，要对不同类型的数据进行排序，必须为每种类型都创建一个排序函数。

​														    泛型编程需要对语言进行扩展，以便可以只编写一个泛型函数（即不是特定类型的函数），并将其用于各种实际类型

源代码 ->(通过编译器) 目标代码 -> (加入启动代码，库代码) 链接程序 -> 可执行代码

sudo apt-get install g++

<< 插入运算符，将右侧信息插入到流中，cout 是对象

```c++
cout<<"hello."
```

将字符串插入到输出流中

cout 是一个 ostream 类对象；cin 一个是 istream 类对象

using namespace std；   // 之后直接可以使用 cout

using std::cout   // 之后直接可以使用 cout

std::cout

### 数据处理

OOP 的本质是设计并扩展自己的数据类型。

short 至少16位

int 至少与short一样长

long 至少32位，且至少与int一样长

long long 至少64位，且至少与long一样长



float 至少32位

double 至少48位，通常64位，且至少与 float 一样长

long double 为80，96，128位，至少和 double 一样长

```C++
int a = 7;
// ==
int a(7);
int a{7};
```

数值表示

- 0x 十六进制 hex

- 0 + 1~8的数字开头 八进制 oct

- 1~9开头 十进制 dec

数值进制打印

- cout<<hex;  // 以下以 hex 显示数值
- cout<<oct;
- cout<<dec;  // 默认以 dec 显示

使用类的对象访问函数

```C++
cout.put('a');  
```



float 32位

1位符号，8位指数位，23位小数点后的位（二进制科学计数法，第一位一定为1）

故在十进制中，有效位为 （23+1）/4 =6位

```C++
float 26.0
11010.00000000(24个0)
1.1010(+24个0)*2^4
符号(一位)     指数(八位)          小数(23位)
0           127+4的二进制        1010(+19个0)
 
float 0.75
0.1100000(22个0)
1.1（+23个0） * 2^-1
符号： 0
指数： 127+（-1）
小数： 1（+22个0）
    
float -2.5
10.1
-1.01 * 2^1
符号： 1
指数： 127+1
小数： 01（+21个0）
    
// 浮点数 +-*/ 都是不精确的，因为浮点数本身就是不精确的（内存中二进制存储导致）  
// 一定要使用小数，使用 double 精度更高
// 不进行四舍五入，固定打印到小数点后六位
cout.setf(ios_base::fixed,ios_base::floatfield);
```

强制类型转换 static_cast

static_cast<typename>(variable)

```C++
auto n =100;  // int
auto n =1.0;  // double
auto n =1.3e12L;  // long double
```

## Chapter 4 复合类型



```
int arrayInt[5] = {};  // all elements set to 0
// ==
int arrayInt[5] {};
```



每次读取一行字符串输入

```C++
cin.getline(str,size);  //读取'\n', 但是不会保存‘\n’,而是用‘\0’替代，最多读取 size-1 个字符
cin.get(str,size);    // 不读取 '\n'

// 使用
cin.get(str,size); 
cin.get();   // 读取掉 '\n'
cin.get(str2,size2); 
// 方式二
cin.get(name,Size).get();  // cin 返回值也是 cin对象
```

==函数重载，函数名相同，但是带的参数列表不同，如 cin.get()==



字符串赋值，拼接和附加

```C++
#include <string>
string str01 = "hello";
string str02 = "orange";
string str03;
str03 = str01;
str03 = str01 + str02;  // 拼接
str02 += str01;  // 末尾追加
str01 += "orange";
cout<<"strlen of str01 = "<<str01.size()<<endl;  // 未初始化时为 0
```

要使用 C 中 string.h 的函数，需要包含 #include <cstring>

```C++
#include <cstring>
strcpy(str01,str02);  // copy str02 to str01
strcat(str01,str02);  // append contents of str02 to str01
```

输入字符串

```C++
char ch[20];
cin.getline(ch,20);
string str01;
getline(cin,str01);
```

### 4.4 结构体



```C++
struct inflatable
{
	char name[20];
	float volume;
	double price;
};
inflatable hat =  // struct not required
{
    "Jack",
    3.1,
    1.89
};  
inflatable hat02 = hat;  // 结构体之间直接赋值
inflatable structArray[10] =  // 结构体数组
{
    {"name1",1,1},  
    {"name2",2,2}
};  
```

结构体中的位字段，常用于底层寄存器操作

```C++
struct torgle_register
{
	unsigned int SN:4;  // 4 bits
	unsigned int :4;  // 4 bits unused
	bool goodIn:1;
	bool goodTorgle:1;
};
torgle_register tr = {14,false,true};
```

### 4.5 共同体

union是一种数据格式，能够存储不同的数据类型，但同时只能存储其中的一种类型

```C++
union one4all
{
	int int_val;
	long long_val;
	double double_val;  // 占用空间为最大成员所占空间
};
one4all pail;
pail.int_val = 6;
pail.double_val = 3.14;  // 存储 一个double 类型数据，覆盖掉原来数据
```

### 4.6 枚举

枚举只有赋值运算，且值只能取规定值。作用是：方便定义多个字符常量

```C++
enum specturm
{
	red,orange,yellow,green,blue,violet,indigo,ultraviolet  // start with 0 in default
};
specturm mycolor = red;  
mycolor++;  // invalid, 只有赋值运算，无算术运算
int color = mycolor; // OK
mycolor = 3; // invalid 即 int 类型比枚举类型的 等级高，int不能转换为枚举类型值
mycolor = specturm(3);  // OK
```

枚举类型范围：

最大值：如 enum 中最大为8，则最大值为 2^4 - 1 = 15  （2^4 ）

​			 如 enum 中最小为-6，则最大值为 -2^3 + 1 = -7

### 4.7 指针和自由存储空间



```C++
int *ptr1, *ptr2;
ptr1 = (int *)0x08000000;  //  error ptr1 = 0x08000000;
```

new 分配内存 (类似于C的malloc() 函数)，为一个数据对象（可以是结构体，基本类型）指定分配内存

```C++
int *ptr = new int;  // new 返回开辟内存的首地址
```

new 分配的内存块通常与常规变量声明分配的内存块不同，常规变量存储在栈stack中，而new 开辟的内存在堆heap或自由存储区free store中

new 如果分配失败，会返回空指针 null pointer

使用 delete 释放内存(与new 成对存在)

```C++
int *ptr = new int;
delete(ptr);
```

不要使用delete释放同一个内存块两次

不要创建两个指向同一个内存块的指针

```C++
int *ptr01 = new int;
int *ptr02 = ptr01;  // 不推荐
delete ptr01 ;
```

使用 new 创建动态数组

```C++
unsigned char *ptrString = new unsigned char [15000];
ptrString[1] = 1;  // == *(ptrString+1) = 1;
delete [] ptrString ;  // 释放整个数组
```

### 4.8 指针，数组和指针算术



```C++
int arr1[3];  // arr1 == &arr1 == &arr1[0]
int *arr2 = new int [3];
cout<<sizeof(arr);   //  12
cout<<sizeof(arr2);  // 8
// arr1+1 为基地址+2
// &arr1+1 为基地址+12
```



指针和字符串

==打印地址（地址不是指向char类型），打印内容（该地址指向char类型）==

```C++
char flower[10] = "rose";
cout<<flower;  // 打印地址（地址不是指向char类型），打印内容（指向char类型）
// 如“rose”字符串代表字符串的首地址
cout<<"rose";
// 打印字符串的地址：
cout<<(int *)(&flower)<<(int *)(&flowe[1]);
```





strcpy (destion,argument);

strncpy (destion,argument,size);

new创建动态结构体

在运行时创建数组优于在编译时创建数组，对结构体亦是如此。

```C++
// 省略创建结构体 inflatable
inflatable *ps = new inflatable;
// 使用 ps-> 来访问成员
```

- 自动存储

  只是在特定函数被执行时存在，保存在栈中，后入先出LIFO，如局部变量，{内部有效变量}

- 静态存储

  在整个程序执行期间都存在的变量，如，函数外部定义的，或加 static

- 动态存储

  内存池，自由存储空间free store 或堆 heap，与上述两种存储变量的内存是分开的。如new 和delete 可以在不同函数中使用。数据的生命周期不完全受程序或者函数的生存时间控制。



### 4.9 类型组合



```C++
struct antarctica_years_end
{
	int year;
};

// 结构体数组
antarctica_years_end trio[3];
trio[0].year = 2023;    // == (trio)->year = 2023;
(trio+1)->year = 2022;  // == trio[1].year = 2022;

// 指针数组
const antarctica_years_end *arp[3] = {&trio[0],&trio[1],&trio[2]};
cout<<arp[1]->year;

// 指针的指针
const antarctica_years_end ** ppa = arp;
cout<<(*ppa)->year;  // == (&trio[0])->year;

// auto
auto arp2 = arp;  // arp2 也是指针数组，每个元素指向一个结构体, 这里 arp2 是指针的指针
(*(arp2+1))->year;  // == (&trio[1])->year;
```

### 4.10 数组的替代品

**模板类 vector  (存储在堆 heap 中)**

**模板类 array  （与数组一样，存储在栈中）**



```C++
#include <vector>
vector<int> vi;    // vector<int> 对象
vector<double> vb(n); // vector<double> 对象 的数组
vb[0] = 1.0/3;

// 一般形式
vectro<typename> vt(n_elem);
```

array 对象的长度是固定的，也使用栈（静态内存分配），而不是自由存储区，因此效率与数组相同。

```C++
#include <array>
array<int, 5> arrInt;  // create array object of 5 ints
array<double, 3> arrDouble = {1,2,3.1};
array<double, 3> arrDouble02 = arrDouble;  // valid for array objects of same size
// 一般形式
array<typename, n_elem> arr;
// 其中 n_elem 不能是变量（与vector 不同）
```



## chapter 5 循环和关系表达式



```C++
cout.setf(ios_base::boolalpha);
cout<<0;  // false
cout<<1;  // true
```



c 中，字符串比较 strcmp(str1,str2); 若相同则返回0

c++ 中，string 库可以实现用 == 



```C++
typedef typename aliasName
//
typedef char byte;
```



#### 5.4 基于范围的 for 循环



```C++
double prices[5] = {4,10,6,7,8};
for(int x:{1,2,4})
    ;
for(double x:prices)  // 不能修改数组元素，只能读
	cout<<x;
for(double &x:prices)  // &表明 x 是一个引用变量，这样声明可以修改数组内容
    x *= 0.8;
```



哨兵字符 sentinel character 判断输入结尾

```C++
char ch;
cin.get(ch);  // ch 是引用类型，所以可以修改其内容
cout<<ch;
while(ch!='#')
{
    
}
//cin.get 可以不带参数，带一个，带两个，因为使用了函数重载，函数名相同，参数不同
```

文件尾条件 EOF（ctrl+d 在 ubuntu 系统中）

检测到 EOF cin.eof() 将返回 true，否则返回 false

```C++
char ch;
cin.get(ch);
while(cin.eof() == true)  // == while(cin.get(ch))
{
	cout<<ch;
    cin.get(ch);
}
// char *city[5]; // == char city[5][name]; == string city[5 ]
```







字符读取

```C++
char ch;
ch = cin.get();  // == cin.get(ch);

```

字符串读取

```C++
// char 数组的输入
char chArr[20];
cin<<chArr;  // 读取遇到的第一个字符串，遇到 空字符结束，不读取空字符

cin.get(chArr, 20);  // 读取字符串，遇到 '\n' 停止，不读取 '\n'（'\n'会留在缓冲区中，为了方便下次正常读取，需要加 cin.get()）
cin.get();  // 读取出缓冲区中的 '\n'，为了下次正常读取

cin.getline(chArr, 20);  // 读取字符串，遇到 '\n' 停止，会读取 '\n'


// string 的输入
#include <string>
string str;
getline(cin,str);  // 读取字符串
```

注意：

输入数值之后，要输入字符串

```C++
cin<<intVal;
cin.get();  // 读取出缓冲区中的 '\n'，为了下次正常读取
char chArr[20];
cin.get(chArr,20);
```

常用

```C++
// 重置错误标记，并清空输入缓冲区
cin.clear();
while(cin.get()!='\n');  // 清除缓冲区内容

cout<<fixed;  // 不要以科学计数法显示
cout.precision(2);  // 保留小数点后两位
cout.setf(ios_base::showpoint);  // 显示小数点后面无效的0


```



### chapter 6 分支语句和逻辑运算符

#### 6.7 读取数字的循环

==cin 要求输入数值，但是输入的是字符，那么cin返回false，且错误输入会被标记（cin完全不能用了），要用 cin.clear() 重置错误输入标记，才能继续读取输入。==

####  6.8 简单文件输入/输出

写入与读取文件 ( 与 cout 的操作一样)

```C++
#include <iostream>  // ostream -> cout   istream -> cin
#include <fstream>   // ofstream   ifstream
// 写入文件
ofstream outFile;  // 创建输出流对象
outFile.open("info.tx");	   // 将对象和文件关联  文件不存在则会创建，若存在则会清空内容
outFile<<"The first line of the .txt file.\n"
outFile.close();
    
```

读取( 与 cin 的操作一样)

```C++
// 读取文件内容
#include <fstream>
#include <cstdlib>
ifstream inFile;
inFile.open("info.txt");
if(inFile.is_open)
    cout<<"open info.txt file successfully.\n";
else 
    exit(EXIT_FAILURE);

char file_name[50];
inFile>>file_name;
if(inFile.good())
    cout<<"read data from file successfully.\n";
if(inFile.eof())
    cout<<"reach the end of file.\n";
inFile.close();
```



### chapter 7 C++ 的编程模块



#### 7.4 函数和二维数组

```C++
int sum(int (*arr)[4], int size);  // 声明  == sum(int arr[][4], int size);
int data[3][4];
sum(data,3);  // 调用
```



 array

```C++
void fill(array<double,5> *pa)
{
	for(int i =0 ; i<5; i++)
		cin>>(*pa)[i];  //*(pa+i);
}
```

#### 7.9 递归

包含多个递归调用的递归

将一项工作不断分为两项较小的，类似的工作时

```C++
int main()
{
	const int len = 66;
	char ruler[len]={0};  ruler[len-1]='\0';
	int min = 0;
	int max = len-2;
	ruler[min] = ruler[max] = '|';
	subdivide(ruler,min,max,6);
	cout<<ruler<<endl;
}
void subdivide(char ar[],int low,int high,int levels)
{
	if(levels == 0) return;

	int mid = (low+high)/2;
	ar[mid] = '\0';
	
	subdivide(ar,low,mid,--levels);
	subdivide(ar,mid,high,--levels);
}
```



#### 7.10 函数指针



```C++
double pam(int);  // 函数原型
double (*ptrFunc)(int);  // 函数指针，ptrFunc 此时为函数
ptrFunc = pam;  // == autp ptr = pam;
ptrFunc(5);  // == (*ptrFunc)(5) 使用函数指针调用被指向函数
// 注意区分
double * func(int)  // 是函数，返回值是指针
    
// 
double (*ptrFuncs[3])(int);  // ptrFuncs 为数组，每个元素都是函数指针
double *(*(*ptrFunc)[3])(int);  // 指向 类似 ptrFuncs 的指针

```

typedef 进行简化 

```C++
typedef double real;
typedef const double *(*ptrFunc)(const double *,int);  // ptrFunc 为type（函数指针） 名字
ptrFunc pa[3] = {func1,func2,func3};  // pa 为数组，每个元素是指向函数的指针
ptrFunc (*pd)[3] = &pa;

```

































### chapter 8 函数

#### 8.1 内联函数

为了提高运行速度，与常规函数的区别不在于编写方式，而在于C++ 编译器如何将它们组合到程序中。

内联函数的编译代码与其他程序代码“内联”起来了。即编译器将使用相应的函数代码代替函数调用。对于内联代码，程序无需跳到另一个位置处执行代码，在跳转回来。因此，内联函数的运行速度比常规函数块，但代码需要占用更多内存。如：程序在10个不同地方调用同一个内联函数，则该程序将包含该内联函数代码的10个副本。

```C++
// 声明
inline double square(double);
// 定义
inline double square(double x)
{	return x*x;	}

// 内联函数与 函数宏的区别
#define square(x) (x)*(x)  // 并不是通过参数传递来实现，而是文本替换，需要谨慎使用，
```



#### 8.2 引用变量

==复合类型，已定义的变量的别名，将引用变量用作参数，函数将使用原始数据，而不是其副本。==

这样，除了指针外，引用也为函数处理大型结构提供了一种方便的途径。

```C++
// 创建引用变量，必须在声明时将其初始化，且初始化后不能改变引用关系
int rats;
int & rodents = rats;  // int & 指的是指向int 的引用，rats 和 rodents可互换，指向相同的值和内存单元
// 本质上是指针的伪装形式  == int * const ptr = &rats;  

// 引用变量的赋值
int a = 90;
rodents = a;  //  == rodents = 90  // 只是赋值，没有改变引用关系
// 也可以使用指针
int *ptr = &rats;  // *ptr 与 rats，rodents 都可以互换
```



```c++
int var;
int *ptrInt = &var;
int & rodents = *ptrInt;  // rodents 与 var 关联起来，且不能改变引用关系，即使 ptrInt 指向了其他变量
```

==将引用  用作函数参数（一般用于参数是结构体）==

```C++
void swap(int &a, int &b)  // 传递给引用参数的实参必须是变量
{
    int temp = a;
    a = b;
    b = temp;
}
int main(void)
{
    int val1=1, val2 =2;
    swap(val1,val2);  // 实现实参的交换
    swap(1,2);  // ERROR
    swap(val1,2+val2);  // ERROR
    return 0;
}
```

==当引用实参与形参不匹配时，c++ 将生成临时变量，仅当参数为 const 引用时，才有效==

```C++
void swap(const int &a, const int &b)  // 传递给引用参数的实参必须是变量
{
    int temp = a;
    a = b;
    b = temp;
}
// 调用
swap(val1,val2);   // ERROR 不允许修改 实参的值
swap(1,2);  // OK
swap(1,2+val1); // OK
```

==尽可能使用 const 引用==

- 避免无意修改数据
- 函数可处理 const 和非 const 实参，否则只能接收非 const 实参
- 使得函数能够正确生成并使用临时变量

==上述引用都是左值引用，第十八章，右值引用==

***

2. 将引用用于结构体，引用非常适合用于结构体和类

```C++
struct free_throws
{float rate;};
// 使用
void set_percent(free_throws & stru);
void display(const free_throws & stru);
free_throws & Func(free_throws & stru)  // 返回值为 结构体引用
{}
```

为什么返回值要用 结构体的引用： 如果返回结构体，那么返回值会被复制到一个临时位置，然后在传递给调用函数；返回结构体的引用，可以省去开辟临时空间的操作，效率高。

```C++
// 注意, 以下做法错误
const free_throws & clone(free_throws & stru)
{
	free_throw newone;
	return newone;  // 函数运行完，newone 就不存在了
}
// 注意, 以下做法可
const free_throws & clone(free_throws & stru)
{
	free_throw *newone;
    *newonw = stru;
	return *newone;  // == return stru
}
```

3. 引用 用于类对象

```C++
string str;
string result;
getline(cin, str);
result = version(str, "***");
const string & version(string & str1, const string & str2)  // 最前面加 const 是既可以返回const 又可以返回 非const
{
    str1 = str2+str1+str2;
    return str1;
}

```



4. 对象，继承和引用

- 继承

ofstream 对象可以使用 ostream 类的方法cin， cout等，能够将特性从一个类传递给另一个类的语言特性被称为继承。

ostream 是基类，ofstream是派生类（从ostream派生而来的），==派生类继承了基类的方法和特性==，这意味着ofstream 对象可以使用基类的特性，如格式化方法precision() 和 setf()

继承的另一个特征是：==基类引用可以指向派生类对象，而无需进行强制类型转换==。

结果：可以定义一个接收基类引用作为参数的函数，调用该函数时，可以将基类对象作为参数，也可将派生类对象作为参数。

```C++
int file_it(ostream &os, int data)
{
    os<<"the data is "<<data<<endl;
}
// 调用
file_it(cout,1);  // 基类对象作为实参
fstream outFile;
file_it(outFile,1);  // 派生类对象作为实参
```

***



引用的特点：

- 能够修改调用函数中的数据
- 通过传递引用而不是整个数据对象，提高运行速度

什么时候使用指针或引用

​	1. 对于不修改传递参数的值的函数

- 如果数据对象很小，如内置数据类型或小型结构体，则按值传递
- ==如果数据对象是数组，则只能使用（const）指针==
- 如果数据对象是较大的结构体，则使用（const）指针或（const）引用，提高程序效率。
- 如果数据对象是类对象，使用（const）引用。传递类对象参数的标准方式是按引用传递
  2. 对于修改调用函数中数据的函数

- 如果数据对象是内置数据类型，则使用指针。如果看到如 fixit(&x) 其中x 是int 类似代码，则很明显，改函数将修改x
- 如果数据对象是数组，则只能使用指针
- 如果数据对象是结构体，则使用指针或引用
- 如果数据对象是类对象，则使用引用

***

#### 8.3 默认参数



```C++
// 函数声明
char *left(const char *str, int n=1);  // 默认参数写在右边
int harpo(int n, int m=4; int j=5);
// 调用
beeps=harpo(2);     //  == harpo(2,4,5)
beeps=harpo(2,7);   //  == harpo(2,7,5)
beeps=harpo(2,7,9); //  == harpo(2,7,9)
beeps=harpo(2,,9);  //  invalid
// 函数定义
int harpo(int n, int m; int j)
{}
```

#### 8.4 函数重载

左值引用和右值引用  &&

***

左右相对于  = 和 +=

```C++
int a = 10;   // a 为左值，10为右值
int c = a + b;  // a+b 为右值

```

记法：能够对其取地址的，就是左值；不能对其取地址的，就是右值

***

之前所有的都是左值引用，如

```C++
int a = 10;
int a2 = 20;
int &b = a;    // = 右侧是一个左值
int &c = 10;   // invalid, 因为 10 是右值
int &d = a+a2; // invalid, 因为 a+b 是右值

const int &c = 10;   // OK 和临时变量产生了关联，常引用（只能通过该引用来读取数据，而不能修改）
const int &d = a+a2; // OK
```

右值引用

```C++
int &&x = 10;   // OK, 右值引用
int &&y = a+a2;  // OK, 右值引用
```

***

函数多态即函数重载，指可以有多个同名的函数，因此对名称进行了重载。

函数重载的关键是函数的参数列表，也称为函数特征标 function signature。

如果两个函数的参数数目和类型相同，同时参数的排列顺序也相同，那么它们的特征标相同，而变量名是无关紧要的。

==如果参数数目和/或参数类型不同，则特征标也不同。==

==C++ 运行定义名称相同的函数，条件是它们特征标不同。==

```C++
void print(int i,int width);  // #1
void print(const char *str,int width);  // #2
void print(double i,int width);  // #3
// 调用
print(1,2);  // #1
print("hello",5);  // #2
float i =1.0; print(i,2);  // ERROR,因为没有匹配类型的函数，而强制类型转换的函数有多个
```

特征标不同的情况

```C++
double cube(double x);
double cube(double &x); //ERROR 不能共存
```

***

```C++
void dribble(char *bits);
void dribble(const char *bits); // OK
```

***

```C++
void dabble(char *bits);
// 调用
const char p1[] = "dedicate";
dabble(p1);  // ERROR

void dabble2(const char *bits);
// 调用
const char p1[] = "dedicate";
char p2[] = "dedicate";
dabble2(p1);  // OK
dabble2(p2);  // OK
```

***

```C++
long gronk(int n ,float m);
double gronk(int n ,float m);  //ERROR
// 特征标相同，不允许重载

long gronk(int n ,float m);
long gronk(float n ,float m);  //OK
// 特征标不同
```

***

```C++
void stove(double &r2);  // #0
void stove(const double &r2);  // #1
void stove(double &&r2);  // #2
// 调用
double x = 1.1, y = 2.2;
stove(x);  // #0
stove(x+y);  // #2 若没有 #2，则会调用 #1
```

***

何时使用函数重载和默认参数

- 当函数基本执行相同的任务，但使用不同类型的参数时，才使用。
- 默认参数：使用不同值的参数，但是参数类型是相同的，使用默认参数。

#### 8.5 函数模板

1. 模板基本创建方法（模板实例化）

```C++
// 函数重载，有时候很麻烦
int Swap(int &a,int &b);
double Swap(double &a,double &b);
char Swap(char &a,char &b);

// 重复定义三个函数，故引入函数模板
template <typename AnyType>  // == template <class AnyType>
// 如 template <typename t>
// void Swap(t &a,t &b)
void Swap(AnyType &a,AnyType &b)
{
    AnyType temp = a;
    a = b;
    b = temp;
}

// 调用
int a,b;
float c,d;
Swap(a,b);
Swap(c,d);
// 编写了一个函数，但是编译器编译之后还是会生成两个 Swap() 函数
```

2. 重载的模板

上述模板不能满足所有需求，如交换两个数组

于是，可以像普通重载函数那样（函数特征标不同），对模板函数进行重载（函数特征标不同）。

```C++
// 函数定义
template <typename T>
void Swap(T a[], T b[],int n)
{
    T temp;
    for (int i=0;i<n;i++)
    {
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}
// 函数声明
template <typename T>
void Swap(T a[], T b[],int n);
// 使用
int arrInt[3] = {1,4,6};
int arrInt2[3] = {0,2,9};
Swap(arrInt,arrInt2);
```

3. 模板的局限性

```C++
template <typename T>
void Swap(T a[], T b[],int n)
{}
// 上述模板对数组（函数内包含a=b或*），结构体（函数内包含a>b或*），指针不成立（函数内包含*）。模板并不能处理所有的数据类型

struct job{char *name; int number;};
job x,y;
Func(x,y);
```

若给上述函数传入两个结构体，那么结构体中所有内容发生互换。

而想要实现结构体中部分内容互换，使用模板的重载不行（因为定义的重载模板的特征标一样），需要用到显式具体化

4. 显式具体化模板（对某一种类型数据做单独处理）：

```C++
template <typename T>
void Swap(T a[], T b[],int n)
{}

// 但 Swap 遇到传入参数为 job 类型时
template <> void Swap<job>(job &j1, job &j2)
{
    int temp = j1.number;
    j1.number = j2.number;
    j2.number = temp;
}
```

==显式具体化模板优先于常规模板，而非模板函数优先于具体化模板和常规模板==

==相同名称函数优先级（前提是满足参数类型）：非模板函数 -> 显式具体化模板 -> 模板函数==

```C++
// 常规模板
template <typename T>
void Swap(T a[], T b[],int n)
// 显式具体化模板，声明和定义一样，都需要
template <> void Swap<job>(job &j1, job &j2)
// 非模板函数
void Swap(int a, int b)

// 调用
int i =1,j=2;
Swap(i,j);  // 调用的是非模板函数
```

5. 实例化和具体化

在代码中包含函数模板本身不会生成函数定义，它只是一个用于生成函数的方案。

程序调用模板函数，编译器要使用模板为特定类型生成函数定义时，得到的是函数实例 instantiation

上述是编译器通过隐式实例化，来使用模板生成函数定义。现在，C++ 允许显式实例化explicit instantiation，这意味着可以直接命令编译器创建特定的实例，如

Swap<int>()

```C++
template void Swap<int>(int , int);  // 显式模板实例化

// 注意 与 显式具体化 specification 的区别
template <> void Swap<int>(int a, int b);
```

具体化相当于模板的一个特例，即使用模板定义了一个特定类型的函数

==实例化：无论模板是否被调用，都定义一个函数（使用模板为特定数据类型）。即预先就定义好函数，主要为编写库文件提供函数（因为没有实例化的模板不能放在目标文件中）==

==在同一个文件（或转换单元）中使用同一中类型的显式实例和显式具体化将出错。==

> 编译之后生成目标文件，目标文件经过链接之后生成可执行文件。函数要实现调用，那么函数必须要在目标文件中（即生成了函数定义）

若没有实例化，只有在调用函数时候，才会使用模板定义一个函数。

***

```C++
// 常规模板
template <typename T>
void Swap(T a[], T b[],int n);
// 使用
int x =1; double y =2;
cout<<Swap<double>(x,y)<<endl;
// 显式实例化，为double类型
```

==隐式实例化，显式实例化，显式具体化统称为具体化 specification。相同之处：表示的都是使用具体类型的函数定义==

==区别显式实例化和显式具体化，使用前缀 template (实例化) 还是 template <>==

![20230901](D:\Administrator\Pictures\forTypora\20230901.jpg)

***

6. 编译器选择使用哪个函数版本

对于函数重载，函数模板，函数模板重载，C++ 需要一个策略：重载解析，来决定为函数调用使用哪一个函数定义。

- Step 1 ->  创建候选函数列表。其中包含与被调用函数的名称相同的函数和函数模板；
- Step 2 ->  使用候选函数列表创建可行函数列表。这些都是函数参数数目正确的函数，为此有一个隐式转换序列，其中包含实参类型与相应的形参类型完全匹配的情况。如使用float参数的函数调用可以将该参数转换为double，从而与double形参匹配，而模板可以为float生成一个实例
- Step 3 -> 确定是否有最佳的可行函数，有则使用。

e.g. 01 编译器自己选择

```C++
template <typename T>
void ShowArray(T arr[],int n)
{
    for(int i=0; i<n; i++)
        cout<<arr[i]<<' ';
}
struct debts{char name[20]; double amount};
int main(void)
{
    int test[] = {1,2,3,4,5};  
    ShowArray(test,sizeof(test)/sizeof(test[0]));  // 可正常显示
    struct debts testdebts[]={
        {"a",22},{"b",44},{"c",33}
    };
    double *ptrDouble[3];
    ptrDouble[0] = &testdebts[0].amount;
    ptrDouble[1] = &testdebts[1].amount;
    ptrDouble[2] = &testdebts[2].amount;
    ShowArray(ptrDouble);  // 显示的是地址（虽然是完全匹配，但是不是最佳匹配）
    return 0;
}

/*-------------修改,增加以下的重载函数，该函数变成最佳匹配，则可正常显示 ptrDouble 的内容---------------------------*/
template <typename T>
void ShowArray(T *arr[], int n)
{
    cout<<"using ShowArray function 02.\n";
    for(int i=0; i<n; i++)
        cout<<*(*arr[i])<<' ';
}
```



e.g. 02 自己选择

```C++
template <typename T>
T findLesser(T a, T b)  // #1  模板函数
{
    cout<<"using findLesser function #01.\n";
    return (a>b)?b:a;
}
int findLesser(int a, int b)  // #2  常规函数
{
    cout<<"using findLesser function #02.\n";
    return  (a>b)?b:a;
}
int main(void)
{
    int m =2,n=3;
    double k=5.2,j=9.1;
    // 编译器选择
    findLesser(m,n);  // 调用 #2
    findLesser(k,l);  // 调用 #1
    // 自己选择
    findLesser<>(m,n);  // 调用 #1
    findLesser<int>(k,l);  // 将k和l强制转换成 int 类型，并调用 #1
}
```

***



```C++
template <typename T1, typename T2>
void fit(T1 a, T2 b)
{
	?type? xpy = x+y;  // C++ 98 中无法确定 xpy 的类型   
}
// 在 C++ 11 中使用新增的关键字 decltype 解决
void fit(T1 a, T2 b)
{
    decltype(x+y) xpy;
	xpy = x + y;  // (x+y) 计算之后才确定 xpy 的类型
}
```

使用 decltype 确定类型，编译器必须遍历一个核对表，分为以下四种情况

```C++
decltype(expression) varname;
```

***

- case 1: expression 为（变量），则var 类型与 expression 的类型一致

```C++
double x;
double y;
double &rx = x;
const double *pd;
decltype(x) w;  // double w
decltype(rx) u = y;  // double & u
decltype(pd) v;  // const double * v
    
```

***

- case 2: expression 为函数，则 var 与返回值一致

```C++
long indeed(int);
decltype (indeed(3)) m;  // int m 
```

***

- case 3: expression  为 （（变量）），则 var 类型是 变量的类型的引用

```C++
double xx =3.3;
decltype((xx)) r2 = xx;  // double &r2;
decltype(xx) w = xx;  // double w;
```

***

- case 4：

```C++
int j;
int &k = j;
int &n = j;
decltype(j+6) i1;  // int
decltype (100L) i2;  // long
decltype (k+n) i3;  // int  因为 k+n 的结果是 int
```

***

还有一个问题 decltype 无法解决，如

```C++
template <typename T2,typename T2>
?type? gt(T1 x, T2 y)
{
	return x+y;
}
// 如果 ?type? 写成 decltype(x+y), ERROR，因为此时还未声明 x 和 y
```

使用新增的语法解决该问题( 后置返回类型 )

```C++
double func(int x, float y);
// ==
auto func(int x, float y) -> double;  // 后置返回类型
auto gt(T1 x, T2 y) -> decltype(x+y);
```

#### 8.6 总结与习题

***

何为内联函数：特殊的函数，相当于把一段代码直接替换到该处，编译器在编译的时候不用像常规函数那样进行函数调用。提高了函数的速度。

何时使用内联函数：代码量小，代码中不出现递归的调用。

参数默认值：只有函数原型声明需要修改（有默认值的参数放在右边），函数定义不用。

```C++
float m;
float &rm = m;
decltype(rm) x1 = m;  // float &
decltype((m)) x2 = m; // float &
decltype(2.0*m) x3;   // double
```

















### chapter 9 内存模型和名称空间



头文件中通常包括：

- 函数原型声明
- 使用 #define 或 const 定义的符号常量
- 结构体声明，类声明
- 模板声明
- 内联函数（只有内联函数的定义放在头文件，其他函数定义不行）

#### 9.2 存储持续性，作用域和链接性

C++ 11中使用四种方案存储数据，区别在于数据保留在内存中的时间

![20230902](D:\Administrator\Pictures\forTypora\20230902.jpg)

作用域：在类中声明的成员的作用域为整个类，在名称空间中声明的变量的作用域为整个名称空间。

作用域解析运算符 ：：

```C++
cout<<::globaVal;  // :: 变量 表示使用变量的全局版本
```

静态持续性，内部链接性，即 static 全局变量

静态存储持续性，无链接性，即 static 局部变量，只会被初始化一次

***

说明符与 限定符

存储说明符：

- auto (C++ 11 不再是)
- register 
- static
- extern
- thread_local (C++ 11 新增)
- mutable

mutable 用来指出，即使结构体或类变量为 const，其某个成员也可以被修改

```C++
struct data
{
	char name[20];
	mutable int accesses;
}
```

cv-限定符

- const
- volatile

const 与 C 语言不同，在 C++ 中 const 全局变量的链接性是内部的，就像使用了 static 一样

```C++
const int fingers = 10;  //  == static const int fingers = 10;
int main(void)
```

在某个 .h 文件中定义一个 const 变量，所有 #include 该头文件的文件中都会复制一份 const 变量

***

函数和链接性

默认情况下，函数的链接性为外部的，即可以在文件间共享。

使用另一个文件中的函数，可以extern +函数声明，extern 可省略。

static 将函数的链接性设置为内部的，使之只能在一个文件中使用。

***

语言链接性 language linkage

C 中，编译器可能将函数 spiff 翻译为 _spiff 

C++中，编译器可能将函数 spiff(int) 翻译为 _spiff_i, 而将 spiff(double) 翻译为 _spiff_d, 将 spiff(double, double) 翻译为 _spiff_d_d

```C++
extern "C" void spiff(int);  // 使用 C 语言链接性
void spiff(int);  // 默认使用 C++ 语言链接性
extern "C++" void spiff(int);  // 使用 C++ 语言链接性
```

***

存储方案和动态分配

1. 使用 new 运算符初始化

```C++
int *ptrInt = new int (4);  // *ptrInt set to 4  == int *ptrInt = new int {4};
double *ptrDouble = new double[3] {1.1,2.2,3.3};
// int *a = new int;  等价于 int *a = new(sizeof(int))
```

new 分配内存失败时，会返回空指针，同时引发异常 std::bad_alloc

***

定位 new 运算符

通常，new 负责在堆 heap 中找到一个足以满足要求的内存块（使用完需要delete 释放内存）；new还有一种变体，即定位运算符placement，使得能够指定要使用的位置。

定位运算符 new 开辟的内存空间不用 delete 释放

```C++
#include <new>
struct struct01{int data;};
char buffer[100];
int main(void)
{
	int *ptr1,*ptr2;
    struct01 *ptr03,*ptr04;
    ptr1 = new int[20];  // place an array in heap
	ptr2 = new (buffer) int[20];  // place an array in buffer
    ptr03 = new struct01;  // 开辟y一个 sizeof(struct01) 大小的内存空间
    ptr04 = new (buffer) struct01;  // 在buffer中 开辟y一个 sizeof(struct01) 大小的内存空间
	delete(ptr1);
    delete(ptr03);
    // ptr2 ptr04 不能释放内存
    return 0;
}
```

#### 9.3 名称空间

C++ 中，名称可以是变量，函数，结构体，枚举，类以及类和结构体的成员。

两个库中可能都定义了名为 Tree，Node的类，会有冲突。

C++ 通过定义一种新的声明区域来创建命名的名称空间，目的之一是提供一个声明名称的区域。

一个名称空间中的名称不会与另一个名称空间的相同名称发生冲突，同时允许程序的其他部分使用该名称空间中声明的东西。

```C++
// 定义名称空间，不同名称空间中声明的名称相互独立。
namespace ONE
{
	double data;
	void getMax();
}
namespace TWO
{
	double data;
	void getMax();
}
// 添加名称到已有的名称空间
namespace TWO
{
	char *goose(const char *);
}
// 再次使用已有名称空间来提供函数的定义
namespace TWO
{
	void getMax()
    {return 0;}
}
```

名称空间可以是全局的，也可以位于另一个名称空间中，但是不能位于代码块中

访问名称空间中的名称，使用作用域解析运算符 ::

```C++
TWO::data = 100;
ONE::data = 90;
```

若不希望每次使用名称都对他进行限定，C++ 提供了两种机制

- using 声明

```C++
using TWO::data;  // 将 data 放置于全局名称空间，相当于全局变量
using TWO::getMax();  // 将 getMax() 放置于全局名称空间
// 使用
getMax();  // == TWO::getMax();

// 需要注意
int data;   
int main(void)
{
    using TWO::data;  // 将 data 放置于main局部名称空间
    using ONE::data;  // ERROR 因为 TWO::data 已经存在了
    int data;  // ERROR 因为已经有了 TWO::data
    cout<<data;  // 输出 TWO::data
    cout<<::data;  // 输出 全局的data 变量
}
```

- using 编译指令，使得名称空间中所有的名称都可用，如 using namespace std; 

```C++
using namespace TWO;
// 使用
getMax();  // == TWO::getMax();
```

==两者的比较，using 声明比using编译指令更安全，但是 using 编译指令更加方便==

using 声明，相当于声明了相应的名称一样。如果某个名称已经在函数中声明了，则不能使用 using 声明导入相同的名称。

然而，using 编译指令，将对名称解析名称空间是全局的。

```C++
int data;
int main(void)
{
    using namespace TWO;
    int data;  // OK 将隐藏 TWO::data; 且不报警告
    cout>>data;  // 
    cout>>TWO::data;
    cout>>::data;
}
// 而 using 则不行
int main(void)
{
    using TWO::data;
    int data;  // ERROR，报错误
}
```

***

名称空间的其他特性：

可以将名称空间进行嵌套：

```C++
namespace elements
{
	namespace flame
	{int a;}
	float water;
}
// 使用 以下三种方法
using elements::flame::a;
using namespace elements::flame;
cout<<elements::flame::a;
```

情况二：

```C++
namespace myth
{
	using elements::water;
	using std::cin;
}
// 使用
std::cout<<myth::water;
std::cout<<elements::water;
```

***

未命名的名称空间

其作用域为从声明点到该声明区域末尾，类似于 static 全局变量

```C++
namespace // unnamed namespace
{
	int ice;
	double cream;
}
```

***



#### 9.4 总结与复习题

***

只在一个文件使用的静态变量，两种定义方法：

```C++
// 方法一：
static int var;
// 方法二：
nemaspace   // 只在当前文件类可用
{
	int var;
}
```



***

using 编译指令与 using 声明

```c++
namespace One
{
	int fetch;
};
int main(void)
{
	using namespace One;
	int fetch;  // OK  该变量会起作用， One 里面的fetch 暂时隐藏
}

// 而 using 声明则不同
int main(void)
{
	using One::fetch;
	int fetch;  // ERROR
}
```

***

编程题部分

















## chapter 10 对象和类

面向对象编程 OOP 特性：

- 抽象
- 封装和数据隐藏
- 多态
- 继承
- 代码的可重用性

### 10.2 抽象和类

==类是一种将抽象转换为用户定义类型的 C++ 工具，它将数据表示和操纵数据的方法组合成一个整洁的包。==

分成两个部分：类声明（.h 文件中）和类成员函数定义（.cpp 文件中）

==数据和函数成员都可以放在 private 和 public 进行声明，但是隐藏数据是 OOP 的主要目标之一，因此数据项通常放在 private 部分。==

==访问控制，public 成员提供了 访问private 成员的桥梁==

```C++
// 类声明，首字母大写 .h 文件中
#include <iostream>
class Stock
{
    // privat 可以省略，因为默认就是 private
    private:  // 数据隐藏，不能直接访问，但是可以通过 public 中的方法来间接访问
    		 std::string company;
    		 long shares;
    		 double value;
    		 double total_val;
    		 void set_total() {total_val = shares*value; }  // 自动转化为内联函数
    public:
    		void acquire(const std::string &co, long n, double price);
    		void buy(long num, double price);
    		void sell(long num, double price);
    		void show(void);
};
// 类成员函数的定义  .cpp 文件中
void Stock::acquire(const std::string &co, long n, double price)
{
    company = co;
    shares = n;
    value = price;
    set_total();  // 在类的方法中调用类的函数，不需要写 Stock::set_total()
}
void Stock::buy(long num, double price)
{
    shares += num;
    value = price;
	set_total();
}
void Stock::sell(long num, double price)
{
	shares -= num;
    value = price;
    set_total();
}
void Stock::show(void)
{
    std::cout<<"company: "<<company<<" , shares: "<<shares<<" and price: "<<price<<" total: "<<totao_val<<std::endl;
}
    // 定义类的对象, 使用类的对象
Stock Lihua;
Lihua.acquire("a",20,11.1);
Lihua.show();
Lihua.buy(12,10.1);
Lihua.show();
Lihua.sell(25,10.5);
Lihua.show();

```

==结构体中只有数据，相当于都是 public 类型，当还有对数据的操作时，才使用类==

==定义位于类声明中的函数都将自动成为内联函数，类声明通常将短小的成员函数作为内联函数。==

![202309021](D:\Administrator\Pictures\forTypora\202309021.jpg)





### 10.3 类的构造函数和析构函数

要解决的问题：初始化类的数据成员（private）

```C++
Stock Lily = {200,1.2,240};  // ERROR 只有数据变量是 public 时候才可
```

1. 类构造函数，用于构造新对象，将值赋给它们的数据成员

声明和定义构造函数，名字和类一样，无返回值

```C++
// 声明，在 class Stock 的 public 中
Stock(const std::string company_,long shares_, double value_=0);  // 可设置默认参数
// 定义
Stock::Stock(const std::string company_, long shares_, double value_)
{
	company =company_;
	shares=shares_;
	value=value_
	set_total();
}
// 使用
Stock food = Stock("strawberry",19,11.1);  // 显式调用构造函数
Stock animal1("fox",39,22.2);   // 隐式调用构造函数
```

若不声明构造函数，C++ 自动提供默认构造函数，是默认构造函数的隐式版本，不做任何工作。可能如下

默认构造函数在类的实例化时才会生成。

```C++
Stock::Stock(){}
```

***

当然，也可以声明构造函数，同时自己声明默认构造函数

- 方法一：使用全部带默认参数（默认参数只在声明中体现）

```C++
Stock::Stock(const std::string company_="error", long shares_=0, double value_=0);  //声明
Stock::Stock(const std::string company_, long shares_, double value_)  // 定义
{
	company =company_;
	shares=shares_;
	value=value_
	set_total();
}
```

- 方法二：通过函数重载来定义另一个构造函数

```C++
Stock::Stock()  // 不带任何参数
{
	company ="error";
	shares=0;
	value=0
	set_total();
}
```

**默认构造函数就是在调用时不需要显示地传入实参的构造函数**

==**上述两种方法，不能同时使用，因为他们都是默认构造函数。**==有了构造函数和默认构造函数后，初始化方法如下

```C++
Stock one;  // 调用的是默认构造函数  隐式
Stock one = Stock();   // 调用的是默认构造函数  显式
Stock two("two",1,2.2);  // 调用的是构造函数
Stock three("three");  // 调用的是构造函数，使用了默认参数
// 注意
Stock *four = new Stock;  // 调用的是默认构造函数  隐式
```

***

2. 析构函数

用构造函数创建对象后，程序负责跟踪该对象，直到其过期为止。对象过期之后，程序自动调用一个特殊的成员函数，析构函数。

析构函数完成清理工作，如构造函数使用 new 来开辟内存，则析构函数将使用 delete 释放这些内存。否则析构函数也没什么需要完成的工作（此时，让编译器生成一个声明都不做的隐式析构函数即可）

析构函数没有返回值，不带参数

```C++
// 声明，在类声明的 public 中
~Stock();
// 定义
Stock::~Stock()
{
	std::cout<<"byebye"<<company;
}
```

析构函数调用时间：

- 如果创建的是静态存储对象（全局变量），则其析构函数将在程序结束时自动被调用
- 如果创建的是自动存储类对象（局部变量），则其析构函数将在程序执行完代码块时自动被调用

==和构造函数一样，如果用户没有定义析构函数，编译器会自动隐式声明一个默认析构函数，当对象被删除后，执行默认析构函数的定义。==

***

类的对象可以相互拷贝，直接用 =

可以使用构造函数对类的对象重新初始化

```C++
Stock one("error",0,0);  // 本质上效率高，因为不用创建临时的对象
//  expression;  //
one = Stock("one",1,2);  // 重新初始化，本质创建一个临时的类的对象，然后进行对象赋值= 操作
```

***

`1. const 类的成员函数如何实现`

`在方法的声明和定义的后面加上 const`

```C++
const Stock two = Stock("two",22,22.22);  // 类的对象 two 是常量
two.show();  // error  因为 show 里面不能保证不修改 类的对象

// 解决方法，两步
// 在类的声明处
void show() const 
// 在函数定义处
void Stock::show() const
```

***

总结：构造函数是一种特殊的成员函数，在创建类的对象时被调用。构造函数可以重载，创建多个同名的构造函数，条件是每个构造函数的特征标都不同。

构造函数没有类型，当构造函数只有一个参数，也可以这样初始化 (不推荐)

```C++
Stock a = 32;
```

默认构造函数没有参数，因此，如果创建对象时没有进行显式初始化，则将调用默认构造函数，前提了自己定义了。

若没有定义任何构造函数，那么编译器会定义一个默认构造函数，里面什么也不做。

`2. 析构函数：对象被删除时，程序将调用析构函数，每个类只能有一个析构函数。`

析构函数没有返回值，没有参数。如果构造函数使用了new，那么析构函数要使用delete释放内存。析构函数定义原型如下

```C++
Stock::~Stock()
```

### 10.4 this 指针

参数中 const int &argument，则按引用来传递参数

this 指针指向正用来调用成员函数的对象

之前所有操作都只涉及到一个类的对象，当涉及到多个类的对象时，需要使用 this 指针。

```C++
// 问题描述：要比较两个类的对象的大小，并返回较大的那个对象
// 函数定义如下
const Stock & Stock::findMax(const Stock & a) const
{
    if(s.total_val>total_val)
        return s;
    else
        return ???  // ERROR 应该返回显式访问的对象（即调用该方法的对象），但是不知道怎么描述
}
// 函数调用
Stock stock1,stock2;
Stock max = stock1.findMax(stock2);  // 显式访问 stock1 对象，隐式访问 stock2 对象
// 或者 Stock max = stock2.findMax(stock1);  
```

==上述问题，解决方法，使用 this 指针，this 指针指向正用来调用成员函数的对象（this 被作为隐藏参数传递给方法）。==

==每个成员函数（包括构造和析构函数）都有一个 this 指针，所有类的方法中， this 指针都设置为调用它的对象的地址。==

==在类的成员函数后面加上 const ，则 this 指针也限定为 const==

```C++
// 函数定义如下
const Stock & Stock::findMax(const Stock & a) const
{
    if(s.total_val>total_val)  // total_val == this->total_val;
        return s;
    else
        return *this;
}
// 方法调用
Stock max = stock1.findMax(stock2); // this 指针指向 stock1 对象的地址
```



### 10.5 对象数组

==需要创建同一个类的多个对象，可以创建独立对象变量，但是也可以创建对象数组。==

```C++
Stock myStock[5];  // 创建一个数组，数组中每个元素都是 Stock 类的对象
// 创建对象数组进行初始化：
Stock test[5] = {
    Stock("name1",22, 44.44),  // 显式使用构造函数
    Stock("name2",33, 11.11),
    Stock()  // 显式使用默认构造函数
    // 剩余两个对象元素也将隐式调用默认构造函数
};
```



### 10.6 类作用域

==类中定义的名称（如类数据成员名和类成员函数名）的作用域都为整个类，作用域为整个类的名称只是在该类中是已知的。==

==类的作用域表明，不能从外部直接调用类的成员以及公有函数，想要调用类的公有函数，必须通过类的对象。==

==在类的声明或成员函数定义中，可以使用未修饰的成员名称==，如

```C++
class Stock
{
	private: int amount; 
   			 double price; 
    		 double total_val;
    public:  void set_total(void){ total_val = amount*price; }  // 不需要写 Stock::set_total
}
```

否则，必须使用

- 直接成员运算符 .
- 间接成员运算符 ->
- 作用域解析运算符 ::

***

作用域为类的常量

有时，要用到符号常量的作用域为类，如

```c++
class Bakery
{
	private: const int MONTHS = 12;  // ERROR,类的声明只是描述类，而并没有创建类的对象
}
```

正确写法：

1. 方法一：在类中声明一个枚举，作用域为整个类

```C++
class Bakery
{
	private: enum{ MONTHS = 12 };  // 这种方式声明枚举并不会创建类的数据成员，即所有对象中都不包含枚举
    		 double cost[MONTHS];
	// MONTHS 只是一个符号名称，在作用域为整个类的代码中遇到它时，编译器将用 12 代替它。
}
```

2. 方法二：使用 static 定义常量，如

```C++
class Bakery
{                                      // 函数内加 static变量，则不调用该函数也会创建该变量
	private: static const int MONTHS = 12;  // 该常量将与其他静态变量存储在一起，而不是存储在对象中，
    										// 因此，只有一个MONTHS 变量，被所有 Bakery对象共享
    		 double cost[MONTHS];
}
```



### 10.7 抽象数据类型

abstract data type （ADT）以通用的方式描述数据类型，而没有引入语言或实现细节

例如：通过使用栈，总是从堆顶添加或删除数据

​		 使用栈来管理自动变量，当新的自动变量被生成后，它们被添加到栈顶；消亡时，从栈中删除它们。

```C++
#ifndef __STACK_H__
#define __STACK_H__

typedef unsigned long UL;  // 便于移植

class Stack
{
private:
	static int MAX = 10;
	LU items[MAX];
	int top;
public:
	Stack(void);
	bool isempty() const;
	bool isfull() const;
	bool push(LU &item);
	bool pop(LU &item);
};

#endif
```

.c

```C++
#include "stack.h"

Stack::Stack()
{
	top = 0;
}
bool Stack::isempty() const
{
	return top == 0;
}
bool Stack::isfull() const
{
	return top == MAX;
}
bool Stack::push(LU &item)
{
	if( top < MAX)
	{
		items[top++] = item;
		return true;
	}
	else
		return false;
}
bool Stack::pop(LU &item)
{
	if(top >0 )
	{
		item = items[--top];
		return true;
	}
	else 
		return false;
}


```

























### 10.8 总结与习题



一个常见警告

```C++
char *str = "golf";    // WAINING
const char *str = "golf";  // OK
```



列表

```C++
#ifndef __LIST_H__
#define __LIST_H_

typedef long int LI;
void visit_item(LI &item);

class list
{
private:
    static int MAX = 10;
    LI items[MAX];
    int top;
    
public:
    list();
    bool isFull(void);
    bool isEmpty(void);
    void addData(LI data); 
    void visit( LI (*pf)(Li &) );
};


#endif
```

.cpp

```c++
#include "list.h"
list::list()
{
	top = 0;
}
bool list::isFull(void)
{

}
bool list::isEmpty(void)
{

}
void list::addData(LI data)
{
	items[top++] = data;
}
void list::visit( LI (*pf)(Li &) )
{
	for(int i = 0; i<top; i++)
		pf(items[i]);

}
void visit_item(LI &item)
{
	cout<<item;

}
```







### 知识点复习，归纳与总结

***

> 1. 类需要析构函数吗

不一定需要

当在类的构造函数中使用了 new 来开辟内存空间，那么必须要创建析构函数，在函数里面使用 delete 来释放空间

***

> 2. 类需要一个虚析构函数吗

虚函数：当一个类有派生类时，使用基类创建了一个指针或者引用，那么该指针或引用可能指向了基类，也可能指向了派生类。派生类继承了基类的方法，也可能做了修改，那么此时到底是调用的基类的方法还是派生类的方法呢？



有些类需要虚析构函数，因为析构函数是虚的。

==只有基类，而没有派生类时，虚函数就没有意义了，完全不需要虚析构函数。（但是好习惯是定义虚析构函数，万一以后会有派生类）==

真正需要虚析构函数的场景：当基类具有派生类时，

***

备注：构造函数的调用顺序：派生类首先调用基类的构造函数，然后调用派生类的构造函数；

​		  析构函数的调用顺序相反：派生类首先调用派生类的析构函数，然后调用基类的析构函数。

==因此，最好在定义类的时候，把析构函数定义为虚析构函数，防止它在创建派生类时可能出现问题。==

***

> 3. 指针和引用的区别

C++ 既有指针，又有引用。很多时候作用差不多。

（1）==有空指针，但是没有空引用。若有可能需要指向一个不存在的变量，那么使用指针。因为，引用必须一直代表一个对象(定义时必须初始化)。==

（2）使用指针作为形参时，在函数中需要先判断指针是否为空。而引用则不需要。

（3）指针可以指向不同的对象，即可赋值；而引用总是指向它最初代表的变量。

​		 ==因此，需要在不同时候代表不同的变量，使用指针。确定了代表对象始终如一时，使用引用。==

```C++
string &rs = str1;
string *ptrStr = &str1;
rs = str2;  // 将str2 赋值给 str1， rs 仍然代表 str1，只是 str1 的值变成了 str2的值
ptrStr = str2;  // ptrStr 指向了 str2， 而 str1 的内容没有变化
```

（4）返回值是引用还是指针，只需要看对返回值的操作（对指针，需要 * 取出内容）

```C++
// 运算符的重载
string name = "LiMing";
cout<<name[0]; // OK
name[2]='L';  // OK
// [] 是二元运算符（有两个操作数），在类 string 中，对其进行了重载
返回值 String::operator[](int n)  // 返回值可以被读取显示也可被修改，故没有 const，要么是引用，要么指针
// 若为指针，应该为cout<<*name[0], 所以一定是引用
//  真实的写法如下
char & String::operator[](int n)
{
    return str[i];
}
```

==因此，在进行运算符的重载时候，使用指针实现不了功能，只能使用引用==

***

> 4. P121 复制构造函数，参数必须是常量的引用，必须按引用传递，而不能按值传递

```C++
Class_name(const Class_name  &)  // 接受一个指向类对象的常量引用作为参数
// 例如，String 类的赋值构造函数的原型如下：
StringBad(const StringBad &)

// 若写成
StringBad(StringBad a):value(a.value){}   // 按值传递，实参传递给形参，StringBad a = temp 那么会无限循环调用构造函数
// 正确写法
StringBad(const StringBad &a):value(a.value){}  
```





***

> P122 静态成员变量

==所有类的对象共享一个静态成员变量==

```C++
class Person
{
	public: string name;  
    static int age;
};
// 初始化
int Person::age = 18;
int main(void)
{
    Person p1,p2;
    cout<<p1.age<<" "<<p2.age;  // 都是 18
    p1.age = 20;
    cout<<p1.age<<" "<<p2.age;  // 都是 20 
}
```

***

> P123 ==静态成员变量其类型可以是当前类的类型，而其他普通成员变量不能，除非是指针==

```c++
class Person
{
	public: string name;  
	static Person p1;   // OK, 因为其存储空间并不在类中
    Person p2;          // ERROR，因为若可以的话，那么Person 需要为 p2 开辟内存空间，Person 的占用空间将无法确定
    Person *ptrPerson;  // OK，因为指针类型的占用空间大小是确定的
};
```

***

> P124 函数指针和函数重载

在 Qt 中常用，比如信号槽

```C++
void my_print(int a);  //  #1
void my_print(int a, int b);
int main(void)
{
    int x =10,y=20;
    my_print(x);  // 10
    my_print(x,y);   // 1020
    void (*ptrFunc)(int) = my_print; // 函数指针，指向 my_print #1 函数
    ptrFunc(x);   // 10
    ptrFunc(x,y);  // ERROR
    
}
void my_print(int a) { cout<<a; }  
void my_print(int a, int b) { cout<<a<<b; }
```

***

> P125 迭代器 iterator

迭代器：伴随着 C++ 的容器所产生的

容器：为了提高 C++ 编程的效率，标准库STL中提供了许多容器，如 vector

vector 可以通过 [] 来访问，而其他容器却不能。

==每个容器都有自己的内嵌迭代器，通过迭代器来访问容器中的数据；==

==迭代器（类型）可以看作泛化的指针，使用方法也是需要加 *==

```C++
int main(void)
{
    int arrInt[]={1,2,3,4,5};
    vector<int> vector_num(arrInt,arrInt+5); // 定义容器
    cout<<vector_num[0];  // 访问容器数据
    vector<int>::iterator pd;
    for(pd = vector_num.begin();pd!=vector_num.end;pd++)
        cout<<*pd;  // 通过迭代器来访问容器中的数据
}
```

***

> P126 类型转换运算符

```C++
class S
{
	operator const int(); // 将 S 类型转换为 const int类型
	operator int() const;  // const 放在函数后面，说明该函数不会修改被隐式访问的对象。将类的类型转换成为 int 类型，
          																    //但是不能通过这个转换运算符修改调用该转换符的对象
}
```

***

> P127 构造函数为什么不能是虚函数

==创建派生类对象时，程序首先调用基类构造函数，然后再调用派生类构造函数。基类构造函数负责初始化继承的数据成员，派生类构造函数主要用于初始化新增的数据成员。==

```C++
RatedPlayer::RatedPlayer(unsigned int r, const TableTenisPlayer & tp)
:TableTennisPlayer(tp),ratin(r)
{}
```

==虚函数：若要在派生类中重新定义基类的方法（同名函数），这样，程序将根据对象类型而不是引用或指针的类型来选择方法版本。通常将基类方法声明为虚的。（为基类声明一个虚的析构函数是惯例）==

第一点：若基类的构造函数定义为虚函数，那么派生类里面需要重新定义该函数。此时，创建派生类时，将调用派生类的构造函数，而不会调用基类的构造函数；

第二点：基类的构造函数与派生类的构造函数不可能一样的名字；

第三点：一个类随时有可能被当作基类，所以定义为虚构造函数会有风险。

***

> P128 当 const 碰上 typedef 类型别名

```C++
typedef char *ptrStr;  // ptrStr 为 char * 的别名
const ptrStr ptr01 = nullptr;  // ????? const char *ptr01 ????? const 修饰的是 char
// 本质上 == char *const ptr01
// 因为 const 修饰的是ptrStr 指针，所以是一个指针常量
```

常量指针：const int * ptrInt 不能通过指针修改指向的对象内容，但指针可以指向其他对象

指针常量：int *const ptrInt 指针只能指向特定位置，不允许修改指向位置，但是可以修改指向的内容

## chapter 11  使用类

### 11.1 运算符重载

```c++
operatorop(orgument-list)
如：
operator+()
```

**运算符重载：只能在原来有的运算符的基础上，进行重载，不能定义新的运算符**

```c++
class Num
{
	private:int n;
	public:
	Num();
	Num(int m);
	Num operator+(const Num &t) const;
    Num operator*(double multi) const;  
};
Num::Num(){n=0;}
Num::Num(int m){n=m;}
Num Num::operator+(const Num &t) const
{Num temp; 
temp.n = n+t.n;
return temp;}
Num Num::operator*(double multi) const
{Num temp; 
temp.n = n*multi;
return temp;}
```

**大部分运算符都可以通过成员或者非成员函数(只能操作public成员数据)进行重载，但是下面的运算符只能通过成员函数进行重载**

- =  赋值运算符
- () 函数调用运算符
- []  下标运算符
- -> 通过指针访类成员

```c++
Num a;  // 调用 Num();
a =10;  // 调用 Num(int m);  编译器会提供   = 默认的重载函数
Num b =a*2.2;  // OK  == a.operator*2.2
Num b =2.2*a;  //false  // 加上以下代码就可以
Num operator*(const Num &temp,double multi) const
{
return temp*multi;}
```



### 11.3  友元

非成员函数只能访问类里面public 成员，而友元函数不是成员函数，可以访问 private和public成员

友元函数定义时不用写 friend，和类名：：，友元函数不是通过类的对象来调用的，所以函数类型不能是 const

```c++
class Num
{
	public:
    Num operator*(double multi); // 可以实现 Num a; a=a*1.2;
    friend Num operator*(double multi，const Num &temp);  // 可以实现 Num a; a=1.2*a;
};
Num operator*(double multi，const Num &temp)  // 可以实现 Num a; a=1.2*a;
{
    Num result;
	result.n = temp.n*multi;  // 可以访问私有成员数据
    return result;
}
```

用友元重载 <<

要实现 cout << trip;，可以重载 cout 的 << 运算符，或者重载 Time 的运算符

```c++
void operator<<(ostream &os,const Time &t)
{
	os<<t.hours<<"hours,"<<t.minutes<<" minutes";
}
```

要实现 cout << trip <<"end";

```c++
ostream & operator<<(ostream &os,const Time &t)
{
	os<<t.hours<<"hours,"<<t.minutes<<" minutes";
	return os;
}
```



### 11.4 重载运算符

运算符重载的方法：

1. 使用成员函数（一个操作数+this）
2. 非成员函数（不能访问private成员）
3. 友元函数（两个操作数，函数不能是 const 类型，运算符的左边和右边的操作数与形参顺序一致）

### 11.5 重载：vector









### 11.6 类的自动转换和强制类型转换

类的转换函数

int 或其他类型数据可以强制转换为 类的类型，要实现相反的转换，可以通过运算符函数，即转换函数

```c++
Num wolfe(285.7);
wolfe = 2.2;

// 如何实现？？？？
double host = (double)wolfe;  //  
```

operator typeName()

- 转换函数必须是类方法
- 转换函数不能指定返回类型
- 转换函数不能有参数

如 operator double();  //将类的对象转换为 double 类型

```c++
class Num
{
	private:int n;
	public: Num(int i):n(i){}
    operator double() const;
};

Num::operator double() const
{
	return (double)(n+0.1)
}
// 使用
Num num1 = 90; // 利用构造函数进行了隐式转换
double i = num1;
double ii = (double)num1;  // 与上面等效
```

当类定义了多个转换函数时，需要显式的转换如 (double)num1，隐式会出现二义性

**explicit operator double() const;  则只可以显式转换**

**构造函数中只有一个参数时，将 double 值赋给该对象时，会调用该构造函数，实现隐式的类型转换。在构造函数声明中添加 explicit 可以防止隐式，而只允许显式转换。**



### 11.7 总结

C++会自动提供以下成员函数：

1. 默认构造函数：如果没有定义构造函数

2. 默认析构函数：如果没有定义

3. **复制构造函数**：如果没有定义

   作用：用于类的对象初始化  

   ```c++
   StringBad s3 = StringBad("two"); 
   ```

   调用时间：每当生成了对象副本时编译器将使用复制构造函数。即函数按值传递对象或函数返回对象时，都将使用复制构造函数。按值传递时，就会生成对象的副本

   因此，尽量使用按引用传递，这样不用调用复制构造函数，并节省空间。

4. **赋值运算符**：如果没有定义  

   ```c++
   StringBad s2;  
   s2 = StringBad("two");
   ```

   

5. **地址运算符**：如果没有定义







## chapter 12  类和动态内存分配

为什么需要动态内存分配：类的数据对象中有数组，需要定义为足够大的固定长度的数组，很容易浪费空间。

因此，在定义类的对象时，程序运行时动态地为它创建存储空间，能够节省空间

在类中如何使用 new 和 delete

```c++
class StringBad
{
	private: 
	char *str; int len;
	static int num_strings;  // 常用来做记录，不属于任何一个类的对象
	public:
    	StringBad(const char *s);
    	StringBad();
    	~StringBad();
    ostream & operator<<(ostream &os,const StringBad &s1);
};

 // .c
#include <cstring>
int StringBad::num_strings = 0;  // 不能在 .h 中，因为.h会被包含多次

StringBad::StringBad(const char *s)
{
    len = strlen(s);
    str = new char[len+1];
    strcpy(str,s);
    ++num_strings;
}
StringBad::StringBad()
{
    len = 1;
    str = new char[1];
    strcpy(str,'\0');
    ++num_strings;
}
StringBad::~StringBad()
{
    delete []str;
    --num_strings;
}
ostream & StringBad::operator<<(ostream &os,const StringBad &s1)
{
    os<<str<<endl;
    return os;
}

// 使用：对象初始化
StringBad s1("one");
StringBad s2 = s1;   // 与下面等效
StringBad s3 = StringBad(s1);  // ERROR，默认的复制构造函数为 StringBad(cosnt StringBad &); 里面不会有new，故对象销毁时，delete出错

// 给类赋值
StringBad s2;
s2 = s1;    // ERROR，因为会创建临时对象
```



类中出现了new，则必须定义自己的复制构造函数、赋值运算符，以及析构函数

```c++
class
{
	public:
		StringBad(const StringBad &s1);
		StringBad & operator=(const StringBad &s1);
};
StringBad::StringBad(const StringBad &s1)
{
    len = s1.len;
    str = new char[len+1];
    strcpy(str,s1);
    ++num_strings;
}
StringBad & StringBad::operator=(const StringBad &s1)
{
    if(this == s1)
        return *this;
    delete []str;
    len = s1.len;
    str = new char[len+1];
    strcpy(str,st.str);
    return *this;
}
```

重载运算符

```c++
class
{
    private:static const int CINLIMIT = 80;
	public:
		StringBad & operator=(const char *s);
		char & operator[](int i);  // 不够严谨
		const char & operator[](int i) const; // 足够严谨
		friend bool operator<(const StringBad &s1,const StringBad &s2);
		friend bool operator>(const StringBad &s1,const StringBad &s2);
		friend bool operator==(const StringBad &s1,const StringBad &s2);
		friend istream & operator>>(istream &is,StringBad &s1);
		static int HowMany();  // 静态函数，只能调用 StringBad::HowMony() 来使用
};

StringBad & StringBad::operator=(const char *s)
{
    delete []str;
    len = strlen(s);
    str = new char[len+1];
    strcpy(str,s);
    return *this;
}
char & StringBad::operator[](int i)  //char & 是指 s1[0]返回的是s1[0]，而不是副本
{
    return str[i];
}
const char & StringBad::operator[](int i) const
{
    return str[i];
}
bool operator<(const StringBad &s1,const StringBad &s2)
{
	return (strcmp(s1.str,s2.str)<0);
}
bool operator>(const StringBad &s1,const StringBad &s2)
{
	return s1<s2;  // 调用了上面的 < 运算符
}
bool operator==(const StringBad &s1,const StringBad &s2)
{
	return (strcmp(s1.str,s2.str)==0);
}

istream & operator>>(istream &is,StringBad &s1)
{
    char temp[StringBad::CINLIMIT];
    is.get(temp,StringBad::CINLIMIT);
    if(is)
        s1 = temp;
    while(is && is.get!='\n')  // 当输入的字符超出了最大长度 CIMLIMIT 
        continue;
    return os;
}
int StringBad::HowMany()
{
    return num_strings;
}
```





### 12.3 在构造函数中使用 new 注意事项

构造函数中用了 new，要定义 虚析构函数，其中要 delete

new 与 delete 成对出现，delete 也可以用于空指针

空指针 0，NULL， nullptr

new [] 与 delete [] 成对出现

### 12.5 使用指向对象的指针

- 使用常规表示法来声明指向对象的指针

```c++
StringBad *glamour;
glamour = &string1;
```

- 使用 new 来初始化，这将创建一个新的对象

```c++
StringBad *gleep = new StringBad;   // 调用默认构造函数 StringBad::StringBad()
StringBad *gleep = new StringBad("two");  // 调用构造函数 StringBad::StringBad(const char *)
StringBad *gleep = new StringBad(string1);//调用构造函数StringBad::StringBad(const StringBad &s1)
```

- new 开辟的类对象，在使用 delete 删除对象时才会调用类的析构函数






- 定位 new 运算符创建的对象，不能搭配 delete 来删除对象，需要显式调用析构函数


```c++
char *buffer = new char[100];
StringBad *s1,*s2，*s;
s1 = new (buffer) StringBad;  // s1 的地址与 buffer 的地址一样
s2 = new (buffer+sizeof(StringBad)) StringBad; 
s3 = new StringBad("OK");

delete s3;  // OK
delete []buffer;  // 不会调用析构函数
delete s1;  // 不会调用析构函数  == delete []buffer
s2->~StringBad();  // 显式调用析构函数
s1->~StringBad(); 
```

---

成员初始化列表：

只用于构造函数

必须用这种格式来初始化 非静态const 数据成员，（静态const成员是指 static const）

必须用这种格式来初始化引用数据成员

### 12.7 使用链表实现队列 Queue

队列是一种抽象的数据类型

队列：先进先出

栈：先入后出

使用单向链表，每个节点地址不连续

```
struct Node{
 Item item;
 struct Node *next;
}
```







## chapter 13  类继承

### 13.1 

```c++
class Num2 : public Num  // 公有派生
{};
```

需要在继承中添加：

- 派生类需要自己的构造函数（构造函数中需要给新成员和继承的成员提供数据）
- 派生类可以根据需要添加额外的数据成员和成员函数

public继承的类，不能直接私有成员，构造函数也不能直接私有成员，必须通过基类公有方法进行访问。

```
class Num2 : public Num  // 公有派生
{
	private :char val2;
	public:
		Num2(char v2,int v1):Num(v1){ val2 = v2; }  // 成员初始化列表，初始化之后才会执行构造函数
};   // 先隐式创建了基类对象，即使没有Num(v1)，也会调用基类默认的构造函数
```

```c++
class Num2 : public Num  // 公有派生
{
	private :char val2;
	public:
		Num2(char v2,int v1)；;
};   
Num2::Num2(char v2,int v1):Num(v1)
{ val2 = v2; } 

// 方法二，定义派生类的构造函数
Num2::Num2(char v2,const Num &n1):Num(n1)  // 会自动生成 复制构造函数
{ val2 = v2; } 
```

派生类构造函数的要点：

- 首先创建基类对象
- 派生类构造函数应通过成员初始化列表将基类信息传递给基类构造函数
- 派生类构造函数应初始化派生类新增的数据成员

构造函数调用顺序：先调用基类的，再调用派生类的

析构函数调用顺序：先调用派生类的，再调用基类的

---

**基类的指针可以指向派生类的对象，基类的引用也可以引用派生类的对象，但是他们只能调用基类的方法**

**相反，派生类的指针不可以指向基类的对象**

```c++
Num2 n2;
Num &n3 = n2;
Num *n4 = &n2;
```

可以使用派生类的对象给基类对象初始化，也可以将派生类对象赋值给基类对象

```  c++
Num2 n4(3.14,2); 
Num n5(n4);  // OK  n5.val = 2  // 隐式调用 复制构造函数
Num n6 = n4; // OK  // 隐式调用赋值运算符
```

---

复制构造函数：Num n5( Num n4);

类定义中可以不写复制构造函数，编译器会自动生成默认的

```c++
Num(const Num &);
```

隐式重载复制运算符

```c++
Num & operator=(const Num &) const;
```









### 13.2 is-a 关系的继承







### 13.3 多态公有继承

如何使得基类和派生类调用同一个方法，实现不同的操作

- 在派生类中重新定义基类的方法
- 使用虚函数 virtual

virtual 只需要在函数声明时出现



```c++
// 如果没有 virtual
Brass dom;
BrassPlus dot;

Brass &b1_ref = dom;
Brass &b2_ref = dot;
b1_ref.ViewAcct();  // 调用的是  Brass::ViewAcct()
b1_ref.ViewAcct();  // 调用的是  Brass::ViewAcct()

// 如果基类中有 virtual void ViewAcct() const;
Brass dom;
BrassPlus dot;

Brass &b1_ref = dom;
Brass &b2_ref = dot;
b1_ref.ViewAcct();  // 调用的是  Brass::ViewAcct()
b1_ref.ViewAcct();  // 调用的是  BrassPlus::ViewAcct()
dom.ViewAcct();   // 调用的是  Brass::ViewAcct()
dot.ViewAcct();   // 调用的是  BrassPlus::ViewAcct()
```

派生类中，virtual void ViewAcct() const; 的 virtual 可以省略，但是一般不省略

也可以直接使用 Brass::ViewAcct(); 来调用该函数





### 13.4 静态联编和动态联编

静态联编：在编译过程就确定使用 哪个函数

动态联编：程序运行时才能确定使用哪个函数，编译器必须生产能够在运行时选择正确的虚函数的代码

只有但虚函数存在在，才会动态联编

**如果要在派生类中重新定义基类的方法，则将它设置为虚函数**

1. 基类的构造函数不能是虚函数
2. 基类的析构函数应该是虚函数
3. 友元函数不能是虚函数，因为友元函数不属于类



### 13.5 访问控制，protected

在类外只能用公有类成员来访问 protected 部分中的类成员

private 和 protected 之间的区别只有在基类派生的类中才会体现出来

**派生类的成员可以直接访问基类的保护成员，但不能直接访问基类的私有成员**

**因此，数据成员最好使用 private，而不使用 protected**

### 13.6 抽象基类

抽象：椭圆包含了园，如果由 class 椭圆 派生出 class 圆，则浪费了资源，他们椭圆中的很多特性用不到。抽象就是：把椭圆和圆的共性抽象成一个类，而椭圆和圆都是派生类



纯虚函数：在基类中定义的虚函数，未实现

**具有纯虚函数的类，也叫抽象类，抽象类不能实例化，不能创建类的对象，只能当作基类来用**

```c++
virtual double Area() const  = 0; // 声明，定义可写可不写
```

### 13.7 继承和动态内存分配

类里面没有new，可以不定义复制构造函数，使用默认的复制构造函数  Num(const Num &);

类里面出现了new，就不能使用默认复制构造函数，因为复制构造函数不会使用new，而析构函数中有 delete

**派生类会先调用基类的复制构造函数，然后再使用派生类的复制构造函数**

如果基类中有 new，必须定义复制构造函数，派生类中如果没有new，则派生类可以不定义复制构造函数，使用默认的

- **第一种情况：派生类中没有 new**

**派生类可以不创建复制构造函数**，因为会先调用基类的复制构造函数（自己定义的），然后再调用派生类的默认复制构造函数。

**派生类可以不创建析构函数**

**派生类可以不实现运算符的重载**



- **第二种情况：派生类中有 new**

  **当基类和派生类都有new时，必须为派生类显式定义构造函数，复制构造函数和赋值运算符**

```c++
class baseDMA
{
	private:
		char *name;
	public:
};

class DMA:public baseDMA
{
	private:
		char *style;
	public:
		DMA(const char *l,const char *h):baseDMA(l){ style = new char[10];strcpy(style,s);}
		DMA(const DMA &,const char *h);
		DMA(const DMA &);
		DMA &operator=(const DMA &) const;
		~DMA();

};
DMA::DMA(const DMA &,const char *h):baseDMA(h)
{
    style = new char[10];strcpy(style,s);
}
DMA::DMA(const DMA &h):baseDMA(h)
{
    new char[10];strcpy(style,h);
}
DMA::~DMA()
{
    delete [] style;
}
DMA & DMA::operator=(const DMA &h) const
{
    if(this == &h)
        return *h;
    baseDMA.operator=(h);
    
    
}

// 使用
DMA m1;
DMA m2 = m1; // 调用的是 DMA(const DMA &h)初始化时调用的是复制构造函数
DMA m3;
m3 = m1 ;  // 调用的是 DMA & DMA::operator=(const DMA &h) const
```

---

### 13.8 总结

**1. 复制构造函数**：如果出现中没有复制构造函数，编译器将提供默认

Num(cosnt Num &)

**使用 new 初始化的成员指针通常要求深拷贝，或者类可能包含需要修改的静态变量，这些情况下需要定义自己的复制构造函数**

作用

- 将新对象初始化为一个同类对象
- 按值将对象传递给对象
- 函数按值返回对象
- 编译器生成临时对象

---

**2. 赋值运算符**：类的对象赋值给类的对象，= 两边都是类的对象。如果没有，则编译器会提供默认

**使用 new 初始化的成员指针通常要求深拷贝，或者类可能包含需要修改的静态变量，这些情况下需要定义自己的赋值运算符**。自己定义了复制构造函数，也要定义赋值运算符

Num & operator=(const Num &)

```c++
DMA m1;
DMA m2 = m1; // 调用的复制构造函数
DMA m3;
m3 = m1 ;   // 调用的赋值运算符
```

赋值运算符是用来，类的对象赋值给类的对象，= 两边都是类的对象

---

转换函数：将类的对象转换为其他类型

将其他数据类型赋值给类的对象，使用带有一个参数的析构函数

构造函数只用于从某种类型到类类型的转换

```c++
Num (const double i);  // 将 double 类型转换为 Num 类型
```

**3. 转换函数（运算符函数）实现相反的操作：将类的对象转换为其他类型**

```c++
Num
{
	double data;
	public: operator(int) const;
};
Num n1;
int a = n1;
int a = (int)n1;   // 加了explicit 只能进行显式转换，explLicit operator(int) const;
```

转换函数，加 explicit 允许使用强制类型转换进行显式转换

---

4. 析构函数，构造不能被继承，因为类名字不同

必须定义显式的析构函数， 来释放类构造函数使用 new 分配的所有内存。

**对于基类，即使它不需要析构函数，也应提供一个虚析构函数。以此来保证，类的指针和引用，先调用派生类的析构函数，再调用基类的析构函数**

---

5. 按值传递与传递引用

对象作为参数时，应按引用来传递对象，可以提高效率。

基类的指针和引用，可以指向派生类的对象

---

6. 返回对象和返回引用

返回引用可以提高效率

7. 使用 const

三种情况：

1. 函数形参带 const：函数不修改该参数
2. 成员函数末尾带 const：该函数不能修改，this 指向的对象
3. 函数返回值带 const ：函数中使用的对象都是const类型，返回它们的引用也需要加 const

构造函数和析构函数不能被继承，因为类名字都不一样。派生类对象释放时，先调用派生类析构函数，再调用基类构造函数。为了保证析构函数调用顺序，基类的析构函数是 virtual 虚函数

赋值运算符不能被继承，Num & operator=(const Num &)，形参不一样

**当类里面出现了 new ，则需要自己定义复制构造函数，赋值运算符，析构函数**

**当类里面没有出现new，可以不定义复制构造函数，赋值运算符，析构函数，编译器会生成默认的**

---

**8. 基类对象 与 派生类对象 相互赋值 =**

**（1）派生类对象可以赋值给基类对象，因为会调用基类的赋值运算符，给基类的成员进行了赋值**

```c++
class baseBrass
{};
class Brass:public baseBrass
{};

Brass b1;
baseBrass b2 = b1;  //  == b2.operator= (b1)
```

**（2）基类对象赋值给派生类对象，一般不能实现，硬要实现**

方法一：派生类中定义了下面转换构造函数

```c++
Brase(const baseBrase &);   // 派生类构造函数
```

方法二：在派生类中定义赋值运算符

```c++
Brass &Brass::operator=(const baseBrase &);
```

---

9. 私有成员与保护成员：最好将数据成员定义为私有，不要使用保护成员

对于外部而言：私有成员与保护成员类似

对于派生类而言：保护成员类似于公有成员，派生类可以直接访问基类的保护成员，但是只能通过基类的成员函数来访问基类的私有成员

---

10. 虚函数：如果希望派生类重新定义方法，则应在基类中将方法定义为虚函数；否则不要定义为 virtual

基类的构造函数为 virtual（基类一定要有虚析析构函数，即使函数为空）

类的引用或者指针调用的是基类还是派生类的方法，要看指针指向的是哪个类

```c++
void show(const baseBrase &b1)  // 当view（）为虚函数时，调用baseBrase::view()或Brase::view()
{ ba.view();v }        
void show(baseBrase b1)  // 调用 baseBrase::view()
{ ba.view(); }
```

---

11. 析构函数基类的虚构是虚函数，这样，当通过指向对象的基类指针或引用来删除派生对象时，会先调用派生类的析构函数，再调用基类的析构函数

12. 友元函数：友元函数并非类成员，因此不能继承。派生类的友元函数如何使用基类的友元函数，可以使用强制类型转换，派生类引用或指针转换为基类引用或指针，然后使用转换后的指针或引用来调用基类的友元函数

```c++
osream &operator <<( ostream &os,const Brase &b1)   // 派生类的友元函数定义
{
	os<<(baseBrase &)b1;  // 使用基类里面的友元函数
	os<<b1.style<<endl;
	return os;
}
```











## chapter 14 代码重用

### 14.1 包含对象成员的类

valarray 处理数组数值的类，支持诸如将数组中所有元素相加以及在数组中找出最大和最小值等操作。

- operator[]() ： 访问元素
- size()  ：返回元素数量
- sum()
- max()
- min()

```c++
valarray<int> q_values;   // size 0
valarray<double> v2(8);  // 8个元素
valarray<double> v3(10.11,8);  // 8个元素，每个元素都是10.11
valarray<double> v4={10.11, 8.8, 6.6}; 
```

```c++
class Student
{
	private: string name;
	valarray<double> scores;  // 两个有名类对象
};
```



### 14.2 私有继承 private

公有继承：基类的公有方法成为派生类的公有方法

私有继承：基类的方法将成为派生类的私有方法

私有继承的基类叫 私有基类，私有派生类

私有基类的公有成员和保护成员成为派生类的私有成员，私有基类中的成员，派生类不可直接访问，只能通过私有基类的接口访问。

**私有基类的公有成员和保护成员在派生类中是私有成员，只有私有派生类的成员函数可以访问**，在私有派生类外面不能访问。

```c++
class Student:private std::string, private std::valarray<double>  // 多重继承
{	// 类里面已经包含了两个无名的子对象成员
    public:
    Student():std::string("null"),std::valarray<double>(){}  // 使用了类名来初始化
};
```

---

使用包含还是私有继承：包含较好理解，可以包含多个独立的 string 对象，而私有继承只能使用一个无名对象

使用私有继承，需要重新定义虚函数，

**如果新类需要用到其他类中的部分方法，则使用包含；如果新类需要访问原有类的保护成员，或需要重新定义虚函数，则使用私有继承**



---

**保护继承 protecte**

使用保护继承，基类的公有成员和保护成员都将成为派生类的保护成员。基类的接口在派生类中也是可用的，但在继承层次结构之外是不可用的。

当从派生类派生出另一个类时，私有继承和保护继承直接的区别：

私有继承时：第三代类不能使用基类的接口，这是因为基类的公有方法在派生类中成为了私有方法。

保护继承时：基类的公有方法在第二代中变成保护的，因此第三代类中可以使用。

---

三种继承方法：

| 特征             | public               | protected              | private              |
| ---------------- | -------------------- | ---------------------- | -------------------- |
| 公有成员         | 公有成员             | 保护成员               | 私有成员             |
| 保护成员         | 保护成员             | 保护成员               | 私有成员             |
| 私有成员         | 只能通过基类接口访问 | 只能通过基类接口访问   | 只能通过基类接口访问 |
| 能否隐式向上转换 | 是                   | 是（但只能在派生类中） | 否                   |

---

**使用 using 重新定义访问权限**

为了让基类的方法在派生类外面使用

**方法一：定义一个使用该基类方法的派生类方法**

```c++
double Student ::sum() const
{
	retrun std::valarray<double>::sum();  // 使用私有继承中基类的方法
}
```

方法二：在派生类的公有部分加入  using

```c++
class Student:private std::string,private std::valarray<double>
{
	public:
	using std::valarray<double>::max;  // 只写成员名，不用带 ()
	using std::valarray<double>::min;
};
```

上述 using 声明使得 valarray<double>::max() 和 valarray<double>::min() 可用，相当于 Student 类的公有方法。





### 14.3 多重继承











## chapter 15 



## chapter 16 

















































