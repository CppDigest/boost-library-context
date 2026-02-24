# #358 - [clang-tidy] Potential memory leak? [Open]

> Username: matlo607  
> Created at: 2021-08-24 11:52:36 UTC  
> Updated at: 2021-08-24 16:56:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/358  

`clang-tidy` on a project brought this to my attention:  
```txt  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:375:17: error: Potential memory leak [clang-analyzer-cplusplus.NewDeleteLeaks,-warnings-as-errors]  
         m_sign = o.m_sign;  
                ^  
...  
pravete_source.cpp:534:25: note: Calling copy assignment operator for 'number<boost::multiprecision::backends::cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<unsigned long long>>, boost::multiprecision::et_on>'  
my_private_include_path/include/boost/multiprecision/number.hpp:243:7: note: Calling copy assignment operator for 'cpp_int_backend<0, 0, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<unsigned long long>>'  
      m_backend = e.m_backend;  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:1346:7: note: Calling 'cpp_int_base::assign'  
      this->assign(o);  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:369:7: note: Taking true branch  
      if (this != &o)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:373:10: note: Calling 'cpp_int_base::resize'  
         resize(o.size(), o.size());  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:287:85: note: '?' condition is true  
      static const unsigned max_limbs = MaxBits / (CHAR_BIT * sizeof(limb_type)) + ((MaxBits % (CHAR_BIT * sizeof(limb_type))) ? 1 : 0);  
                                                                                    ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:289:11: note: Assuming 'new_size' is <= 'max_limbs'  
      if (new_size > max_limbs)  
          ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:289:7: note: Taking false branch  
      if (new_size > max_limbs)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:294:11: note: Assuming 'new_size' is > 'cap'  
      if (new_size > cap)  
          ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:294:7: note: Taking true branch  
      if (new_size > cap)  
      ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:298:28: note: Calling 'new_allocator::allocate'  
         limb_pointer pl = allocator().allocate(cap);  
                           ^  
/usr/lib/gcc/x86_64-redhat-linux/7/../../../../include/c++/7/ext/new_allocator.h:101:2: note: Taking false branch  
        if (__n > this->max_size())  
        ^  
/usr/lib/gcc/x86_64-redhat-linux/7/../../../../include/c++/7/ext/new_allocator.h:111:27: note: Memory is allocated  
        return static_cast<_Tp*>(::operator new(__n * sizeof(_Tp)));  
                                 ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:298:28: note: Returned allocated memory  
         limb_pointer pl = allocator().allocate(cap);  
                           ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:300:15: note: Field 'm_internal' is false  
         if (!m_internal)  
              ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:300:10: note: Taking true branch  
         if (!m_internal)  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:373:10: note: Returned allocated memory  
         resize(o.size(), o.size());  
         ^  
my_private_include_path/include/boost/multiprecision/cpp_int.hpp:375:17: note: Potential memory leak  
         m_sign = o.m_sign;  
                ^  
```  
Is this a false positive ?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-24 16:56:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/358#issuecomment-904814645  

To be honest I don't understand the message: `m_sign = o.m_sign;` is just assigning a bool.
