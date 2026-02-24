# #58 - Test stack_test is LittleEndian dependant [Open]

> Username: trex58  
> Created at: 2020-06-02 14:47:36 UTC  
> Updated at: 2020-06-03 15:59:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58  

Hi,  
Running the test **stack_test** on AIX, which is BigEndian, I see that it fails while running the test **fixed_size_stack_test_exhausted** .  
  
```  
../libs/lockfree/test/stack_test.cpp :   
     85 BOOST_AUTO_TEST_CASE( fixed_size_stack_test_exhausted )  
     86 {  
     87     boost::lockfree::stack<long, boost::lockfree::capacity<2> > stk;  
     88  
     89     stk.push(1);  
     90     stk.push(2);  
     ....  
```  
  
The first stk.push(1) seems OK, but not really, since:  
```  
../boost/lockfree/detail/freelist.hpp:552  
  
552                 tagged_index new_pool(next_index->get_index(), old_pool.get_next_tag());  
(gdb) whatis next_index  
 type = boost::lockfree::detail::tagged_index *  
  
AIX:  
  (gdb) p * next_index  
  $12 = {index = 0, tag = 1}  
  
Linux:  
  (gdb) p * next_index  
  $14 = {index = 0, tag = 65535}  
```  
  
Then, stk.push(2) does not put correct values in stk.  
  
Main differences between Fedora/x86_64 and AIX after line stk.push(2) are:  
 - padding is 60 on Fedora and 124 on AIX (compiled in 64bit too)  
 - stk.pool.pool_._M_i = {index = 2, tag = 2} On Fedora but : {index = 0, tag = 0} on AIX.  
  
On **Fedora/x86_64**:  
```  
stk = {static has_capacity = true, static capacity = 2, static fixed_sized = false, static node_based = false, static compile_time_sized = true,  
     tos = { static _S_min_alignment = 4, static _S_alignment = 4, _M_i = {index = 0, tag = 0}},  
     static padding_size = 60,  
 padding = ...........,  
 pool = {<boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<long, boost::lockfree::capacity<2> >::node, 2>>  
      = {data = {  
       elems = .......}},  
         pool_ = {static _S_min_alignment = 4, static _S_alignment = 4, _M_i = {index = 2, tag = 2}}}}  
```  
On **AIX**:  
```  
stk = {static has_capacity = true, static capacity = 2, static fixed_sized = false, static node_based = false, static compile_time_sized = true,  
      tos = { static _S_min_alignment = 4, static _S_alignment = 4, _M_i = {index = 0, tag = 0}},  
      static padding_size = 124,  
 padding = ........,  
 pool = {<boost::lockfree::detail::compiletime_sized_freelist_storage<boost::lockfree::stack<long, boost::lockfree::capacity<2> >::node, 2>>  
      = {data = { elems = ..........}},  
         pool_ = {static_S_min_alignment = 4, static _S_alignment = 4, _M_i = {index = 0, tag = 0}}}}  
```  
  
However, looking at details with gdb:  
**Fedora/x86_64**:  
```  
(gdb) p & stk.pool.pool_._M_i  
  $54 = (boost::lockfree::detail::tagged_index *) 0x7fffffffc1c0  
  (gdb) x/2x 0x7fffffffc1c0  
  0x7fffffffc1c0: 0x00020002      0x00000000  
```  
**AIX**:  
```  
(gdb)  p & stk.pool.pool_._M_i  
  $12 = (boost::lockfree::detail::tagged_index *) 0xfffffffffffdb08  
  (gdb) x/4x 0xfffffffffffdb08  
  0xfffffffffffdb08:      0x00000000      0x00000002      0x00020001      0x001baca8  
```  
So, it seems that the code does not write the 0x00020002 value at the right place on AIX.  
  
This is probably due to the following code:  
```  
boost/lockfree/detail/freelist.hpp :   
class  
BOOST_ALIGNMENT( 4 ) // workaround for bugs in MSVC  
tagged_index  
{  
public:  
   typedef boost::uint16_t tag_t;  
   typedef boost::uint16_t index_t;  
...  
protected:  
   index_t index;  
   tag_t   tag;  
};  
```  
since using 16bit has already shown within another library of Boost that it does not work fine in BigEndian.  
  
Note: On Fedora/PPC64LE (PowerPC LittleEndian), this works fine.

---

## Comment 1

> Username: timblechmann  
> Created at: 2020-06-03 04:11:22 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-637940785  

```  
AIX:  
  (gdb) p * next_index  
  $12 = {index = 0, tag = 1}  
  
Linux:  
  (gdb) p * next_index  
  $14 = {index = 0, tag = 65535}  
```  
  
this is certainly funny, the next tag is computed as:  
```  
    tag_t get_tag() const  
    {  
        return tag;  
    }  
  
    tag_t get_next_tag() const  
    {  
        tag_t next = (get_tag() + 1u) & (std::numeric_limits<tag_t>::max)();  
        return next;  
    }  
```  
  
iirc the bitmasking is mainly done to do avoid overflow integer overflow warnings that the compiler might issue.  
  
> since using 16bit has already shown within another library of Boost that it does not work fine in BigEndian.  
  
what exactly is the issue? the main point of using 16bit integers here is to be able to use 32bit atomic operations  
  
--  
  
unfortunately i have neither access nor experience with AIX or ppc, but i can try to help you going through this code. it would definitely be interesting to see what `get_next_tag` is doing

---

## Comment 2

> Username: trex58  
> Created at: 2020-06-03 08:04:43 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638033260  

Hi Tim,  
I've only spent some hours on this issue, which looks close to: https://github.com/boostorg/gil/issues/498 . However, I wasn't able too to find the exact place where the code is doing something nasty (in BE). In this gil:498 issue, I think that the memory was not completely zeroed, in addition.  
Back to lockfree:58, I've experienced different errors depending if I compile with -O2 or -O0 of with GCC v8.4 or GCC v9.3 . The different error messages i've got were:  
```  
../libs/lockfree/test/stack_test.cpp(93): fatal error: in "fixed_size_stack_test_exhausted": critical check out == 2 has failed [4296738800 != 2]  
  
../libs/lockfree/test/stack_test.cpp(91): fatal error: in "fixed_size_stack_test_exhausted": critical check !stk.push(3) has failed  
  
../libs/lockfree/test/stack_test.cpp(79): fatal error: in "fixed_size_stack_test": critical check out == 2 has failed [1 != 2]  
******** errors disabling the alternate stack:  
        #error:1  
        Not owner  
unknown location(0): fatal error: in "fixed_size_stack_test_exhausted": memory access violation at address: 0x000003e1: invalid permissions  
../libs/lockfree/test/stack_test.cpp(96): last checkpoint  
```  
  
After doing some changes in the code (comment previous tests, add a stk.push(3) line, expand code by g++ -E), the issue disappeared when using GCC v8.4 . So I moved to GCC 9.3 and the description above was generated while debugging with gdb code produced by GCC 9.3 .  
  
Maybe the issue is not in Boost but in GCC C++ code being used by Boost. I already saw this case within another Boost test failure, due to a different behavior on AIX when converting negative Floating Point values to unsigned integer (but this is not the case here).  
  
I suspect BE because using a 16bit integer does not use the same part of a 32bit integer in BE like in LE I think. However, since gdb correctly interprest binary data, one has to print and interpret hexa values, not easy.  
  
About get_next_tag, I think I've checked that it gives the same result on both Fedora/Intel and AIX.   
```  
552                 tagged_index new_pool(next_index->get_index(), old_pool.get_next_tag());  
```  
  
However, the issue probably is in a place where the code accesses to a half 32-bit integer value, taking the wrong part, due to the 2 ways (BE & LE) to internally put the bytes of an integer:  
"On big endian platforms, the value 1 is stored in binary and is represented here in hexadecimal notation. One byte is stored as 01, two bytes as 00 01, and four bytes as 00 00 00 01. On little endian platforms, the value 1 is stored in one byte as 01 (the same as big endian), in two bytes as 01 00, and in four bytes as 01 00 00 00."  
Thus getting the half of the same integer in BE or LE may give either 0 or 1 .  
But that does not explain why I saw 1 and 65535 (0xFFFF) for next_index.  
  
Now that I have a better idea about where some not-appropriate action is done, I'll re-analyze what is done, on Fedora/Intel and AIX in parallel.  
Any suggestion is welcome.

---

## Comment 3

> Username: timblechmann  
> Created at: 2020-06-03 08:40:41 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638051403  

> However, the issue probably is in a place where the code accesses to a half 32-bit integer value  
  
thing is: i don't think i'm doing this anywhere. there are `atomic<tagged_index>`, where `tagged_index` has two 16bit integers, so the atomic operations are operated on 32bit, but whenever we're accessing the 16bit integers, we're accessing them directly.  
  
---  
  
do i understand it correctly that gcc-8.4 produces good code? in that case it would be quite interesting to see how other compilers perform (do you have clang for aix or other gcc versions?)

---

## Comment 4

> Username: trex58  
> Created at: 2020-06-03 09:34:24 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638081912  

I'm using GCC only. Clang should be available in 2H2020 I think.  
About GCC 8.4.0, I already got the issue with Boost v1.69 :    
```  
Running 12 test cases...  
../libs/lockfree/test/stack_test.cpp(93): fatal error: in "fixed_size_stack_test_exhausted": critical check out == 2 has failed [1 != 2]  
```  
However, we then published some new releases of this 8.4.0 and - maybe - the issue is gone then ? I'm not sure. I rather think that the issue is random and triggers some different errors depending on... how is blowing the wind maybe...  
Yes. I see that it depends if "-g -gdwarf" is added (no error) or not (error) to the g++ line. I remember now that, since I wasn't able to debug without -g stuff, I then moved to GCC v9.3 which is able to show the issue with "-g -gdwarf".  
  
When compiling with GCC 8.4 and NO -g stuff, I have an error message and CPU is consumed in:  
```  
0x000000010013fd70  std::atomic<boost::lockfree::detail::tagged_index>::store(boost::lockfree::detail::tagged_index, std::memory_order)(??, ??, ??) + 0x3c  
0x0000000100142df4  bool boost::lockfree::stack<long, boost::lockfree::capacity<2ul> >::unsynchronized_pop<long>(long&)(??, ??) + 0xc0  
0x0000000100142cbc  boost::lockfree::stack<long, boost::lockfree::capacity<2ul> >::unsynchronized_pop(long&)(??, ??) + 0xc  
0x0000000100142c40  boost::lockfree::stack<long, boost::lockfree::capacity<2ul> >::~stack()(??) + 0x28  
```  
  
BTW, I have the following warning on AIX:  
```  
/opt/freeware/lib/gcc/powerpc-ibm-aix7.2.0.0/8/include/c++/atomic: In constructor 'constexpr std::atomic<_Tp>::atomic(_Tp) [with _Tp = boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::stack<long int>::node, std::allocator<boost::lockfree::stack<long int>::node> >::freelist_node>]':  
/opt/freeware/lib/gcc/powerpc-ibm-aix7.2.0.0/8/include/c++/atomic:200:17: note: the ABI of passing aggregates with 16-byte alignment has changed in GCC 5  
       constexpr atomic(_Tp __i) noexcept : _M_i(__i) { }  
                 ^  
```  
which does not appear on Fedora/Intel. However, this warning also appears on Fedora32/PPC64LE:  
```  
/usr/include/c++/9/atomic: In constructor constexpr std::atomic<_Tp>::atomic(_Tp) [with _Tp = boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::stack<long int>::node, std::allocator<boost::lockfree::stack<long int>::node> >::freelist_node>]:  
/usr/include/c++/9/atomic:204:17: note: the ABI of passing aggregates with 16-byte alignment has changed in GCC 5  
  204 |       constexpr atomic(_Tp __i) noexcept : _M_i(__i) { }  
      |                 ^  
```  
So, there maybe is something special here dealing with GCC and Power HW.

---

## Comment 5

> Username: trex58  
> Created at: 2020-06-03 10:09:32 UTC  
> Updated at: 2020-06-03 10:10:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638098539  

Look at this:  
```  
(gdb) s  
std::atomic<boost::lockfree::detail::tagged_index>::load (this=0x7fffffffc1c0, __m=std::memory_order_consume)  
   at /usr/include/c++/9/atomic:253  
253             _Tp* __ptr = reinterpret_cast<_Tp*>(__buf);  
(gdb) n  
254             __atomic_load(std::__addressof(_M_i), __ptr, int(__m));   
  
(gdb) p _M_i  
$7 = {index = 1, tag = 0}  
  
AIX:  
  (gdb) p &_M_i  
  $8 = (boost::lockfree::detail::tagged_index *) 0xfffffffffffdb08  
  (gdb) x/10x 0xfffffffffffdb08  
  0xfffffffffffdb08:      0x00010000  
  
Linux:  
  (gdb) p & _M_i  
  $9 = (boost::lockfree::detail::tagged_index *) 0x7fffffffc1c0  
  (gdb) x/2x 0x7fffffffc1c0  
  0x7fffffffc1c0: 0x00000001    0x00000000  
```  
Though _M_i contains the same pair {1,0} on AIX and Fedora/Intel, as seen by gdb, it is implemented differently inside the memory.  
  
It clearly shows that  boost::lockfree::detail::tagged_index  is implemented differently on AIX vs Fedora/Intel, or that   __atomic_load(std::__addressof(_M_i), __ptr, int(__m));   returns something different on AIX.  
  
My hypothesis is that Boost code has a fixed way to put and get some data here, but the structure is implemented differently in G++ code on AIX vs Fedora/Intel. So, Boost code run on AIX does not look at the right place at some moment.

---

## Comment 6

> Username: timblechmann  
> Created at: 2020-06-03 15:18:22 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638265875  

2 thoughts:  
* could you potentially compile with `BOOST_LOCKFREE_FORCE_BOOST_ATOMIC` defined? that will use boost.atomic instead of std::atomic  
* check the sizeof `tagged_index` to ensure that that the compiler won't do any packing  
* check the alignment hints

---

## Comment 7

> Username: trex58  
> Created at: 2020-06-03 15:59:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/58#issuecomment-638291115  

I've compiled on both AIX and Fedora/Intel with: `g++ -DBOOST_LOCKFREE_FORCE_BOOST_ATOMIC ...`  
Nothing special with or without.  
  
The size of tagged_index is the same on both AIX & Fedora/Intel :   
   `sizeof(boost::lockfree::detail::tagged_index): 4`  
  
Under gdb, I see the moment where something wrong happens, but still I can't say exactly what's wrong.  
Moreover, the issue changes according to the code being compiled (when I add some printf() ). Now, it fails with: `check out == 1 has failed [4294967297 != 1]` .  
  
And, sometimes, the issue moves to another place and I spend a lot of time with a case which fails after the place I'm looking at in details... nuts !  
  
It's very difficult to see where the issue is since it requires to look at what's in memory in hexa, and I do not master what is being done.  
  
I have added printf()s in order to get more easily details. However, I need to improve these traces, too raw for now. Tomorrow.  
  
How do you suggest to check the alignment hints?  
  
  
Look at the difference between AIX and Fedora/Intel:  
```  
AIX:  
  
stk.push(2)  
../boost/lockfree/detail/freelist.hpp:450  
456             return node;  
(gdb) p node  
$3 = (boost::lockfree::stack<long, boost::lockfree::capacity<2> >::node *) 0xfffffffffffdb00  
(gdb) x/4x 0xfffffffffffdb00  
0xfffffffffffdb00:      0x00020001      0x100309f0      0x00000000      0x00000002  
  
(gdb) n  
228                 tagged_node_handle new_tos (pool.get_handle(new_top_node), old_tos.get_tag());  
(gdb) p old_tos  
$4 = {index = 1, tag = 0}  
  
Linux:  
  
stk.push(2)  
../boost/lockfree/detail/freelist.hpp:450  
456             return node;  
(gdb) p node  
$56 = (boost::lockfree::stack<long, boost::lockfree::capacity<2> >::node *) 0x7fffffffc180  
(gdb) x/4x 0x7fffffffc180  
0x7fffffffc180: 0xffff0002      0x00007fff      0x00000002      0x00000000  
  
(gdb) n  
228                 tagged_node_handle new_tos (pool.get_handle(new_top_node), old_tos.get_tag());  
(gdb) p old_tos  
$60 = {index = 1, tag = 0}  
```
