# #52 - queue misaligned access fails UBSAN [Closed]

> Username: jefftrull  
> Created at: 2019-08-01 14:31:07 UTC  
> Updated at: 2022-04-20 15:48:29 UTC  
> Closed at: 2022-04-20 15:48:28 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52  

Simple usage of the queue triggers gcc's UBSAN checker flagging misaligned 64b accesses. Test case:  
  
```  
#include <iostream>  
#include <boost/lockfree/queue.hpp>  
  
int main()  
{  
    boost::lockfree::queue<std::size_t> q(5);  
  
    q.push(1);  
    q.push(2);  
    q.push(3);  
    q.push(4);  
    q.push(5);  
  
    std::size_t top;  
    q.pop(top);  
    std::cout << "top of queue was " << top << "\n";  
  
    if (q.empty())  
    {  
        std::cout << "the queue is now empty (!?)\n";  
    }  
  
    q.consume_all(  
        [](std::size_t v)  
        {  
            std::cout << "next value " << v << "\n";  
        });  
}  
  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2019-08-01 14:32:17 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-517313469  

And the results with compiled and run with `-fsanitize=undefined`:  
```  
/home/jet/boost_1_70_0/boost/lockfree/detail/freelist.hpp:102:13: runtime error: constructor call on misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:119:9: runtime error: reference binding to misaligned address 0x5612fcd39fb0 for type 'struct <unknown>', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:120:19: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:120:19: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:123:43: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:125:13: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 00 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:311:50: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  00 00 00 00 00 00 01 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:318:37: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  00 00 00 00 00 00 01 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:414:52: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 02 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
top of queue was 1  
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:394:49: runtime error: member access within misaligned address 0x5612fcd39fb0 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39fb0: note: pointer points here  
 00 00 00 00  60 9f d3 fc 12 56 02 00  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^   
next value 2  
next value 3  
next value 4  
next value 5  
/home/jet/boost_1_70_0/boost/lockfree/queue.hpp:455:49: runtime error: member access within misaligned address 0x5612fcd39e70 for type 'struct node', which requires 64 byte alignment  
0x5612fcd39e70: note: pointer points here  
 00 00 00 00  00 00 00 00 00 00 01 00  05 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 0  
```

---

## Comment 2

> Username: jefftrull  
> Created at: 2019-08-06 18:07:56 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-518781385  

Perhaps this is related to #11 , as well as [TRAC 11968](https://svn.boost.org/trac10/ticket/11968)

---

## Comment 3

> Username: jefftrull  
> Created at: 2021-02-10 04:09:23 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-776425439  

This issue is still present - any thoughts? Seems like the nodes have to be 64b aligned, and are not.

---

## Comment 4

> Username: timblechmann  
> Created at: 2021-03-25 07:22:43 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-806424834  

i'd tend to close this issue as wontfix: while i can reproduce the ubsan error with c++14, c++17 doesn't flag this issue. probably because the run-time library finally takes alignment attributes into account. if people run into this issue, i'd kindly ask them to migrate to c++17

---

## Comment 5

> Username: jefftrull  
> Created at: 2021-03-25 14:10:17 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-806823098  

Do you mean the UBSAN run-time library? Is it an error in the sanitizer?

---

## Comment 6

> Username: timblechmann  
> Created at: 2021-03-25 14:40:30 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-806871927  

> Do you mean the UBSAN run-time library? Is it an error in the sanitizer?  
  
it's not an error in the sanitizer. the issue is that heap-allocating aligned data structures was not properly working before c++17, as there was no way to `aligned_alloc` or alignment aware `operator new`.  
so you need to compile the application code (boost.lockfree) with c++17

---

## Comment 7

> Username: jefftrull  
> Created at: 2021-03-28 05:20:46 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-808847408  

Should Lockfree flag an error if compiled under 14 or earlier, then? It sounds like it requires 17...

---

## Comment 8

> Username: timblechmann  
> Created at: 2021-03-28 05:38:29 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-808850845  

it doesn't exactly "require" c++17: the alignment specifications were mainly used as "hints". the hints came mainly from dcas, which really depends on the implementation of std::atomics. it's mainly that ubsan enforses these "hints" as "requirements".

---

## Comment 9

> Username: jefftrull  
> Created at: 2021-03-29 04:11:02 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-809051891  

I'm still struggling with this I guess - can you elaborate on your last comment?  
  
My takeaway so far is that a simple use of lockfree invokes UB for C++14 and before...

---

## Comment 10

> Username: timblechmann  
> Created at: 2021-03-29 04:14:02 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-809052682  

> Or is there a compiler bug?  
  
it's a language bug.  
  
> If it is, does C++17 give some new tools to avoid it that lockfree takes advantage of?  
  
`new an_overaligned_class` is not properly aligned before c++17, because `new` does not know anything about the alignment: it simply returns memory which has the same alignment constraints as `malloc`.   
  
> My takeaway so far is that a simple use of lockfree invokes UB for C++14 and before...  
  
depends on your definition of "undefined behaviour": we have some alignment specifiers which are *hints* and not *requirements*. ubsan flags the violations of these "hints" as "undefined behaviour".

---

## Comment 11

> Username: jefftrull  
> Created at: 2021-03-29 05:09:14 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-809069351  

Is it the use of BOOST_ALIGNMENT at https://github.com/boostorg/lockfree/blob/f568ba9319c284a0fc8559f4784c1a59aa11a742/include/boost/lockfree/queue.hpp#L114 that is the "hint", not requirement?

---

## Comment 12

> Username: jefftrull  
> Created at: 2021-03-29 05:41:23 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-809083337  

If so let me check my understanding:  
  
1. `struct node` specifies cache line alignment with `alignas`  
2. UBSAN inserts matching checks for all `node` pointers  
3. `new node()` produces some implementation-defined alignment prior to C++17, and an alignment matching the specified type's after  
  
And as a result, UBSAN flags errors for versions prior to 17

---

## Comment 13

> Username: jefftrull  
> Created at: 2021-03-29 13:52:58 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-809394970  

It looks like replacing `std::allocator<T>` with `boost::aligned::aligned_allocator<T>` [here](https://github.com/boostorg/lockfree/blob/f568ba9319c284a0fc8559f4784c1a59aa11a742/include/boost/lockfree/detail/parameter.hpp#L56) removes the UB.

---

## Comment 14

> Username: jefftrull  
> Created at: 2022-04-20 15:48:28 UTC  
> Url: https://github.com/boostorg/lockfree/issues/52#issuecomment-1104090204  

I just noticed this from 2021 - it looks like the issue got fixed.  
  
https://github.com/boostorg/lockfree/commit/9144954d247024db8b21da0bca3fa7f20edd4fcc  
  
I notice some other alignment issues in the tracker, might be worth seeing if it closes them too.
