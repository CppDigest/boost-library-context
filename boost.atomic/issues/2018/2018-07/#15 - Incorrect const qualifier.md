# #15 - Incorrect const qualifier? [Closed]

> Username: DavidCallahan  
> Created at: 2018-07-16 21:35:41 UTC  
> Updated at: 2020-04-08 21:18:39 UTC  
> Closed at: 2018-07-17 09:41:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/15  

Using a near-trunk build of clang which as a new semantic check, I get this error:  
  
```  
include/boost/atomic/detail/ops_gcc_x86_dcas.hpp:408:16: error: address argume\nt to atomic builtin cannot be const-qualified ('const volatile boost::atomics::detail::gcc_dcas_x86_64::storage_type *' (aka 'constvolatile unsigned __int128 *') invalid)  
        return __sync_val_compare_and_swap(&storage, value, value);  
               ^                           ~~~~~~~~  
1 error generated.  
```  
  
which is generated due to the "const" declaration in this static function:   
[https://github.com/boostorg/atomic/blob/develop/include/boost/atomic/detail/ops_gcc_x86_dcas.hpp#L402](https://github.com/boostorg/atomic/blob/develop/include/boost/atomic/detail/ops_gcc_x86_dcas.hpp#L402 )  
  
The error seems correct but so does the code. I worked around this by casting away the const operand to the intrinsic.  
```  
        return __sync_val_compare_and_swap(  
            const_cast<storage_type volatile*>(&storage), value, value);  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2018-07-17 09:07:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-405513623  

I can't reproduce this. What clang version do you use?

---

## Comment 2

> Username: Lastique  
> Created at: 2018-07-18 09:47:44 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-405875395  

On 07/18/18 00:30, David Callahan wrote:  
> Using current trunk (master).  
> Here is a test case  
>   
> |#define BOOST_ATOMIC_DETAIL_X86_HAS_CMPXCHG16B 1 #include   
> <boost/atomic.hpp> typedef boost::atomics::detail::gcc_dcas_x86_64<true>   
> DCAS; const volatile DCAS::storage_type data = 0; unsigned get() {   
> return DCAS::load(data, boost::memory_order::relaxed); } |  
  
The current develop doesn't fail the compilation with clang 6.0, but the   
test case is not valid. boost::atomic objects (and their internal   
storage) must reside in writable memory. load() may issue store   
operation on the storage memory even though the stored value doesn't change.

---

## Comment 3

> Username: fiesh  
> Created at: 2018-12-05 09:39:39 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-444421461  

With boost 1.67 and clang-7, I can reproduce the issue with this:  
  
```zsh  
echo '#include <boost/log/utility/setup.hpp>' > /tmp/a.cpp && clang++ -c -march=haswell /tmp/a.cpp  
```  
  
```  
In file included from /tmp/a.cpp:1:  
In file included from /usr/include/boost/log/utility/setup.hpp:20:  
In file included from /usr/include/boost/log/utility/setup/common_attributes.hpp:22:  
In file included from /usr/include/boost/log/attributes/counter.hpp:26:  
In file included from /usr/include/boost/atomic/atomic.hpp:20:  
In file included from /usr/include/boost/atomic/fences.hpp:21:  
In file included from /usr/include/boost/atomic/detail/operations.hpp:17:  
In file included from /usr/include/boost/atomic/detail/operations_lockfree.hpp:21:  
In file included from /usr/include/boost/atomic/detail/ops_gcc_atomic.hpp:24:  
/usr/include/boost/atomic/detail/ops_gcc_x86_dcas.hpp:408:16: error: address argument to atomic builtin cannot be const-qualified ('const volatile boost::atomics::detail::gcc_dcas_x86_64::storage_type *'  
      (aka 'const volatile unsigned __int128 *') invalid)  
        return __sync_val_compare_and_swap(&storage, value, value);  
               ^                           ~~~~~~~~  
1 error generated.  
```

---

## Comment 4

> Username: fiesh  
> Created at: 2018-12-18 14:23:51 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-448237316  

6e14ca2 indeed fixes the issue -- boost 1.69 works!  Thanks!

---

## Comment 5

> Username: Lastique  
> Created at: 2018-12-18 14:24:05 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-448237392  

On 12/18/18 3:18 PM, fiesh wrote:  
> @Lastique Any chance this can be re-opened?  
  
No, the problem has been fixed already.

---

## Comment 6

> Username: fiesh  
> Created at: 2018-12-19 10:42:07 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-448549939  

> On 12/18/18 3:18 PM, fiesh wrote: @Lastique Any chance this can be re-opened?  
> No, the problem has been fixed already.  
  
Yeah that's why I had removed the comment before your answer and replaced it with the other one.

---

## Comment 7

> Username: timgivois  
> Created at: 2019-08-15 19:34:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-521768403  

So basically, this is fixed in 1.69 which is incompatible with MacOS and that means you won't fix 1.67....

---

## Comment 8

> Username: bilalebi  
> Created at: 2020-04-08 21:18:38 UTC  
> Url: https://github.com/boostorg/atomic/issues/15#issuecomment-611200019  

I had the same issue but upgrading to boost 1.72 solved it.  
  
I'm on Mac Catalina
