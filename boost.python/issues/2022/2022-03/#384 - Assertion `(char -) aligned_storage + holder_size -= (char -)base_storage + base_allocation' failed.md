# #384 - Assertion `(char *) aligned_storage + holder_size <= (char *)base_storage + base_allocation' failed. [Open]

> Username: aristk  
> Created at: 2022-03-09 07:50:03 UTC  
> Updated at: 2024-06-03 15:46:14 UTC  
> Url: https://github.com/boostorg/python/issues/384  

Hi,  
  
Many thanks for the great library!  
  
We just migrated to boost 1.78 and start getting:  
```  
python3: libs/python/src/object/class.cpp:762: static void* boost::python::instance_holder::allocate(PyObject*, std::size_t, std::size_t, std::size_t): Assertion `(char *) aligned_storage + holder_size <= (char *)base_storage + base_allocation' failed.  
```  
on arm platform. On x64 everything works perfect.   
  
I took a look into boost::python::instance_holder::allocate with debugger and see:  
```  
sizeof(alignment_marker_t) == 4  
alignment == 4  
holder_size == 16  
```  
so assertion is correct.   
  
I'm a bit confused, why we need -1 at https://github.com/boostorg/python/blame/develop/src/object/class.cpp#L750?  
  
Looking forward for your answer!

---

## Comment 1

> Username: aristk  
> Created at: 2022-03-09 09:43:23 UTC  
> Url: https://github.com/boostorg/python/issues/384#issuecomment-1062735442  

OK, this -1 is there, since it is not expected to reach end of base_storage, as in this case no alignment is need.  
  
But then formula in https://github.com/boostorg/python/blame/develop/src/object/class.cpp#L759 should be corrected.  
`(x & (alignment - 1)) == 0` should be used as condition.

---

## Comment 2

> Username: Minimonium  
> Created at: 2022-04-05 13:38:36 UTC  
> Url: https://github.com/boostorg/python/issues/384#issuecomment-1088715978  

Can confirm that it fails on x86.

---

## Comment 3

> Username: Osyotr  
> Created at: 2022-07-19 15:55:29 UTC  
> Url: https://github.com/boostorg/python/issues/384#issuecomment-1189229489  

I can confirm that changing   
`const uintptr_t padding = alignment == 1 ? 0 : ( alignment - (x & (alignment - 1)) );`  
to  
`const uintptr_t padding = ((x & (alignment - 1)) == 0) ? 0 : ( alignment - (x & (alignment - 1)) );`  
fixes the issue.  
Thanks @aristk

---

## Comment 4

> Username: teeks99  
> Created at: 2024-06-03 15:46:13 UTC  
> Url: https://github.com/boostorg/python/issues/384#issuecomment-2145552158  

I can confirm that this impacts windows 32-bit builds, this seems like a pretty important issue.   
  
I was able to apply @Osyotr and @aristk 's fix from above and it then started working as expected.
