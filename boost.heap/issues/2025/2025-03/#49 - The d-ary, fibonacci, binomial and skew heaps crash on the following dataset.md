# #49 - The d-ary, fibonacci, binomial and skew heaps crash on the following dataset [Open]

> Username: idler66  
> Created at: 2025-03-10 14:26:33 UTC  
> Updated at: 2025-03-12 08:27:20 UTC  
> Url: https://github.com/boostorg/heap/issues/49  

在下面数据集上，第一个字段为节点id，第二个字段为键值或 dlt>0, 第三个字段为操作类型，  
     1 为插入，2优先值增加，3优先值降低，0删除最小元素。    
In the following data set, the first field is the node id, the second field is the priority or dlt>0, and the third field is the operation type, 1 is insert, 2 is priority increase, 3 is priority decrease, and 0 is delete the smallest element.  
  
3,3,1,   //插入节点3，优先值为3； insert node 3, priority value is 3;  
8,8,1,   //插入节点8，优先值为8     insert node 8, priority value is 8  
8,9,2,   //节点8，优先值增加9，操作后为17      node 8, priority value increases by 9, after operation is 17  
3,2,3,  //节点3，优先值降低2，操作后为1      node 3, priority value decreases by 2, after operation is 1  
8,3,3,  
3,2,3,  
4,6,1,  
3,1,3,   
3,5,0,  //节点3，优先值目前堆中最小，删除该最小元素，这里5没有意义，是生成数据时随机应数 node 3, priority value is currently the smallest in the heap, delete the smallest element, here 5 is meaningless, it is a random number   
6,4,1,  
6,10,2,  
6,9,3,  
8,5,2,  
6,7,2,  
6,3,3,  
4,10,3,  
6,7,2,  
8,2,3,  
4,3,0,  
6,4,2,  
8,8,0,   //这里出现问题。正常应该删除的最小元素是8，优先级为17。但是上述几个堆删除的是节点6，优先级为20。There is a problem here. Normally, the smallest element that should be deleted is 8, with a priority of 17. However, the above heaps delete node 6, with a priority of 20.  
6,2,2,   //这里出现崩溃！ There is a crash here!  
6,7,2,  
6,2,3,  
6,8,0,  
  
pairing 堆在更大的数据集上存在同样的问题！The pairing stack has the same problem on larger datasets!

---

## Comment 1

> Username: idler66  
> Created at: 2025-03-10 22:50:00 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2712020539  

I generate lots of datasets for https://github.com/idler66/AOHeap.   
AOHeap is optimal even in the presence of the above errors.

---

## Comment 2

> Username: idler66  
> Created at: 2025-03-10 23:32:13 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2712077900  

I uploaded several datasets at https://github.com/idler66/heap-benchmarks/tree/main.

---

## Comment 3

> Username: timblechmann  
> Created at: 2025-03-11 00:19:55 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2712140188  

do you have a reduced testcase for it? unfortunately i don't have much time these days to spend on boost.heap

---

## Comment 4

> Username: idler66  
> Created at: 2025-03-11 00:59:41 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2712186748  

> do you have a reduced testcase for it? unfortunately i don't have much time these days to spend on boost.heap  
  
One situation I found is:   
1. Increase the priority of the element with the minimum priority to make it non-minimum;  
2. Delete the minimum element and an error occurs.   
  
I'll provide a Pull-request later.

---

## Comment 5

> Username: idler66  
> Created at: 2025-03-12 06:38:26 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2716698706  

d-ary fix:   
   void increase(size_type index)  
    {  
      siftdown(index);//siftup(index);  
    }  
    void decrease(size_type index)  
    {  
      siftup(index);//siftdown(index);  
    }

---

## Comment 6

> Username: idler66  
> Created at: 2025-03-12 08:27:19 UTC  
> Url: https://github.com/boostorg/heap/issues/49#issuecomment-2717033623  

Pairing heap fix:   
void decrease(handle_type handle, const_reference v)  
    {  
        BOOST_ASSERT(value_compare()(handle.iterator->first, v)); //BOOST_ASSERT(!value_compare()(handle.iterator->first, v))  
        handle.iterator->first = v;  
        decrease(handle);  
    }  
void increase(handle_type handle, const_reference v)  
    {  
        BOOST_ASSERT(value_compare()(v, handle.iterator->first));//BOOST_ASSERT(!value_compare()(v, handle.iterator->first));  
        handle.iterator->first = v;  
        increase(handle);  
    }
