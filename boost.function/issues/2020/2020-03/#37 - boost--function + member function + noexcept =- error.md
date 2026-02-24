# #37 - boost::function + member function + noexcept => error [Closed]

> Username: sf-mc  
> Created at: 2020-03-24 19:10:20 UTC  
> Updated at: 2020-03-25 16:56:48 UTC  
> Closed at: 2020-03-25 16:56:48 UTC  
> Url: https://github.com/boostorg/function/issues/37  

Code gives compile error in GCC, Clang, MSVC in C++17 mode:  
```  
#include <boost/function.hpp>  
int func1(int) {  
    return 0;  
}  
int func2(int) noexcept {  
    return 0;  
}  
struct test_t {  
    int mem_func1(int)  {  
        return 0;  
    }  
    int mem_func2(int) noexcept {  
        return 0;  
    }  
};  
void test()  
{  
    boost::function1<int, int> f1(&func1); // OK  
    boost::function1<int, int> f2(&func2); // OK  
    boost::function2<int, test_t*, int> mf1(&test_t::mem_func1); // OK  
    boost::function2<int, test_t*, int> mf2(&test_t::mem_func2); // error C2064: term does not evaluate to a function taking 2 arguments  
}  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-25 13:33:15 UTC  
> Url: https://github.com/boostorg/function/issues/37#issuecomment-603841689  

This is a problem with `mem_fn` and should be fixed with https://github.com/boostorg/bind/commit/e31c1f77e6c51d7c6eb6a7053296dbc3b627f28c.

---

## Comment 2

> Username: sf-mc  
> Created at: 2020-03-25 16:28:24 UTC  
> Url: https://github.com/boostorg/function/issues/37#issuecomment-603942989  

The case with 'noexcept' is fixed now.  
I checked some other combinations, and apparently it isn't working with the 'volatile' qualifier:  
```  
struct test_t {  
    int mem_func1(int)  {  
        return 0;  
    }  
    int mem_func2(int) noexcept {  
        return 0;  
    }  
    int mem_func3(int) const {  
        return 0;  
    }  
    int mem_func4(int) volatile {  
        return 0;  
    }  
    int mem_func5(int) const volatile {  
        return 0;  
    }  
    int mem_func6(int) const volatile noexcept {  
        return 0;  
    }  
    int mem_func7(int) const noexcept {  
        return 0;  
    }  
    int mem_func8(int) volatile noexcept {  
        return 0;  
    }  
};  
void test_mf()  
{  
    boost::function2<int, test_t*, int> mf1(&test_t::mem_func1); // OK  
    boost::function2<int, test_t*, int> mf2(&test_t::mem_func2); // OK  
    boost::function2<int, test_t*, int> mf3(&test_t::mem_func3); // OK  
    boost::function2<int, test_t*, int> mf4(&test_t::mem_func4); // error C2064: term does not evaluate to a function taking 2 arguments  
    boost::function2<int, test_t*, int> mf5(&test_t::mem_func5); // error C2064: term does not evaluate to a function taking 2 arguments  
    boost::function2<int, test_t*, int> mf6(&test_t::mem_func6); // error C2064: term does not evaluate to a function taking 2 arguments  
    boost::function2<int, test_t*, int> mf7(&test_t::mem_func7); // OK  
    boost::function2<int, test_t*, int> mf8(&test_t::mem_func8); // error C2064: term does not evaluate to a function taking 2 arguments  
}  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2020-03-25 16:52:18 UTC  
> Url: https://github.com/boostorg/function/issues/37#issuecomment-603956978  

I don't think volatile-qualified functions have ever been supported.

---

## Comment 4

> Username: sf-mc  
> Created at: 2020-03-25 16:56:48 UTC  
> Url: https://github.com/boostorg/function/issues/37#issuecomment-603959657  

Maybe. Anyway, the issue with 'noexcept' is fixed.
