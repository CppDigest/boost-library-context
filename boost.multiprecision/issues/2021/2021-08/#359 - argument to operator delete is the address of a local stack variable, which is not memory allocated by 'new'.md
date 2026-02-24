# #359 - [clang-tidy] argument to operator delete is the address of a local stack variable, which is not memory allocated by 'new' [Open]

> Username: matlo607  
> Created at: 2021-08-24 12:36:34 UTC  
> Updated at: 2021-10-08 10:04:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/359  

`clang-tidy` on a project brought this to my attention:  
```txt  
/usr/lib/gcc/x86_64-redhat-linux/7/../../../../include/c++/7/ext/new_allocator.h:125:2: error: Argument to operator delete is the address of a local stack variable, which is not memory allocated by 'new' [clang-analyzer-cplusplus.NewDelete,-warnings-as-errors]  
        ::operator delete(__p);  
        ^  
...  
my_private_include_path/include/boost/multiprecision/number.hpp:2005:25: note: Calling 'number::str'  
   std::string     s  = r.str(d, os.flags());  
                        ^  
my_private_include_path/include/boost/multiprecision/number.hpp:808:14: note: Calling 'cpp_int_backend::str'  
      return m_backend.str(digits, f);  
             ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1920:14: note: Calling 'cpp_int_backend::do_get_string'  
      return do_get_string(f, trivial_tag());  
             ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1822:11: note: Assuming the condition is false  
      if ((f & std::ios_base::oct) == std::ios_base::oct)  
          ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1822:7: note: Taking false branch  
      if ((f & std::ios_base::oct) == std::ios_base::oct)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1824:16: note: Assuming the condition is false  
      else if ((f & std::ios_base::hex) == std::ios_base::hex)  
               ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1824:12: note: Taking false branch  
      else if ((f & std::ios_base::hex) == std::ios_base::hex)  
           ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1830:11: note: 'base' is not equal to 8  
      if (base == 8 || base == 16)  
          ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1830:11: note: Left side of '||' is false  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1830:24: note: 'base' is not equal to 16  
      if (base == 8 || base == 16)  
                       ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1830:7: note: Taking false branch  
      if (base == 8 || base == 16)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1876:10: note: Taking false branch  
         if (t.sign())  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1881:14: note: Assuming the condition is false  
         if (this->size() == 1)  
             ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1881:10: note: Taking false branch  
         if (this->size() == 1)  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1889:13: note: Loop condition is false. Execution continues on line 364  
            while (eval_get_sign(t) != 0)  
            ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1904:10: note: Calling implicit destructor for 'cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<unsigned long long>>'  
         }  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1904:10: note: Calling '~cpp_int_base'  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:364:11: note: Assuming field 'm_internal' is false  
      if (!m_internal)  
          ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:364:7: note: Taking true branch  
      if (!m_internal)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:365:10: note: Calling 'new_allocator::deallocate'  
         allocator().deallocate(limbs(), capacity());  
         ^  
/usr/lib/gcc/x86_64-redhat-linux/7/../../../../include/c++/7/ext/new_allocator.h:125:2: note: Argument to operator delete is the address of a local stack variable, which is not memory allocated by 'new'  
        ::operator delete(__p);  
        ^  
```  
I am not sure but it seems that `clang-tidy` misinterprets the call to the custom allocator `scoped_shared_storage`.

---

## Comment 1

> Username: boblytton  
> Created at: 2021-10-08 07:28:25 UTC  
> Updated at: 2021-10-08 07:50:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/359#issuecomment-938410977  

I have the same issue: clang-analyzer-cplusplus.NewDelete & clang-analyzer-cplusplus.NewDeleteLeaks

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-08 10:04:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/359#issuecomment-938516510  

Someone is going to have to convince me that there is a code path that results in `m_internal == false` and the memory *not* allocated via the allocator, because I just don't see how that can happen right now.
