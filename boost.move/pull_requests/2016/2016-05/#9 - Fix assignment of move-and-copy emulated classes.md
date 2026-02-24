# #9 Fix assignment of move-and-copy emulated classes [Merged]

> Username: Flamefire  
> Created at: 2016-05-12 11:32:42 UTC  
> Updated at: 2016-06-30 10:23:17 UTC  
> Merged at: 2016-06-30 10:22:58 UTC  
> Closed at: 2016-06-30 10:22:58 UTC  
> Url: https://github.com/boostorg/move/pull/9  

This works on the bug described here: https://svn.boost.org/trac/boost/ticket/12194 and which was introduced here: https://github.com/boostorg/move/commit/4f9c2b62fbdcf5995ecf50a2ecf2494048a6696d#diff-6a11d48d06dd33c1193ffb3d794787fbR252  
  
Testcode:  
  
```  
#include <boost/move/move.hpp>  
#include <cassert>  
  
class Foo{  
    BOOST_COPYABLE_AND_MOVABLE(Foo)  
public:  
    int i;  
    explicit Foo(int val): i(val){}  
    Foo(BOOST_RV_REF(Foo) obj): i(obj.i) {}  
    Foo& operator=(BOOST_RV_REF(Foo) rhs){ i = rhs.i; rhs.i = 0; return *this; }  
    Foo& operator=(BOOST_COPY_ASSIGN_REF(Foo) rhs){ i = rhs.i; return *this; }  
    template<class OTHER>  
    Foo& operator=(const OTHER& rhs){ i = rhs.j; return *this; }  
};  
  
struct Bar{  
    int j;  
    explicit Bar(int val): j(val){}  
};  
  
int main(){  
    Foo foo1(1);  
    Foo foo2(2);  
    Bar bar(3);  
    assert(foo1.i == 1);  
    assert(foo2.i == 2);  
    assert(bar.j == 3);  
    foo2 = foo1; // This works with this patch  
    assert(foo1.i == 1);  
    assert(foo2.i == 1);  
    foo1 = bar;  
    assert(foo1.i == 3);  
    foo2 = boost::move(foo1);  
    assert(foo1.i == 0);  
    assert(foo2.i == 3);  
    const Foo foo4(4);  
    foo2 = foo4; // This fails in either  
    assert(foo2.i == 4);  
    assert(foo4.i == 4);  
    return 0;  
}  
```  
  
I get the feeling that `BOOST_COPY_ASSIGN_REF(Foo)` was a bad idea.  
Note that the above code works fine in C++11.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-06-30 10:23:17 UTC  
> Url: https://github.com/boostorg/move/pull/9#issuecomment-229620487  

Thanks for the patch.

---
