# #449 - Heap overflow caused by incorrect alignment calculation in instance_holder::allocate() [Closed]

> Username: andrew-rogers-sndk  
> Created at: 2024-09-04 11:21:43 UTC  
> Updated at: 2024-10-02 10:21:42 UTC  
> Closed at: 2024-10-02 10:21:42 UTC  
> Url: https://github.com/boostorg/python/issues/449  

In the following code in [`libs\python\src\object\class.cpp`](https://github.com/boostorg/python/blob/8dd151177374dbf0aa5cb86bd350cf1ad13e2160/src/object/class.cpp#L750-L765):  
  
```c++  
typedef unsigned int alignment_marker_t;  
                                                       
void* instance_holder::allocate(PyObject* self_, std::size_t holder_offset, std::size_t holder_size, std::size_t alignment)  
{  
    assert(PyType_IsSubtype(Py_TYPE(Py_TYPE(self_)), &class_metatype_object));  
    objects::instance<>* self = (objects::instance<>*)self_;  
      
    int total_size_needed = holder_offset + holder_size + alignment - 1;  
      
    if (-Py_SIZE(self) >= total_size_needed)  
    {  
        // holder_offset should at least point into the variable-sized part  
        assert(holder_offset >= offsetof(objects::instance<>,storage));  
  
        size_t allocated = holder_size + alignment;  
        void* storage = (char*)self + holder_offset;  
        void* aligned_storage = ::boost::alignment::align(alignment, holder_size, storage, allocated);  
  
        // Record the fact that the storage is occupied, noting where it starts  
        const size_t offset = reinterpret_cast<uintptr_t>(aligned_storage) - reinterpret_cast<uintptr_t>(storage) + holder_offset;  
        Py_SET_SIZE(self, offset);  
        return (char*)self + offset;  
    }  
    else  
    {  
        const size_t base_allocation = sizeof(alignment_marker_t) + holder_size + alignment - 1;  
        void* const base_storage = PyMem_Malloc(base_allocation);  
       if (base_storage == 0)  
            throw std::bad_alloc();  
  
        const uintptr_t x = reinterpret_cast<uintptr_t>(base_storage) + sizeof(alignment_marker_t);  
        //this has problems for x -> max(void *)  
        //const size_t padding = alignment - ((x + sizeof(alignment_marker_t)) % alignment);  
        //only works for alignments with alignments of powers of 2, but no edge conditions  
        const uintptr_t padding = alignment == 1 ? 0 : ( alignment - (x & (alignment - 1)) );  
        const size_t aligned_offset = sizeof(alignment_marker_t) + padding;  
        void* const aligned_storage = (char *)base_storage + aligned_offset;  
        BOOST_ASSERT((char *) aligned_storage + holder_size <= (char *)base_storage + base_allocation);  
        alignment_marker_t* const marker_storage = reinterpret_cast<alignment_marker_t *>((char *)aligned_storage - sizeof(alignment_marker_t));  
        *marker_storage = static_cast<alignment_marker_t>(padding);  
        return aligned_storage;  
    }  
}  
  
```  
The `else` path allocates `(sizeof(alignment_marker_t) + holder_size + alignment – 1)` bytes from an underlying allocator (`PyMem_Malloc()`, and in turn the heap). This memory is to be used to return to the caller a block of `holder_size` bytes, suitably aligned.  
  
After allocating this many bytes (in memory at `base_storage`), the code then forms an aligned pointer into that allocated memory block at `aligned_offset` and returns this memory to the caller (as `aligned_storage`). The problem is that there are fewer than `holder_size` bytes available after `aligned_storage` in the underlying block (at `base_storage`), as `(aligned_offset > (sizeof(alignment_marker_t) + alignment – 1))` (i.e. the original allocation size, minus the size the caller expects to be able to use).  
  
If we look at this code running in a debugger on Windows (32-bit, with Python 3.8), we can see why this is happening:  
  
```  
0:000:x86> kn8  
 # ChildEBP RetAddr        
00 011e7074 0c23528d     boost_python38_vc143_mt_gd_x32_1_80!boost::python::instance_holder::allocate+0x147 [C:\boost_1_80_0\libs\python\src\object\class.cpp @ 763]   
01 011e718c 0c1e4821     <caller_dll>!boost::python::objects::make_holder<0>::apply<boost::python::objects::value_holder<caller_type_name>,boost::mpl::vector0<boost::mpl::na> >::execute+0x5d [C:\<other-path>\Boost\boost\python\object\make_holder.hpp @ 92]   
02 011e7268 0c1f3091     <caller_dll>!boost::python::detail::invoke<int,void (__cdecl*)(_object *),boost::python::arg_from_python<_object *> >+0x41 [C:\<other-path>\Boost\boost\python\detail\invoke.hpp @ 79]   
03 011e73bc 0c1f2cd9     <caller_dll>!boost::python::detail::caller_arity<1>::impl<void (__cdecl*)(_object *),boost::python::default_call_policies,boost::mpl::vector2<void,_object *> >::operator()+0xd1 [C:\<other-path>\Boost\boost\python\detail\caller.hpp @ 233]   
04 011e74a8 50029a99     <caller_dll>!boost::python::objects::caller_py_function_impl<boost::python::detail::caller<void (__cdecl*)(_object *),boost::python::default_call_policies,boost::mpl::vector2<void,_object *> > >::operator()+0x49 [C:\<other-path>\Boost\boost\python\object\py_function.hpp @ 39]   
05 011e74c0 50025ad1     boost_python38_vc143_mt_gd_x32_1_80!boost::python::objects::py_function::operator()+0x29 [C:\boost_1_80_0\boost\python\object\py_function.hpp @ 148]   
06 011e755c 50026bb0     boost_python38_vc143_mt_gd_x32_1_80!boost::python::objects::function::call+0x491 [C:\boost_1_80_0\libs\python\src\object\function.cpp @ 221]   
07 011e7570 500271e4     boost_python38_vc143_mt_gd_x32_1_80!boost::python::objects::`anonymous namespace'::bind_return::operator()+0x20 [C:\boost_1_80_0\libs\python\src\object\function.cpp @ 581]   
  
0:000:x86> dv  
     base_storage = 0x2d89c1c0  
  base_allocation = 0x857  
                x = 0x2d89c1c4  
   aligned_offset = 8  
   marker_storage = 0xcccccccc  
          padding = 4  
  aligned_storage = 0x2d89c1c8  
            self_ = 0x1997bbb8  
    holder_offset = 0x18   
      holder_size = 0x850  
        alignment = 4  
             self = 0x1997bbb8  
total_size_needed = 0n2155  
  
```  
Noting that `alignment_marker_t` is a typedef for `unsigned int`, and so `sizeof(alignment_marker_t)` is `4`, then `x = base_storage + sizeof(alignment_marker_t) = 2d89c1c4`, and so with `alignment = 4`, we then have:  
  
```  
padding = alignment == 1 ? 0 : ( alignment - (x & (alignment - 1)) )  
padding = ( alignment - (x & (alignment - 1)) )  
padding = ( 4 - (2d89c1c4 & 3) )  
padding = ( 4 - 0 )  
padding = 4  
  
```  
and so `(padding > (alignment – 1))` and the pointer returned will cause the caller to overrun the size of the buffer which has been allocated by one byte!  
  
The solution would be to write the code as the following:  
  
```c++  
padding = (x & (alignment - 1)) ? ( alignment - (x & (alignment - 1)) ) : 0  
```

---

## Comment 1

> Username: andrew-rogers-sndk  
> Created at: 2024-10-02 10:21:42 UTC  
> Url: https://github.com/boostorg/python/issues/449#issuecomment-2388296312  

Resolved by merging #392.
