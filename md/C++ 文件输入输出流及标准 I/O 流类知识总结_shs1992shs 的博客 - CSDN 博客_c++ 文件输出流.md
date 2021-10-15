> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/shs1992shs/article/details/83043522?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link)

在学习使用 C++ 进行 STL 模型文件的读写与显示时，遇到了一些文件读取的问题，在网上找了很多资料，有些写的比较模糊，自己也遇到了很多坑，在参考以前大神的资料基础上，对 C++ 文件流及标准 I/O 流相关知识进行了一些总结。  
转载请注明出处：  
[https://blog.csdn.net/shs1992shs/article/details/83043522](https://blog.csdn.net/shs1992shs/article/details/83043522)

C++ 所有的输入输出流 (I/O) 都是基于 stream 流类，包括 iostream，fstream，stringstream 等，其对象称为流对象，其之间的相互关系如下：

  
![](https://img-blog.csdn.net/20181011172649821?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoczE5OTJzaHM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

首先介绍 stream 流中的两个重要的运算符 "<<“和”>>"  
1、**插入器 (<<)**  
　　向流输出数据。比如说系统有一个默认的标准输出流 (cout)，cout<<“Write cout”<<’\n ‘，由于箭头方向向左，就是把字符串 "Write cout" 和换行字符(’\n’) 输出到标准输出流，而标准输出流一般情况下就是指的显示器。

2、**析取器 (>>)**  
　　从流中输入数据。比如说系统有一个默认的标准输入流 (cin)，对于 cin>>n，由于>> 箭头方向向右，所以意思就是将 cin 中的数据输入 n 变量，所以 cin>>n 就把屏幕上的数据写入 n 变量。也就是说箭头的方向相当于流的方向，也就是数据的流向，对于其他流对象 (xxxstream) 也是一样的道理。

C++ 通过以下几个类支持文件的输入输出：  
以下几个流类都在头文件 fstream 需要 #include <fstream>  
ofstream: 写操作（输出）的文件类 (由 ostream 引申而来) ，将文件从内存写入存储设备（本地磁盘）  
ifstream: 读操作（输入）的文件类 (由 istream 引申而来) ，将文件从存储设备加载到内存中  
fstream: 可同时读写操作的文件类 (由 iostream 引申而来)

一、打开文件
------

无论是以哪种方式操作文件，都是要先创建文件流类的一个对象 (这些类的一个实例) ，然后将这个对象与文件联系起来，也就是说打开一个文件，被打开的文件在程序中由一个流对象(stream object) 来表示 ，而对这个流对象所做的任何输入输出操作实际就是对该文件所做的操作。  
使用 fstream 类的成员函数 open() 实现文件的打开，其函数原型为：  
void open (const char * filename, openmode mode);  
void open (const char * filename, openmode mode，int access);

filename 是一个字符串，代表要打开的**文件名**；mode 是打开文件的**方式**；access 打开文件的**属性** (基本上很少用到，使用第一种函数原型就可以)。

**mode 打开文件的方式**是在 ios 类中定义的，常用的值如下：

<table><thead><tr><th align="center">ios::in</th><th align="center">为输入 (读) 而打开文件</th></tr></thead><tbody><tr><td align="center">ios::out</td><td align="center">为输出 (写) 而打开文件</td></tr><tr><td align="center">ios::binary</td><td align="center">二进制方式打开，用于二进制文件</td></tr><tr><td align="center">ios::ate</td><td align="center">初始位置：文件尾</td></tr><tr><td align="center">ios::app</td><td align="center">所有输出附加在文件末尾</td></tr><tr><td align="center">ios::trunc</td><td align="center">如果文件已存在则先删除该文件</td></tr><tr><td align="center">ios::nocreate</td><td align="center">不建立文件，所以文件不存在时打开失败</td></tr><tr><td align="center">ios::ios::noreplace</td><td align="center">不覆盖文件，所以打开文件时如果文件存在失败</td></tr></tbody></table>

以上的标识符可以被组合使用，中间以”或”操作符 (|) 间隔，如 ios::out|ios::binary。

**打开文件的属性取值**是：

<table><thead><tr><th align="center">0</th><th align="center">普通文件，打开访问</th></tr></thead><tbody><tr><td align="center">1</td><td align="center">只读文件</td></tr><tr><td align="center">2</td><td align="center">隐含文件</td></tr><tr><td align="center">4</td><td align="center">系统文件</td></tr></tbody></table>

同样可以用”或”操作符 (|) 或者 “+” 将以上属性连接起来，如 1|2 就是以只读和隐含属性打开文件。

打开文件的形式就如下：

```
fstream file;
file.open("Hello.txt",ios::in|ios::out|ios::binary, 0);
//上行代码等价于file.open("Hello.txt",ios::in|ios::out|ios::binary);

```

如果 open 函数只有一个文件名参数（xxstream.open(“filepath”) 的函数形式），则以默认的方式打开文件，几个类的默认方式为：

fstream： ios::in | ios::out  
ifstream： ios::in  
ofstream：ios::out | ios::trunc// 打开文件做读操作，删除文件中已有的数据

需要注意的是，只有当函数被调用时没有声明方式参数的情况下，默认值才会被采用；如果函数被调用时声明了任何参数，默认值将被完全改写，而不会与调用参数组合。可以根据实际应用，选择不同的类来实例一个对象。

由于对类 fstream，ofstream，ifstream 的对象所进行的第一个操作通常都是打开文件，这些类都有一个构造函数可以直接调用 open 函数，并拥有同样的参数。这样，我们就可以通过以下方式进行与上面同样的定义对象和打开文件的操作：

```
fstream file("Hello.txt", ios::out | ios::app | ios::binary); 
// 等价于
fstream file ;
file.open("Hello.txt",ios::out | ios::app | ios::binary) ;

```

可以通过调用成员函数 is_open() 来检查一个文件是否已经被顺利的打开了：  
bool is_open() ; 它返回一个布尔 (bool) 值，为真（true）代表文件已经被顺利打开，假 ( false ) 则相反。

```
fstream file("Hello.txt", ios::out | ios::app | ios::binary); 
if(file.is_open())
{
  ...........
  return ture;
}

```

也可以直接对对象进行判断:

```
 fstream file("Hello.txt", ios::out | ios::app | ios::binary); 
 if(!file)//!操作符已被重载
     return false;

```

**二、关闭文件**
----------

当文件读写操作完成之后，我们必须将文件关闭以使文件重新变为可访问的。关闭文件需要调用成员函数 close()，它负责将**缓存中的数据排放出来**并关闭文件，其函数原型为：void close();  
这个函数一旦被调用，原先的流对象 (stream object) 就可以被用来打开其它的文件了，这个文件也就可以重新被其它的进程 (process) 所有访问了。  
为防止流对象被销毁时还联系着打开的文件，析构函数 (destructor) 将会自动调用关闭函数 close()。

```
file.close();

```

三、读写文件
------

这里读写文件分为对文本文件和二进制文件的读取，对于文本文件的读取比较简单，用插入器 (<<) 和析取器 (>>) 就可以了；而对于二进制的读取就要复杂些，下要就详细的介绍这两种方式

**1、文本文件 (Text mode files)**

一般来说，我们将使用这些类与同控制台 (console) 交互同样的成员函数 (cin 和 cout) 来进行输入输出。如下面的例题所示，我们使用重载的插入操作符<<：

```
// 向文件中写入数据(向文件输出)
#include <fstream>
using namespace std;

int main()
{
    ofstream out("example.txt");
    if (!out) //!运算符已经重载
    {
        return false;
    ｝
    else{
        out << "This is a line.\n";
        out << "This is another line.\n";
        out.close();
    }
    return 0;
}

```

文件里现在的内容是：  
This is a line.  
This is another line.

这种方式还有一种简单的格式化能力，比如可以指定输出为 16 进制等等，具体的格式有以下一些：

<table><thead><tr><th align="center">操纵符</th><th align="center">功能</th><th align="center">输入 / 输出</th></tr></thead><tbody><tr><td align="center">dec</td><td align="center">格式化为十进制数值数据</td><td align="center">输入和输出</td></tr><tr><td align="center">endl</td><td align="center">输出一个换行符并刷新此流</td><td align="center">输出</td></tr><tr><td align="center">ends</td><td align="center">输出一个空字符</td><td align="center">输出</td></tr><tr><td align="center">hex</td><td align="center">格式化为十六进制数值数据</td><td align="center">输入和输出</td></tr><tr><td align="center">oct</td><td align="center">格式化为八进制数值数据</td><td align="center">输入和输出</td></tr><tr><td align="center">setpxecision(int p)</td><td align="center">设置浮点数的精度位数</td><td align="center">输出</td></tr></tbody></table>

比如要把 123 当作十六进制输出：file1<<hex<<123;  
要把 3.1415926 以 5 位精度输出：file1<<setpxecision(5)<<3.1415926。

从文件中读入数据也可以用与 cin 的使用同样的方法：

```
//从文件中读取数据(从文件输入)
#include <iostream>
#include <fstream>
#include <cstdlib>
using namespace std;
int main ()
{
    char buffer[256];
    ifstream in("example.txt");
    if (!in.is_open())
    {
        cout << "Error opening file"; exit (1);
    }
    while (!in.eof())
    {
        in.getline(buffer,100);//getline()函数的作用是逐行读取，这里就是读取流in中的数据
        cout<<buffer<< endl;
    }
    return 0;
}

```

此时将从文件中逐行读取文件内容，直至文件的末尾，然后将其在屏幕上显示出来。我们使用了一个新的成员函数叫做 eof() ，它是 ifstream 从类 ios 中继承过来的，当到达文件末尾时返回 true 。

**状态标志符的验证 (Verification of state flags)**

除了 eof() 以外，还有一些验证流的状态的成员函数（所有都返回 bool 型返回值 ），其调用形式为：

```
   xxstream s; 
   s.bad();

```

*   bad()  
    如果在读写过程中出错，返回 true 。例如：当我们要对一个不是打开为写状态的文件进行写入时，或者我们要写入的设备没有剩余空间的时候。
*   fail()  
    除了与 bad() 同样的情况下会返回 true 以外，加上格式错误时也返回 true ，例如当想要读入一个整数，而获得了一个字母的时候。
*   eof()  
    如果读文件到达文件末尾，返回 true。
*   good()  
    这是最通用的：如果调用以上任何一个函数返回 true 的话，此函数返回 false 。  
    要想重置以上成员函数所检查的状态标志，你可以使用成员函数 clear()，没有参数。
*   rdstate()  
    返回流的当前状态标志字
*   clear(strm::iostate flag)  
    将流的状态设置为 flag，clear() 函数无参数时，是清除错误信息，并将重置所有状态标志。

**I/O 对象在任意时候都对应一种状态**：比如有效状态（还未处理或者正确处理完毕时的状态），比如失败状态（处理失败时），比如数据流被破坏（文件错误）等等。流的状态主要是由状态标记位表示，状态标记位有几个常量：goodbit、eofbit、failbit、badbit，分别对应不同的流状态，这几个标志位常量分别由以上的几种流状态验证函数**置位**。

strm::iostate // 机器相关的整型名，由各个 iostream 类定义，用于定义条件状态  
strm::goodbit // strm::iostate 类型的值，用于指出正常的流  
strm::badbit // strm::iostate 类型的值，用于指出被破坏的流  
strm::failbit // strm::iostate 类型的值，用于指出失败的 IO 操作  
strm::eofbit // strm::iostate 类型的值，用于指出流已经到达文件结束符

<table><thead><tr><th align="center">状态标记位常量</th><th align="center">含义</th><th align="center">good()</th><th align="center">eof()</th><th align="center">fail()</th><th align="center">bad()</th></tr></thead><tbody><tr><td align="center">ios::goodbit</td><td align="center">没有错误</td><td align="center">1</td><td align="center">0</td><td align="center">0</td><td align="center">0</td></tr><tr><td align="center">ios::eofbit</td><td align="center">已到达文件尾</td><td align="center">0</td><td align="center">1</td><td align="center">0</td><td align="center">0</td></tr><tr><td align="center">ios::failbit</td><td align="center">I/O 流出现非致命错误 (读数字遇到字母)，流可继续使用，可挽回</td><td align="center">0</td><td align="center">0</td><td align="center">1</td><td align="center">0</td></tr><tr><td align="center">ios::badbit</td><td align="center">I/O 流发生了 (或许是物理上) 致命性错误，流不可继续使用，不可挽回</td><td align="center">0</td><td align="center">0</td><td align="center">1</td><td align="center">1</td></tr></tbody></table>

如果对应的标记位被置位，则返回 1；当到达文件的结束位置时，eofbit 和 failbit 都会被置位；当 badbit 被置位时，fail() 也会返回 1，所以使用 good() 和 fail() 是确定流能否使用的正确方法，流当做条件使用的代码就等价于 (!xx.fail())，而且 eof() 和 bad() 操作只能表示特定的错误。

为提高程序的可靠性，应在程序中检测 I/O 流的操作是否正常。当检测到流操作出现错误时，可以通过异常处理来解决问题。

```
#include <iostream>
#include <sstream>//stringstream流的头文件
#include <fstream>
using namespace std;
int main()
{
        string str;
        ifstream in;
        // 保证ifstream对象可以抛出异常
        /*failbit和badbit均是用来检测流的状态(flags)是否正常
        failbit是逻辑错误，badbit是读写错误，其返回的是true或false*/
        in.exceptions(ifstream::failbit | ifstream::badbit);
        try
        {
            in.open("Hello.txt");
            //读取文件的缓冲内容到数据流中
            stringstream strStream;
            /*缓冲区类streambuf,供输入输出流对象使用，每个标准C++输入输出流对象
            都包含一个指向streambuf的指针，通过调用成员函数rdbuf()获取指针,
            直接访问底层streambuf对象，进行数据的直接读写，跳过上层的格式化输入输出操作,
            对于文件流、字符串流类，都可以使用*/
            strStream << in.rdbuf();
            //关闭文件
            in.close();
            str = strStream.str();
        }
        catch (ifstream::failure e)
        {
          cout << "ERROR::SHADER::File not succesfully read" << endl;
        }
        return 0;
}

```

**获得和设置流指针 (get and put stream pointers)**

所有输入 / 输出流对象 (i/o streams objects) 都有至少一个流指针：  
ifstream， 类似 istream, 有一个被称为 get pointer 的指针，指向下一个将被读取的元素。ofstream, 类似 ostream, 有一个指针 put pointer ，指向写入下一个元素的位置。fstream, 类似 iostream，同时继承了 get 和 put 。我们可以通过使用以下成员函数来读出或配置这些指向流中读写位置的流指针：

*   tellg() 和 tellp()  
    这两个成员函数不用传入参数，返回 pos_type 类型的值 (根据 ANSI-C++ 标准) ，就是一个整数 (一般设为 long int)，代表当前 get 流指针的位置 (用 tellg) 或 put 流指针的位置 (用 tellp).
*   seekg() 和 seekp()  
    这对函数分别用来改变流指针 get 和 put 的位置。两个函数都被重载为两种不同的原型：  
    seekg (pos_type position);  
    seekp (pos_type position);  
    使用这个原型，流指针被改变为指向从文件开始计算的一个绝对位置。要求传入的参数类型与函数 tellg 和 tellp 的返回值类型相同。  
    seekg (off_type offset, seekdir direction);  
    seekp (off_type offset, seekdir direction);  
    使用这个原型可以指定由参数 direction 决定的一个具体的指针开始计算的一个位移 (offset)。它可以是：

<table><thead><tr><th align="center">ios::beg</th><th align="center">从流开始位置计算的位移</th></tr></thead><tbody><tr><td align="center">ios::cur</td><td align="center">从流指针当前位置开始计算的位移</td></tr><tr><td align="center">ios::end</td><td align="center">从流末尾处开始计算的位移</td></tr></tbody></table>

流指针 get 和 put 的值对文本文件 (text file) 和二进制文件 (binary file) 的计算方法都是不同的，因为文本模式的文件中某些特殊字符可能被修改。由于这个原因，建议对以文本文件模式打开的文件总是使用 seekg 和 seekp 的第一种原型，而且不要对 tellg 或 tellp 的返回值进行修改。对二进制文件，你可以任意使用这些函数，应该不会有任何意外的行为产生。  
以下例子使用这些函数来获得一个二进制文件的大小：

```
#include <iostream>   
#include <fstream>       
const char * filename = "test.txt";       
int main () 
{           
           long l,m;
           ifstream in(filename, ios::in|ios::binary);
           l = in.tellg(); 
           in.seekg (0, ios::end);
           m = in.tellg();        
           in.close();        
           cout << "size of " << filename;       
           cout << " is " << (m-l) << " bytes.\n";
           return 0;    
 }     
  //结果:   size of example.txt is 40 bytes

```

**2、二进制文件 (Binary files)**

在二进制文件中，使用插入器 <<和析取器>>，以及函数（如 getline）来操作符输入和输出数据，没有什么实际意义，虽然它们是符合语法的，向二进制文件输入输出数据有几种函数形式：

**一、put() 函数**  
put() 函数向流写入一个字符，其原型是 ofstream &put(char ch)，使用也比较简单，如 file1.put(‘c’); 就是向流写一个字符’c’。

**二、 get() 函数**  
get() 函数比较灵活，有 3 种常用的重载形式：  
1、一种就是和 put() 对应的形式：ifstream &get(char &ch); 功能是从流中读取一个字符，结果保存在引用 ch 中，如果到文件尾，返回空字符。如 file2.get(x); 表示从文件中读取一个字符，并把读取的字符保存在 x 中。  
2、另一种重载形式的原型是： int get(); 这种形式是从流中返回一个字符，如果到达文件尾，返回 EOF，如 x=file2.get(); 和第一种形式的功能是一样的。  
3、还有一种形式的原型是：ifstream &get(char *buf,int num,char delim=‘n’)；这种形式把字符读入由 buf 指向的数组，直到读入了 num 个字符或遇到了由 delim 指定的字符，如果没使用 delim 这个参数，将使用缺省值换行符’\n’。例如：file2.get(str1,127,‘A’); 从文件中读取字符到字符串 str1，当遇到字符’A’或读取了 127 个字符时终止。

**三、文件流包括两个为顺序读写数据特殊设计的成员函数**：**write** 和 **read**。第一个函数 (write) 是 ostream 的一个成员函数，都是被 ofstream 所继承。而 read 是 istream 的一个成员函数，被 ifstream 所继承。类 fstream 的对象同时拥有这两个函数。它们的原型是：  
write (char * buffer, streamsize size);  
read (char * buffer, streamsize size);  
这里 buffer 是一块内存的地址，用来存储或读出数据。参数 size 是一个整数值，表示要从缓存（buffer）中读出或写入的字符数 (**size 值很重要**，因为二进制文件内容**没有**行的概念 (’\n’)，字节之间是**紧挨着的**)。

```
//读取二进制文件
#include <iostream>
#include <fstream>
using namespace std;
int main ()
{
    const char* filename = "example.txt";
    char* buffer;
    long size;
    ifstream file(filename, ios::in|ios::binary|ios::ate);//读取到文件尾
    size = file.tellg();//确定文件的大小
    file.seekg(0, ios::beg);//重新将文件流指针置于文件开始的位置
    buffer = new char [size];
    file.read(buffer, size);
    file.close();
    cout <<"the complete file is in a buffer";
    delete[] buffer;
    return 0;
}
//The complete file is in a buffer

```

```
//向二进制文件写入数据
#include <iostream>
#include <fstream>
using namespace std;
int main ()
{
    unsigned char str1[]="I Love You";
    int n[5];
    ifstream in("xxx.xxx");
    ofstream out("yyy.yyy");
    out.write(str1,strlen(str1));//把字符串str1全部写到yyy.yyy中
    in.read((unsigned char*)n,sizeof(n));//从xxx.xxx中读取指定个整数，注意类型转换
    in.close();
    out.close();
}

```

四、缓存和同步 (Buffers and Synchronization)
-------------------------------------

当我们对文件流进行操作的时候，它们与一个 streambuf 类型的缓存 (buffer) 联系在一起。这个缓存（buffer）实际是一块内存空间，**作为流 (stream) 和物理文件的媒介** (也称为**缓冲区**)。例如，对于一个输出流， 每次成员函数 put (写一个单个字符) 被调用，这个字符不是直接被写入该输出流所对应的物理文件中的，而是首先被插入到该流的缓存（buffer）中。

缓冲分为：**全缓冲**、**行缓冲**和**不带缓冲**。  
　1、**全缓冲**  
　　在这种情况下，当填满标准 I/O 缓存后才进行实际 I/O 操作。全缓冲的典型代表是对磁盘文件的读写。  
　2、**行缓冲**  
　　在这种情况下，当在输入和输出中遇到换行符时，执行真正的 I/O 操作。这时，我们输入的字符先存放在缓冲区，等按下回车键换行时才进行实际的 I/O 操作，典型代表是键盘输入数据。  
　3、**不带缓冲**  
　　也就是不进行缓冲，标准出错情况 stderr 是典型代表，这使得出错信息可以直接尽快地显示出来。  
　　  
当缓存被排放出来 (flush) 时，它里面的所有数据或者被写入物理媒质中（如果是一个输出流的话），或者简单的被抹掉(如果是一个输入流的话)。这个过程称为同步(synchronization)，它会在以下任一情况下发生：

*   当文件被关闭时： 在文件被关闭之前，所有还没有被完全写出或读取的缓存都将被同步。
*   当缓存 buffer 满时：缓存 Buffers 有一定的空间限制，当缓存满时，它会被自动同步。
*   控制符明确指明：当遇到流中某些特定的控制符时，同步会发生， 这些控制符包括：flush 和 endl。cout<<flush；(cout<<endl ; 等价于 cout<<”\n” <<flush ; ）
*   明确调用函数 sync(): 调用成员函数 sync() (无参数) 可以引发立即同步。这个函数返回一个 int 值，等于 - 1 表示流没有联系的缓存或操作失败。

在**状态标志符的验证**一节中的示例代码中出现了 rdbuf()函数，其就是指向文件流对象的 steambuf 缓冲的指针，可以通过插入器 (<<) 直接将数据输入一个 stringstream 流对象中(重载操作符<<，以 streambuf 指针为参数)，直接进行底层的数据读写，跳过了上层的格式化输入输出，提高了读写效率。字符串流 stringstream 也同样可以使用。

```
string str;
ifstream in("Hello.txt",ios::in);
stringstream strstrm;
strstrm<<in.rdbuf();
cout<<in.rdbuf()；//直接输出到屏幕显示
in.close();
str=strstrm.str();

```

引用及参考博文：  
[https://blog.csdn.net/kingstar158/article/details/6859379?utm_source=copy](https://blog.csdn.net/kingstar158/article/details/6859379?utm_source=copy)  
[http://www.cnblogs.com/kingcat/archive/2012/05/09/2491847.html](http://www.cnblogs.com/kingcat/archive/2012/05/09/2491847.html)  
[https://blog.csdn.net/zkl99999/article/details/45692547](https://blog.csdn.net/zkl99999/article/details/45692547)