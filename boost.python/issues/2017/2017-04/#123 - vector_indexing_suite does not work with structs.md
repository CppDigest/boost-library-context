# #123 - vector_indexing_suite does not work with structs [Open]

> Username: mg262  
> Created at: 2017-04-01 15:21:28 UTC  
> Updated at: 2017-04-10 10:13:22 UTC  
> Url: https://github.com/boostorg/python/issues/123  

If you define a simple struct  
  
```  
struct Foo {  
    string a;  
    string b;  
};  
  
```  
and then try to expose this via   
  
    class_<std::vector<Foo>>("FooList")  
            .def(vector_indexing_suite<std::vector<Foo>>() );  
  
This causes an error,   
  
> /usr/lib/gcc/x86_64-pc-cygwin/5.4.0/include/c++/bits/predefined_ops.h:194:17: error: no match for ‘operator==’ (operand types are ‘Foo’ and ‘const Foo’)  
  
Defining an explicit `operator==` fixes the problem --- but I'm wrapping code I can't change.  
  
Full log attached.  
[log.txt](https://github.com/boostorg/python/files/887739/log.txt)  
  
Using Windows/Cygwin, gcc 5.4.0, python 2.7 and Boost 1.60.0, all installed via Cygwin rather than windows.

---

## Comment 1

> Username: mg262  
> Created at: 2017-04-01 18:18:06 UTC  
> Url: https://github.com/boostorg/python/issues/123#issuecomment-290937643  

Also tried with clang -- problem persists.

---

## Comment 2

> Username: Svalorzen  
> Created at: 2017-04-10 10:13:22 UTC  
> Url: https://github.com/boostorg/python/issues/123#issuecomment-292907686  

Can't you define a free `operator==`? As in:  
  
    inline bool operator==(const Foo & lhs, const Foo & rhs) {  
        return lhs.a == rhs.a && lhs.b == rhs.b;  
    }  
  
You can define it just in the translation unit of the `vector_indexing_suite` if only needed there.
