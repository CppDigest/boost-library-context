# #702 - Explore potential for optimization in array::insert(Iter, InputIt, InputIt) [Open]

> Username: grisumbras  
> Created at: 2022-05-15 11:22:01 UTC  
> Updated at: 2022-05-16 23:38:31 UTC  
> Url: https://github.com/boostorg/json/issues/702  

The idea is this:  
1. Fill the buffer up to capacity _at the end_ (needs a `revert_insert` protection).  
2. Create a temporary buffer with the remaining elements (can throw).  
3. If temporary buffer is not empty  
    1. Allocate new buffer with necessary size (can throw).  
    2. Relocate elements  
        * first original elements, up to insertion point,  
        * then new elements (at the end of the original buffer),  
        * then new elements from the temporary buffer,  
        * then remaining old elements.  
4. If the temporary buffer is empty, rotate elements in the original buffer instead.  
  
In the end result:  
1. If the original capacity could accommodate the input range, then no new buffer (even a temporary one) is allocated. This is particularly useful when the caller uses input iterators, but does know the input range size, and thus can call `reserve`.  
2. We should be able to keep the strong guarantee.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-15 14:24:44 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1126952358  

I don't know about this... the standard library doesn't do it

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-15 16:48:36 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1126977135  

This is not worth it

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-05-16 14:54:48 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1127778299  

Libc++ does this

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-05-16 15:08:04 UTC  
> Updated at: 2022-05-16 15:08:59 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1127793748  

Interesting, do you have a link? And is there a discussion about it that we could look at? I don't see any evidence that people are using this overload or even care about optimizing it if they are.

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-05-16 21:33:15 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1128159462  

https://github.com/llvm-mirror/libcxx/blob/master/include/vector#L1921  
  
I don't think this overload is super useful, but if we provide it, why not make it a bit better? One particular example when this overload might become useful is when (if) coroutine-based generators come into fashion, as it is my understanding that such generators are supposed to be input ranges.  
  
Currently the input iterators found in the standard library are `istream_iterator`s.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-05-16 23:37:09 UTC  
> Updated at: 2022-05-16 23:38:31 UTC  
> Url: https://github.com/boostorg/json/issues/702#issuecomment-1128241489  

I rather just leave this issue open, mark it as "optimization" and wait a while. If we see demand for it then we can consider it.  
  
> why not make it a bit better  
  
More code means more tests means bigger executable means more complexity, and we don't have any evidence that users will benefit.
