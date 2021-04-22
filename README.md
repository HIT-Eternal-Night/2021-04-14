# 2021-04-14
可变数组、指针SSE、数组和const指针的关系

一、我们现在自己写一个可以自动增长大小的数组程序：
头文件：array.h
#ifndef _ARRAY_H_
#define _ARRAY_H_

typedef struct {
	int *array;
	int size;
} Array;

Array array_create(int init_size);
void array_free(Array *a);
int array_size(const Array *a);
int *array_at(Array *a,int index);
void array_inflate(Array *a,int more_size);

#endif

.c文件：
#include "array.h"
#include <stdio.h>
#include <stdlib.h>

const BLOCK_SIZE = 20;

Array array_create(int init_size)
{
	Array a;
	a.size = init_size;
	a.array = (int*)malloc(sizeof(int)*a.size);
	return a;
}

void array_free(Array *a)
{
	free(a->array);
	a->array = NULL;
	a->size = 0; 
}

//封装 
int array_size(const Array *a)
{
	return a->size;
}

int *array_at(Array *a,int index)
{
	if ( index >= a->size )
	{
		array_inflate(a,(index/BLOCK_SIZE+1)*BLOCK_SIZE-a->size );
	}
	return &(a->array[index]);
}

void array_inflate(Array *a,int more_size)
{
	int *p = (int*)malloc(sizeof(int)*(a->size + more_size));
	int i;
	for (i=0 ; i<a->size ; i++)//可以使用memcpy拷贝数组 
	{
		p[i] = a->array[i];
	}
	free(a->array);
	a->array = p;
	a->size += more_size;
}

int main(int argc,char const *argv[])
{
	Array a = array_create(100);
	printf("%d\n",array_size(&a));
	*array_at(&a,0) = 10;
	printf("%d\n",*array_at(&a,0));
	int cnt = 0;
	int number = 0;
	while (number != -1)
	{
		scanf("%d",&number);
		if (number != -1)
			*array_at(&a,cnt++) = number;
	}
	array_free(&a);
	
	return 0;
}

注：如何用memcpy拷贝数组？
#include <string.h>
memcpy(p,a->array,a->size);

二、下面做点简单指针练习吧！
1、给定如下的数组：
  float litres[] = { 11.5, 11.21, 12.7, 12.6, 12.4 } ;
  float miles[] = { 471.5, 358.72, 495.3, 453.6, 421.6 } ;
  int mpl[5] ;  // Miles per litre. 
写一个程序计算并显示mpl中每个元素的值。使用指针而不是下标访问数组元素。

**输出格式要求："%d\t"

自己做的：
#include<stdio.h>

int main(int argc,char const *argv[])
{
	float litres[] = { 11.5, 11.21, 12.7, 12.6, 12.4 } ;
	float miles[] = { 471.5, 358.72, 495.3, 453.6, 421.6 } ;
  	int mpl[5] ;  /* Miles per litre. */
    
	int *p1 = mpl;
    float *p2 = miles;
    float *p3 = litres;
    
	int i;
    for (i=0 ; i<5 ; i++)
    {
    	*p1 = (int)(*p2 / *p3);
    	printf("%d\t",*p1);
    	p1++;
    	p2++;
    	p3++;
	}
	
	return 0;
}
标准答案：
#include <stdio.h>
 
int main()
{             
    float litres[] = {11.5, 11.21, 12.7, 12.6, 12.4};
    float miles[] = {471.5, 358.72, 495.3, 453.6, 421.6};
    int mpl[5];  // Miles per litre.
    int *p;
    float *pl, *pm;
 
    pl = litres;
    pm = miles;
    for (p = mpl; p < mpl + (sizeof(mpl) / sizeof(int)); p++)
    {             
        *p = (int)(*pm / *pl);
        printf("%d\t", *p);
        pm++;
        pl++;
    }
 
    return 0;
}

2、写一个程序读入你的姓名，然后每个字母间加一个空格后输出。例如，姓名John显示为J o h n。

**输入格式要求："%s"  提示信息："请输入你的姓名："

自己做的：
#include<stdio.h>
#include<string.h>

int main(int argc,char const *argv[])
{
	char name[100];
    char *p = name;
 
    printf("请输入你的姓名：");
    scanf("%s", name);
 
    for (p=name ; p<name+strlen(name) ; p++)
    {
    	if (p<name+strlen(name)-1 )
    		printf("%c ",*p);
    	else
    		printf("%c",*p);
	}
 
    return 0;
}

参考答案：
#include <stdio.h>
 
int main()
{           
    char name[100];
    char *p = name;
 
    printf("请输入你的姓名：");
    scanf("%s", name);
 
    while (*p != '\0')
    {           
        putchar(*p);
        putchar(' ');
        p++;
    }
 
    return 0;
}

三、数组和const指针的关系
#include<stdio.h>

int main(int argc,char const *argv[])
{
	int a[10] = {0,1,2,3,4,5,6,7,8,9,};
    int* const p = a;

    //测试1：（由于影响编译，因此将其注释掉，与其他测试并非在同一次中进行）
    //能否用一个数组变量来初始化一个数组变量？
    //能否用一个const指针来初始化一个const指针？
/*
    int b[10] = a;  //编译无法通过 error：invalid initializer
            //非法的初始化，因此不可以用一个数组变量来初始化一个数组变量
    int* const q = p;             //编译通过，这也是通常的用法
    printf("q=%p,p=%p\n", q, p);  //运行结果 q=0062FDF0,p=0062FDF0
            //可见q和p是一样的，因此可以用一个const指针来初始化一个const指针
*/
    //测试2：（由于影响编译，因此将其注释掉，与其他测试并非在同一次中进行）
    //能否用一个数组变量来初始化一个const指针？
    //能否用一个const指针来初始化一个数组变量？
/*
    int* const q = a;           //编译通过，这也是通常的用法
    printf("q=%p,a=%p\n", q, a);  //运行结果 q=0062FDF0,a=0062FDF0
            //可见q和a是一样的，因此可以用一个数组变量来初始化一个const指针
    int b[10] = p;  //编译无法通过 error：invalid initializer
            //非法的初始化，因此不可以用一个const指针来初始化一个数组变量
*/
    //以下的测试为同一次编译运行
    //测试3：
    //能否对一个数组变量进行sizeof()运算，结果是什么？
    //能否对一个const指针进行sizeof()运算，结果是什么？

    printf("sizeof(a)=%d\n", sizeof(a));    //编译通过，运行结果 sizeof(a)=40
    printf("sizeof(p)=%d\n", sizeof(p));    //编译通过，运行结果 sizeof(p)=8 
            //sizeof(a)得到的是整个数组的大小4*sizeof(int)
            //sizeof(p)得到的是一个指针的大小

    //测试4：
    //数组变量的值是什么？
    //const指针的值是什么？
    //能否对一个数组变量进行“&”运算，结果是什么？
    //能否对一个const指针进行“&”运算，结果是什么？

    printf("a=%p\n", a);            //编译通过，运行结果 a=0062FDF0
    printf("p=%p\n", p);            //编译通过，运行结果 p=0062FDF0
    printf("&a=%p\n", &a);          //编译通过，运行结果 &a=0062FDF0
    printf("&a[0]=%p\n", &a[0]);    //编译通过，运行结果 &a[0]=0062FDF0
    printf("&p=%p\n", &p);          //编译通过，运行结果 &p=0062FDE8
            //对数组变量和const指针都能进行“&”运算
            //a,&a,&a[0]的值相同，说明a的值是一个地址，所以可以把a赋给p
            //a与&a相同，都是数组a的第一个元素a[0]的地址
            //p的值就是程序开始时初始化得到的a的值
            //&p取到的是p这个constant量自己在内存中的地址，和普通变量一样
            //a的地址竟然是它自己，这个确实很有趣，可以理解，但有点说不通?
/*补充:
	        a和&a的值都是是0062FDF0
			a+1的值是0062FDF4
			&a+1的值是0062FE18
			可见，a+1的值比a多了一个int，&a+1的值比&a多了40个字节，也就是整个a[10]数组的大小，
			可见，a指的是一个数组元素的地址，就相当于&a[0]，而&a是指整个数组的地址，
			所以当他们分别加1以后，出现的结果是a+1往后移动了一个元素，而&a+1往后移动了一整个数组。很神奇啊！
*/
    //测试5：
    //能否对一个数组变量进行“*”运算？
    //能否对一个const指针进行“*”运算？

    printf("*a=%d\n", *a);    //编译通过，运行结果 *a=0
    printf("*p=%d\n", *p);    //编译通过，运行结果 *p=0
            //对数组变量和const指针都能进行“*”运算
            //意义也相同

    //测试6：
    //能否对一个数组变量进行“[n]”运算？
    //能否对一个const指针进行“[n]”运算？

    printf("a[6]=%d\n", a[6]);    //编译通过，运行结果 a[6]=6
    printf("p[6]=%d\n", p[6]);    //编译通过，运行结果 p[6]=6
            //对数组变量和const指针都能进行“[n]”运算
            //两者运算规律相同，
            //偏移量n实际偏移量的都与指向的类型（如本测试中的int）相关

    //测试7：
    //数组变量+1是什么？
    //const指针+1是什么？

    printf("a+1=%p\n", a+1);        //编译通过，运行结果 a+1=0062FDF4
    printf("p+1=%p\n", p+1);        //编译通过，运行结果 p+1=0062FDF4
    printf("*(a+1)=%d\n", *(a+1));  //编译通过，运行结果 *(a+1)=1
    printf("*(p+1)=%d\n", *(p+1));  //编译通过，运行结果 *(p+1)=1
    printf("a[1]=%d\n", a[1]);      //编译通过，运行结果 a[1]=1
    printf("p[1]=%d\n", p[1]);      //编译通过，运行结果 p[1]=1
            //第一次写前两句时，用了%d，结果编译warning，说类型是int*
            //所以改为%p,说明a+1和p+1仍为指针
            //与测试4的结果相比较，a+1和a，p+1和p之间都相差sizeof(int)
            //两者运算规律相同，其意义也与[]运算符相同

    return 0;
}
