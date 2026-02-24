# #46 Update str.hpp [Closed]

> Username: rick68  
> Created at: 2015-10-19 04:49:51 UTC  
> Updated at: 2016-09-29 12:12:58 UTC  
> Closed at: 2016-09-29 12:12:58 UTC  
> Url: https://github.com/boostorg/python/pull/46  

Fix a mistake in `str::count()`

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2015-10-19 11:56:01 UTC  
> Url: https://github.com/boostorg/python/pull/46#issuecomment-149193357  

Would you mind accompanying your patch with a test that demonstrates the changes you are proposing ?

---

## Comment 2

> Username: rick68  
> Created_at: 2015-10-22 03:25:15 UTC  
> Updated_at: 2015-10-22 03:27:40 UTC  
> Url: https://github.com/boostorg/python/pull/46#issuecomment-150096302  

hello.cpp  
  
``` c++  
#include <boost/python.hpp>  
using namespace boost::python;  
  
long count(str s, object sub) { return s.count(sub); }  
long count(str s, object sub, object start) { return s.count(sub, start); }  
long count(str s, object sub, object start, object end) { return s.count(sub, start, end); }  
  
BOOST_PYTHON_MODULE(hello)  
{  
    long (*count1)(str, object) = count;  
    long (*count2)(str, object, object) = count;  
    long (*count3)(str, object, object, object) = count;  
    def("count", count1);  
    def("count", count2);  
    def("count", count3);  
}  
```  
  
hello.py  
  
``` python  
r'''>>> import hello  
    >>> s = "Hello world"  
    >>> hello.count(s, 'l')  
    3  
    >>> hello.count(s, 'l', 3)  
    2  
    >>> hello.count(s, 'l', 0, 3)  
    1  
'''  
  
def run(args = None):  
    if args is not None:  
        import sys  
        sys.argv = args  
    import doctest  
    return doctest.testmod()  
  
if __name__ == '__main__':  
    import sys  
    sys.exit(run()[0])  
```

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2016-09-29 12:12:58 UTC  
> Url: https://github.com/boostorg/python/pull/46#issuecomment-250449086  

Merged (manually), thanks !

---
