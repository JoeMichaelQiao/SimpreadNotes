> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/qq_40861244/article/details/96580820)

C 语言中字符数组的常用函数有以下几个：

strlen(str): 返回字符串 str 的长度；  
strcpy(str1,str2): 将字符串 str2 复制到字符串 str1 中，并返回字符串 str1；  
strchr(str,ch): 返回在字符串 str 中第一次出现指定的字符 ch 的地址；  
strcat(str1,str2): 将字符串 str2 连接到字符串 str1 的尾部，并返回连接后的字符串 str1；  
strcmp(str1,str2): 比较两个字符串 str1 和 str2，若 str1 大于（等于、小于）str2，则返回大于（等于、小于）0 的数；  
strstr(str1,str2): 在字符串 str1 中查找指定的字符串 str2 第一次出现的位置，并返回地址值；  
函数的实现：  
1.strlen(str)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    char str[N];
    printf("input a str:");
 
/*
    //字符串的输入 
    fgets(str,6,stdin);
    gets(str);
    for(i = 0;i < 5;i++)
       scanf("%s",str);
    i = strlen(str);        //使用strlen()；
*/
    while( str[i] != '\0')    //自定义实现
    {
        i++;
    }    
    printf("%d\n",i);
    
}
```

2.strcpy(str1,str2)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    int j = 0;
    char str1[N] = {0};
    char str2[N] = {0};
    printf("input a str1:");
    gets(str1);
    printf("input a str2:");
    gets(str2);
    
//   strcpy(str1,str2);        //strcpy();
    while(str2[i] != '\0')    //自定义实现strcpy();
    {
        str1[i] = str2[i];
        i++;
    }
    puts(str1);    
}
```

3.strchr(str,ch)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    char ch;
    char str[N];
    printf("input a str:");
    gets(str);
    printf("input a char:");
    ch =  getchar();
 
//    printf("%d\n",strchr(str,ch));    //打印地址
//    printf("%s\n",strchr(str,ch));    //打印ch及后面的字符
    while(str[i] != '\0')            //自定义实现strchr();
    {
        if(str[i] == ch)
            break;
        i++;
    }
    printf("%d\n",i);
}
```

4.strcat(str1,str2)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    int j = 0;
    char str1[N] = {0};
    char str2[N] = {0};
    printf("input a str1:");
    gets(str1);
    printf("input a str2:");
    gets(str2);
    
    strcat(str1,str2);        //strcat();
    
/*    while(str1[i] != '\0')    //自定义实现strcat();
        i++;
    while(str2[j] != '\0')
    {
        str1[i+j] = str2[j];
        j++;
    }
 */  
    puts(str1);
        
}
```

5.strcmp(str1,str2)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    int j = 0;
    char str1[N] = {0};
    char str2[N] = {0};
    printf("input a str1:");
    gets(str1);
    printf("input a str2:");
    gets(str2);
    
 //   j = strcmp(str1,str2);            //strcmp();
 //   printf("%d\n",j);
    
    while((str1[i] == str2[i]) && (str1[i] != '\0'))//自定义实现strcmp();打印不同字符的差值
            i++;
    printf("%d\n",str1[i] - str2[i]);
    
}
```

6.strstr(str1,str2)

```
#include <stdio.h>
#include <string.h>
#define N 100
 
void main()
{
    int i = 0;
    int j = 0;
    int k = 0;
    char str1[N] = {0};
    char str2[N] = {0};
    printf("input a str1:");
    gets(str1);
    printf("input a str2:");
    gets(str2);
 
    //  j =  strstr(str1,str2);    //strstr();
    //   printf("%d\n",j);
    while(str1[i] != '\0')      //自定义实现strstr();打印查找到的地址值，若没有查到，则返回0
    {
        if(str1[i] != str2[j])
            i++;
        if(str1[i] == str2[j])
        {
            i++;
            j++;
 
            if(str2[j] == '\0')
            {
                k = 0;
                break;
            }
            if(str1[i] != str2[j])
            {
                k = 1;
            }
        }
    }
 
    if(k == 1)
        printf("%d\n",0);
    else if(k == 0)
        printf("%d\n",i-j+1);
 
}
```

初学 C 语言，以上代码若有错误，请指正！谢谢！！！