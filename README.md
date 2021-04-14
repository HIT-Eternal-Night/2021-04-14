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
