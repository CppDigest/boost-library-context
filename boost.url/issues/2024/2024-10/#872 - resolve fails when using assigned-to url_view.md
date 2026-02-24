# #872 - resolve fails when using assigned-to url_view [Closed]

> Username: mgroenhoff  
> Created at: 2024-10-24 08:53:12 UTC  
> Updated at: 2024-10-25 07:08:05 UTC  
> Closed at: 2024-10-25 01:01:23 UTC  
> Url: https://github.com/boostorg/url/issues/872  

I am running into an issue where first defining an empty `url_view` and later assigning it from an `url` makes `resolve` return an error (case 2) whereas constructing an `url_view` with an `url` works fine (case 1):  
  
```cpp  
#include <boost/url.hpp>  
#include <iostream>  
  
void test_resolve(boost::url_view base, boost::url_view ref) {  
    boost::url dest;  
    auto resolve_result = boost::urls::resolve(base, ref, dest);  
    if (resolve_result) {  
        std::cerr << "ok: " << dest << std::endl;  
    } else {  
        std::cerr << "error: " << resolve_result.error().message() << std::endl;  
    }  
}  
  
int main() {  
    boost::url base{"https://127.0.0.1"};  
    boost::url_view ref = "/foo";  
  
    // Case 1  
    boost::url_view base_view1{base}; // constructing works  
    assert(base_view1.has_scheme()); // ok. doesn't assert  
    test_resolve(base_view1, ref); // outputs "ok"  
  
    // Case 2  
    boost::url_view base_view2;  
    base_view2 = base; // assign instead of construct  
    assert(base_view2.has_scheme()); // ok. doesn't assert  
    test_resolve(base_view2, ref); // outputs: "error: not_a_base" even though the assert above doesn't fire  
  
    return 0;  
}  
  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-10-24 14:45:06 UTC  
> Updated at: 2024-10-24 14:45:53 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2435502756  

I'm trying to replicate it but  
  
```  
resolve(base_view1, ref);   
```  
  
Does this function exist?  
  
  
The free function has 3 arguments: https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/resolve.html  
  
And the member function has only 1: https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/url_base/resolve.html  
  
It can't be  
  
```  
base_view1.resolve(ref);   
```  
  
either because views don't have the `resolve` member function.

---

## Comment 2

> Username: mgroenhoff  
> Created at: 2024-10-24 14:49:46 UTC  
> Updated at: 2024-10-24 14:52:02 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2435514961  

@alandefreitas It is the function in the example above `main` which is simply a small wrapper. Maybe I should've given it another name to avoid confusion.  
  
edit: renamed `resolve` to `test_resolve`

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-10-24 14:51:25 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2435519414  

Oh... Let me replicate that without the wrapper then.

---

## Comment 4

> Username: mgroenhoff  
> Created at: 2024-10-24 15:14:57 UTC  
> Updated at: 2024-10-24 15:17:00 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2435578492  

I tried to make the example even smaller and it seems I don't even need the `resolve`. Here is a smaller example where the last assert goes off:  
  
```cpp  
#include <boost/url.hpp>  
#include <cassert>  
  
int main() {  
    boost::url base{"https://127.0.0.1"};  
  
    // Case 1  
    {  
        boost::url_view base_view{base}; // construct view of base  
        assert(base_view.has_scheme()); // ok. doesn't assert  
  
        boost::url base_copy{base_view}; // copy from view of base  
        assert(base_copy.has_scheme()); // ok. doesn't assert  
    }  
  
    // Case 2  
    {  
        boost::url_view base_view; // construct empty view  
        base_view = base; // assign to be a view of base  
        assert(base_view.has_scheme());  
  
        boost::url base_copy{base_view}; // copy from view of base  
        assert(base_copy.has_scheme()); // error: asserts  
    }  
  
    return 0;  
}  
```

---

## Comment 5

> Username: alandefreitas  
> Created at: 2024-10-24 15:59:12 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2435679687  

Thanks. I replicated it as:  
  
```cpp  
        // issue #872  
        {  
            url base{"https://127.0.0.1"};  
            url_view ref = "/foo";  
  
            // url_view(url)  
            {  
                url_view base_view{base};  
                BOOST_TEST(base_view.has_scheme());  
                url dest;  
                auto res = resolve(base_view, ref, dest);  
                BOOST_TEST(res);  
                BOOST_TEST(dest.has_scheme());  
                BOOST_TEST_EQ(dest.buffer(), "https://127.0.0.1/foo");  
            }  
  
            // url_view::operator=(url)  
            {  
                url_view base_view;  
                base_view = base;  
                BOOST_TEST(base_view.has_scheme());  
                url dest;  
                auto res = resolve(base_view, ref, dest);  
                BOOST_TEST(res);  
                BOOST_TEST(dest.has_scheme());  
                BOOST_TEST_EQ(dest.buffer(), "https://127.0.0.1/foo");  
            }  
        }  
```

---

## Comment 6

> Username: mgroenhoff  
> Created at: 2024-10-25 07:08:05 UTC  
> Url: https://github.com/boostorg/url/issues/872#issuecomment-2437055022  

Thanks for fixing this so quickly!
