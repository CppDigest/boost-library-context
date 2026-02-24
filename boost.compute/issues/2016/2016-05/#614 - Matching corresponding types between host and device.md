# #614 - Matching corresponding types between host and device [Open]

> Username: jszuppe  
> Created at: 2016-05-21 12:48:11 UTC  
> Updated at: 2017-04-24 16:41:24 UTC  
> Url: https://github.com/boostorg/compute/issues/614  

All over the documentation and in the tests we use fundamental C++ types like `int`, `short`, `long`. That may cause problems when their sizes do not match sizes of OpenCL types with the same names.   
  
In particular, I'm talking about `long`. It's common that on 32-bit OSs `sizeof(long) == 4` but for OpenCL `long` type `sizeof(long) == 8'. Example of code that will fail on 32-bit systems:  
  
``` cpp  
boost::compute::vector<long> vec(...);  
boost::compute::transform(  
  vec.begin(), vec.end(), vec.begin(), boost::compute::identity<long>()  
);  
```  
  
Error that we would probably get:  
  
``` bash  
/Users/travis/build/haahh/compute/include/boost/compute/detail/meta_kernel.hpp:451:19: error: no matching function for call to 'type_name'  
        stream << type_name<Type>();  
                  ^~~~~~~~~~~~~~~  
/Users/travis/build/haahh/compute/include/boost/compute/detail/meta_kernel.hpp:375:19: note: in instantiation of function template specialization 'boost::compute::detail::meta_kernel::type<long *>' requested here  
        stream << type<T>() << " " << name;  
(...)  
```  
  
This is because we do not define `type_name<long>()` but probably `type_name<long long>()` as in this case C++ `long long` is OpenCL `long`  
  
I think we have to ways to fix that (if you think that needs to be somehow fixed):  
- We can put a note/warning about this problem with types in documentation instructing users to use `boost::compute::<type_name>_` types (or just to have that issue in mind) and change all types in test to `boost::compute::<type_name>_` that have correct size  (`long` --> 'boost::compute::long_` ).  
- We can define correct `type_name<T>()` for every fundamental C++ based on their real size, so if `sizeof(long) == 4`  then `type_name<long>()` returns `"int"` string.  
##
