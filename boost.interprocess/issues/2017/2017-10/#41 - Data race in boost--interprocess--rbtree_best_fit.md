# #41 - Data race in boost::interprocess::rbtree_best_fit [Closed]

> Username: ostash  
> Created at: 2017-10-26 10:23:02 UTC  
> Updated at: 2017-10-27 12:38:52 UTC  
> Closed at: 2017-10-26 20:22:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/41  

Ther is a race on bit-field (https://github.com/google/sanitizers/wiki/ThreadSanitizerPopularDataRaces#race-on-bit-field).  
  
If we take a look on ```boost::interprocess::rbtree_best_fit::SizeHolder```:  
  
```c++  
struct SizeHolder  
{  
   //!This block's memory size (including block_ctrl  
   //!header) in Alignment units  
   size_type m_prev_size :  sizeof(size_type)*CHAR_BIT;  
   size_type m_size      :  sizeof(size_type)*CHAR_BIT - 2;  
   size_type m_prev_allocated :  1;  
   size_type m_allocated :  1;  
};  
```  
non-synchronized access to ```m_prev_size``` and ```m_size``` is a race.  
From what I understand from reading source, it is allowed to write ```UsableByPreviousChunk``` bytes past allocated block, or to ```m_prev_size``` of next block. But during allocation reads and writes to ```m_size``` are performed.

---

## Comment 1

> Username: ostash  
> Created at: 2017-10-26 10:48:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/41#issuecomment-339627201  

Simplest fix I can think of is to remove ```m_prev_size``` from bit-field:  
  
```c++  
struct SizeHolder  
{  
   //!This block's memory size (including block_ctrl  
   //!header) in Alignment units  
   size_type m_prev_size;  
   size_type m_size      :  sizeof(size_type)*CHAR_BIT - 2;  
   size_type m_prev_allocated :  1;  
   size_type m_allocated :  1;  
};  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2017-10-26 20:22:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/41#issuecomment-339789592  

Many thanks for your detailed report and fix. I tried to find the issue but could not imagine the bitfiled would be the cause. I considered m_prev_size as a separate memory location, but maybe it's incorrect and it's also better expressed removing the bit field part. thanks again!

---

## Comment 3

> Username: ostash  
> Created at: 2017-10-27 12:38:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/41#issuecomment-339959816  

Thanks for confirming and closing this!
