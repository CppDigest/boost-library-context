# #1 - diagnostic message on using non-polymorphic hierarchies [Closed]

> Username: akrzemi1  
> Created at: 2025-04-16 19:10:56 UTC  
> Updated at: 2025-05-12 21:19:44 UTC  
> Closed at: 2025-05-12 21:19:44 UTC  
> Url: https://github.com/boostorg/openmethod/issues/1  

Consider the following program.  
  
```c++  
#include <iostream>  
#include <memory>  
#include <boost/openmethod.hpp>  
#include <boost/openmethod/compiler.hpp>  
  
struct IData  
{  
    ~IData() {} // forgot `virtual`  
};      
  
struct Data : IData  
{  
    int value;  
    explicit Data(int v) : value(v) {}  
};  
  
  
BOOST_OPENMETHOD(  
    value, (virtual_ptr<const IData>), int  
);  
      
BOOST_OPENMETHOD_OVERRIDE(  
    value, (virtual_ptr<const Data> d), int  
)   
{  
    return d->value;  
}  
  
  
BOOST_OPENMETHOD_CLASSES(IData, Data);  
      
int main()  
{  
    boost::openmethod::initialize();  
      
    std::unique_ptr<IData> d(new Data{11});  
    std::cout << "value: " << value(*d) << std::endl;  
}  
```  
  
I forgot to make my destructor `virtual`, as a consequence the program has a strange behavior:  it doesn't throw any exception, it doesn't terminate with a visible error report. I would expect that either `initialize` or the call to `value` would detect my mistake and report a runtime error.

---

## Comment 1

> Username: jll63  
> Created at: 2025-04-16 22:54:53 UTC  
> Updated at: 2025-04-16 23:54:37 UTC  
> Url: https://github.com/boostorg/openmethod/issues/1#issuecomment-2811036557  

Thank you for the report.  
  
YOMM2 (from which this library is derived) used to `static_assert` on non-polymorphic types used as virtual arguments. Then it acquired the capacity to deal with them, in three ways: "final" constructs, RTTI customization, and intrusive vptrs.   
  
If you remove the virtual destructor in [ast_unique_ptr.cpp](https://github.com/jll63/Boost.OpenMethod/blob/6f77e9ab4bbee3ed2fae5abde9d5257f763ca0d6/examples/ast_unique_ptr.cpp#L21), it will be correct as far as OpenMethod is concerned. It will still be incorrect, though, because  _std::unique_ptr_ will call the wrong dtor.  
  
You are the second reviewer who reports this and I agree with you. I am going to put the compile-time test back. Only it is a little more subtle than a blanket `static_assert(std::is_polymorphic_v<...>).  
  
As for the absence of diagnostic in debug builds, that is a bug. Fixed in the PR. Now you will get:  
```  
no applicable overrider for boost::openmethod::method<value_boost_openmethod (boost::openmethod::virtual_ptr<IData const, boost::openmethod::policies::debug>), int, boost::openmethod::policies::debug>(void)  
Aborted (core dumped)  
```

---

## Comment 2

> Username: akrzemi1  
> Created at: 2025-04-30 21:15:29 UTC  
> Url: https://github.com/boostorg/openmethod/issues/1#issuecomment-2843301666  

> YOMM2 (from which this library is derived) used to static_assert on non-polymorphic types used as virtual arguments. Then it acquired the capacity to deal with them, in three ways: "final" constructs, RTTI customization, and intrusive vptrs.  
  
I am still uncomfortable about this. In my example I didn't use any "advanced" features: no RTTI customizations, no intrusive vptrs. For my simple use case, I would expect to get a compiler error. Why wait until run time?   
  
Are these advanced features (RTTI customizations, intrusive vptrs) enabled dynamically?

---

## Comment 3

> Username: jll63  
> Created at: 2025-04-30 23:00:07 UTC  
> Url: https://github.com/boostorg/openmethod/issues/1#issuecomment-2843628146  

> > YOMM2 (from which this library is derived) used to static_assert on non-polymorphic types used as virtual arguments. Then it acquired the capacity to deal with them, in three ways: "final" constructs, RTTI customization, and intrusive vptrs.  
>   
> I am still uncomfortable about this. In my example I didn't use any "advanced" features: no RTTI customizations, no intrusive vptrs. For my simple use case, I would expect to get a compiler error. Why wait until run time?  
  
Indeed it can probably be detected. I'll look into it. This week I seem to be writing more emails than code ;-)  
  
> Are these advanced features (RTTI customizations, intrusive vptrs) enabled dynamically?  
  
No, it's all `if constexpr` and the like.

---

## Comment 4

> Username: jll63  
> Created at: 2025-05-03 19:19:34 UTC  
> Updated at: 2025-05-03 21:18:20 UTC  
> Url: https://github.com/boostorg/openmethod/issues/1#issuecomment-2848769065  

Available in "review" branch.  
  
I opted for disabling ctors and assignment using `enable_if` over `static_assert`, to tests and users to detect what is available. I believe that is also the way it would go with concepts.  
  
CE: https://godbolt.org/z/MoYTz4vEY
