# 2021-04-14
可变数组

我们现在自己写一个可以自动增长大小的数组程序：
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

下面做点简单指针练习吧！
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
