# 哈希表查找

## By C++

```c
#include <stdio.h>
#define MaxSize 100		/*哈希表最大长度*/

typedef int KeyType;

typedef struct
{
	KeyType key;	/*关键字值*/
	int si;			/*探查次数*/
} HashTable;

void CreateHT(HashTable ht[],KeyType a[],int n,int m,int p)	/*构造哈希表*/
{
	int i,d,cnt;
	for (i=0;i<m;i++)  /*置初值*/
	{
		ht[i].key=0;
		ht[i].si=0;
	}
	for (i=0;i<n;i++)
	{
		cnt=1;			/*累计探查次数*/
		d=a[i]%p;
		if (ht[d].key==0)
		{
			ht[d].key=a[i];
			ht[d].si=cnt;
		}
		else
		{
			do			/*处理冲突*/
			{
				d=(d+1)%m;
                cnt++;
			} while (ht[d].key!=0);
			ht[d].key=a[i];
			ht[d].si=cnt;
		}
	}
}

void DispHT(HashTable ht[],int n,int m)	/*输出哈希表*/
{
	int i;
	double avg;
	printf("i:  ");
	for (i=0;i<m;i++) 
		printf("%-3d",i);
	printf("\n");
	printf("key:");
	for (i=0;i<m;i++) 
		printf("%-3d",ht[i].key);
	printf("\n");
	printf("si: ");
	for (i=0;i<m;i++) 
		printf("%-3d",ht[i].si);
	printf("\n");
	avg=0;
	for (i=0;i<m;i++) 
		avg+=ht[i].si;
	avg=avg/n;
	printf("平均查找长度:ASL(%d)=%g\n",n,avg);
}

void main()
{
	HashTable ht[MaxSize];
	KeyType a[]={19,1,23,14,55,20,84,27,68,11,10,77};
	int n=12,m=19,p=13;
	CreateHT(ht,a,n,m,p);
	DispHT(ht,n,m);
}
```

## By Golang

```go
// Package hashtable creates a ValueHashtable data structure for the Item type
package hashtable

import (
	"fmt"
	"sync"
)

// Key the key of the dictionary
type Key interface{}

// Value the content of the dictionary
type Value interface{}

// ValueHashtable the set of Items
type ValueHashtable struct {
	items map[int]Value
	lock  sync.RWMutex
}

// the hash() private function uses the famous Horner's method
// to generate a hash of a string with O(n) complexity
func hash(k Key) int {
	key := fmt.Sprintf("%s", k)
	h := 0
	for i := 0; i < len(key); i++ {
		h = 31*h + int(key[i])
	}
	return h
}

// Put item with value v and key k into the hashtable
func (ht *ValueHashtable) Put(k Key, v Value) {
	ht.lock.Lock()
	defer ht.lock.Unlock()
	i := hash(k)
	if ht.items == nil {
		ht.items = make(map[int]Value)
	}
	ht.items[i] = v
}

// Remove item with key k from hashtable
func (ht *ValueHashtable) Remove(k Key) {
	ht.lock.Lock()
	defer ht.lock.Unlock()
	i := hash(k)
	delete(ht.items, i)
}

// Get item with key k from the hashtable
func (ht *ValueHashtable) Get(k Key) Value {
	ht.lock.RLock()
	defer ht.lock.RUnlock()
	i := hash(k)
	return ht.items[i]
}

// Size returns the number of the hashtable elements
func (ht *ValueHashtable) Size() int {
	ht.lock.RLock()
	defer ht.lock.RUnlock()
	return len(ht.items)
}
```
