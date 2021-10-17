> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/ksws0292756/article/details/79432329?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163444061416780274151520%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163444061416780274151520&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-79432329.pc_search_ecpm_flag&utm_term=C%2B%2B+%E5%AD%97%E7%AC%A6%E6%95%B0%E7%BB%84+%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1018.2226.3001.4187)

在 C++ 中，有两种类型的字符串表示形式：

*   C - 风格字符串
*   C++ 引入的 string 类

C - 风格字符串
---------

C 风格的字符串起源于 C 语言，并在 C++ 中继续得到支持。字符串实际上是使用 null 字符 ‘\0’ 终止的一维字符数组。因此，一个以 null 结尾的字符串，包含了组成字符串的字符。  
下面的声明和初始化创建了一个 “Hello” 字符串。由于在数组的末尾存储了空字符，所以字符数组的大小比单词 “Hello” 的字符数多一个。

```
char greeting[6] = {'H', 'e', 'l', 'l', 'o', '\0'};
```

其实，您不需要把 null 字符放在字符串常量的末尾。C++ 编译器会在初始化数组时，自动把 ‘\0’ 放在字符串的末尾。所以也可以利用下面的形式进行初始化

```
char greeting[] = "Hello";
```

以下是 C/C++ 中定义的字符串的内存表示：  
![](https://img-blog.csdn.net/201803031918422?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva3N3czAyOTI3NTY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
C++ 中有大量的函数用来操作以 null 结尾的字符串：

<table><thead><tr><th>序号</th><th>函数</th><th align="right">功能</th></tr></thead><tbody><tr><td>1</td><td>strcpy(s1,s2)</td><td align="right">复制字符串 s2 到字符串 s1</td></tr><tr><td>2</td><td>strcat(s1,s2)</td><td align="right">连接字符串 s2 到字符串 s1 的末尾</td></tr><tr><td>3</td><td>strlen(s1)</td><td align="right">返回字符串 s1 的长度</td></tr><tr><td>4</td><td>strcmp(s1,s2)</td><td align="right">返回 s1 与 s2 的比较结果</td></tr><tr><td>5</td><td>strchr(s1,ch)</td><td align="right">返回一个指针，指向字符串 s1 中字符 ch 的第一次出现的位置</td></tr><tr><td>6</td><td>strstr(s1,s2)</td><td align="right">返回一个指针，指向字符串 s1 中 s2 的第一次出现的位置</td></tr></tbody></table>

C++ 中的 String 类
---------------

C++ 标准库提供了 string 类类型，支持上述所有的操作，另外还增加了其他更多的功能。比如：

*   append() – 在字符串的末尾添加字符
*   find() – 在字符串中查找字符串
*   insert() – 插入字符
*   length() – 返回字符串的长度
*   replace() – 替换字符串
*   substr() – 返回某个子字符串
*   …

4 种字符串类型
--------

C++ 中的字符串一般有以下四种类型，

*   string
    
*   char*
    
*   const char*
*   char[]

下面分别做简单介绍，并说明其中的一些区别

### string

string 是一个 C++ 类库中的一个类，它位于名称空间 std 中，因此必须使用 using 编译指令或者 std::string 来引用它。它包含了对字符串的各种常用操作，它较 char * 的优势是内容可以动态拓展，以及对字符串操作的方便快捷，用 + 号进行字符串的连接是最常用的操作

### char*

char* 是指向字符串的指针 (其实严格来说，它是指向字符串的首个字母)，你可以让它指向一串常量字符串。

### const char*

该声明指出，指针指向的是一个 const char 类型，即不能通过当前的指针对字符串的内容作出修改

注意这里有两个概念：

*   char * const [指向字符的静态指针]
*   const char * [指向静态字符的指针]

**前者 const 修饰的是指针，代表不能改变指针**  
**后者 const 修饰的是 char，代表字符不能改变，但是指针可以变，也就是说该指针可以指针其他的 const char。**

### char[]

与 char * 与许多相同点，代表字符数组，可以对应一个字符串，如

```
char * a="string1";
char b[]="string2";
```

这里 a 是一个指向 char 变量的指针，b 则是一个 char 数组（字符数组）  
也就是说：

**二者的不同点**

**一，char * 是变量，值可以改变， char[] 是常量，值不能改变！**  
a 是一个 char 型指针变量，其值（指向）可以改变；  
b 是一个 char 型数组的名字，也是该数组首元素的地址，是常量，其值不可以改变

**二，char[] 对应的内存区域总是可写，char * 指向的区域有时可写，有时只读**  
比如：

```
char * a="string1";
char b[]="string2";
gets(a); //试图将读入的字符串保存到a指向的区域，运行崩溃！ 
gets(b) //OK
```

**解释**： a 指向的是一个字符串常量，即指向的内存区域只读；  
b 始终指向他所代表的数组在内存中的位置，始终可写！

注意，若改成这样 gets(a) 就合法了：

```
char * a="string1";
char b[]="string2";
a=b; //a,b指向同一个区域
gets(a) //OK
printf("%s",b) //会出现gets(a)时输入的结果
```

**解释**： a 的值变成了是字符数组首地址，即 & b[0]，该地址指向的区域是 char * 或者说 char[8]，习惯上称该类型为字符数组，其实也可以称之为 “字符串变量”，区域可读可写。

**总结**：char * 本身是一个字符指针变量，但是它既可以指向字符串常量，又可以指向字符串变量，指向的类型决定了对应的字符串能不能改变！

**三，char * 和 char[] 的初始化操作有着根本区别：**  
测试代码：

```
char *a="Hello World"; 
char b[]="Hello World"; 
printf("%s, %d\n","Hello World", "Hello World"); 
printf("%s, %d %d\n", a, a,  &a);                           
printf("%s, %d %d\n", b,     b,  &b);
```

结果：

```
Hello World，13457308
Hello World，13457308    2030316
Hello World，2030316 2030316
```

结果可见：尽管都对应了相同的字符串，但”Hellow World” 的地址 和 a 对应的地址相同，与 b 指向的地址有较大差异；&a 、&b 都是在同一内存区域，且 & b==b  
根据 c 内存区域划分知识，我们知道，局部变量都创建在栈区，而常量都创建在文字常量区，显然，a、b 都是栈区的变量，但是 a 指向了常量（字符串常量），b 则指向了变量（字符数组），指向了自己 (&b==b==&b[0])。  
说明以下问题：  
**char * a=”string1”; 是实现了 3 个操作：**

1.  声明一个 char * 变量 (也就是声明了一个指向 char 的指针变量);
2.  在内存中的文字常量区中开辟了一个空间存储字符串常量”string1”
3.  返回这个区域的地址，作为值，赋给这个字符指针变量 a

最终的结果：指针变量 a 指向了这一个字符串常量 “string1”  
（注意，如果这时候我们再执行：char * c=”string1”；则，c==a，实际上，只会执行上述步骤的 1 和 3，因为这个常量已经在内存中创建）

**char b[]=”string2”; 则是实现了 2 个操作：**

1.  声明一个 char 的数组，
2.  为该数组 “赋值”，即将”string2” 的每一个字符分别赋值给数组的每一个元素

最终的结果：“数组的值”（注意不是 b 的值）等于”string2”，而不是 b 指向一个字符串常量

**实际上， char * a=”string1”; 的写法是不规范的！**  
因为 a 指向了即字符常量，一旦 strcpy(a,”string2”) 就糟糕了，试图向只读的内存区域写入，程序会崩溃的！尽管 VS 下的编译器不会警告，但如果你使用了语法严谨的 Linux 下的 C 编译器 GCC，或者在 windows 下使用 MinGW 编译器就会得到警告。  
所以，我们还是应当按照” 类型相同赋值” 的原则来写代码：

```
const char * a="string1";
```

保证意外赋值语句不会通过编译

另外，关于 char * 和 char[] 在函数参数中还有一个特殊之处，运行下面的代码

```
void fun1 ( char *p1,  char p2[] ) {
 printf("%s %d %d\n",p1,p1,&p1);
 printf("%s %d %d\n",p2,p2,&p2);
p2="asdf"; //通过! 说明p2不是常量！ 
printf("%s %d %d\n",p2,p2,&p2);
}
void main(）{
char a[]="Hello";
fun1(a,a);
}
```

运行结果：

```
Hello 3471628 3471332
Hello 3471628 3471336
asdf 10704764 3471336
```

结果出乎意料！上面结果表明 p2 这时候根本就是一个指针变量！  
**结论是：作为函数的形式参数，两种写法完全等效的！都是指针变量！**

**const char * 与 char[] 的区别：**  
const char * a=”string1”  
char b[]=”string2”;  
二者的区别在于：

1.  a 是 const char _类型， b 是 char_ const 类型  
    （ 或者理解为 (const char)_xx 和 char_ (const xx) ）
    
2.  a 是一个指针变量，a 的值（指向）是可以改变的，但 a 只能指向（字符串）常量，指向的区域的内容不可改变；
    
3.  b 是一个指针常量，b 的值（指向）不能变；但 b 指向的目标（数组 b 在内存中的区域）的内容是可变的
    
4.  作为函数的声明的参数的时候，char [] 是被当做 char * 来处理的！两种形参声明写法完全等效！
    

字符串类型之间的转换： string、const char*、 char* 、char[] 相互转换
--------------------------------------------------

**一、转换表格**

<table><thead><tr><th>源格式 -&gt; 目标格式</th><th align="right">string</th><th align="right">char*</th><th align="right">const char*</th><th align="right">char[]</th></tr></thead><tbody><tr><td>string</td><td align="right">NULL</td><td align="right">直接赋值</td><td align="right">直接赋值</td><td align="right">直接赋值</td></tr><tr><td>char*</td><td align="right">strcpy</td><td align="right">NULL</td><td align="right">const_cast</td><td align="right">char*=char</td></tr><tr><td>const char*</td><td align="right">c_str()</td><td align="right">直接赋值</td><td align="right">NULL</td><td align="right">const char*=char;</td></tr><tr><td>char[]</td><td align="right">copy()</td><td align="right">strncpy_s()</td><td align="right">strncpy_s()</td><td align="right">NULL</td></tr></tbody></table>

**二、总结方法：**

1.  变成 string, 直接赋值。
2.  char[] 变成别的，直接赋值。
3.  char * 变 constchar * 容易，const char * 变 char * 麻烦。`<const_cast><char*>(constchar*);`
4.  string 变 char * 要通过 const char * 中转。
5.  变成 char[]。string 逐个赋值，char* const char* strncpy_s()。

**三，代码示例**

1、string 转为其他类型

①、string 转 const char*

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    std::string str = "HelloWorld!";     //初始化string类型，并具体赋值
    const char* constc = nullptr;         //初始化const char*类型，并赋值为空
    constc= str.c_str();                 //string类型转const char*类型
    printf_s("%s\n", str.c_str());        //打印string类型数据 .c_str()
    printf_s("%s\n", constc);             //打印const char*类型数据
    return 0;
}
```

②、string 转 char*

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    std::string str = "HelloWorld!";     //初始化string类型，并具体赋值
    char* c = nullptr;                    //初始化char*类型，并赋值为空
    const char* constc = nullptr;         //初始化const char*类型，并赋值为空
    constc= str.c_str();                 //string类型转const char*类型
    c= const_cast<char*>(constc);        //const char*类型转char*类型
    printf_s("%s\n", str.c_str());        //打印string类型数据 .c_str()
    printf_s("%s\n",c);                  //打印char*类型数据
    return 0;

}
```

③、string 转 char[]

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    std::string str = "HelloWorld!";      //初始化string类型，并具体赋值
    char arrc[20] = {0};                   //初始化char[]类型，并赋值为空
    for (int i = 0; i < str.length(); i++) //string类型转char[]类型
    {
        arrc[i]=str[i];
    }
    printf_s("%s\n", str.c_str());         //打印string类型数据 .c_str()
    printf_s("%s\n", arrc);                //打印char[]类型数据
    return 0;
}
```

2、const char * 转为其他类型

①const char * 转 string

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    const char* constc = "Hello World!";     //初始化const char* 类型，并具体赋值
    std::string str;                        //初始化string类型
    str= constc;                            //const char*类型转string类型
    printf_s("%s\n", constc);                //打印const char* 类型数据
    printf_s("%s\n", str.c_str());           //打印string类型数据
    return 0;
}
```

②const char * 转 char*

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    const char* constc = "Hello World!";     //初始化const char* 类型，并具体赋值
    char* c = nullptr;                       //初始化char*类型
    c= const_cast<char*>(constc);           //const char*类型转char*类型
    printf_s("%s\n", constc);                //打印const char* 类型数据
    printf_s("%s\n", c);                     //打印char*类型数据
    return 0;
}
```

③const char * 转 char[]

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    const char* constc = "Hello World!";     //初始化const char* 类型，并具体赋值
    char arrc[20] = { 0 };                   //初始化char[]类型，并赋值为空
    strncpy_s(arrc,constc,20);              //const char*类型转char[]类型
    printf_s("%s\n", constc);                //打印const char* 类型数据
    printf_s("%s\n", arrc);                  //打印char[]类型数据
    return 0;
}
```

3、char * 转为其他类型

①char * 转 string

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    char* c = "HelloWorld!";           //初始化char* 类型，并具体赋值
    std::string str;                   //初始化string类型
    str= c;                            //char*类型转string类型
    printf_s("%s\n", c);                //打印char* 类型数据
    printf_s("%s\n", str.c_str());      //打印string类型数据
    return 0;
}
```

②char * 转 const char*

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    char* c = "HelloWorld!";         //初始化char* 类型，并具体赋值
    const char* constc = nullptr;     //初始化const char* 类型，并具体赋值
    constc= c;                       //char*类型转const char* 类型
    printf_s("%s\n", c);              //打印char* 类型数据
    printf_s("%s\n", constc);         //打印const char* 类型数据
    return 0;
}
```

③char * 转 char[]

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    char* c = "HelloWorld!";         //初始化char* 类型，并具体赋值
    char arrc[20] = { 0 };           //初始化char[] 类型，并具体赋值
    strncpy_s(arrc,c,20);             //char*类型转char[] 类型
    printf_s("%s\n", c);              //打印char* 类型数据
    printf_s("%s\n", arrc);           //打印char[]类型数据
    return 0;
}
```

4、char[] 转为其他类型

```
#include "stdafx.h"
#include <iostream>
int _tmain(intargc, _TCHAR* argv[])
{
    char arrc[20] = "HelloWorld!";//初始化char[] 类型并具体赋值
    std::string str;                 //初始化string
    const char* constc = nullptr;   //初始化const char*
    char*c = nullptr;                //初始化char*
    str= arrc;                     //char[]类型转string类型
    constc= arrc;             //char[]类型转const char* 类型
    c= arrc;                        //char[]类型转char*类型
    printf_s("%s\n", arrc);         //打印char[]类型数据
    printf_s("%s\n", str.c_str());  //打印string类型数据
    printf_s("%s\n", constc);       //打印const char* 类型数据
    printf_s("%s\n", c);            //打印char*类型数据
    return 0;
}
```