# #150 - Some headers fail to compile independently [Closed]

> Username: tonyelewis  
> Created at: 2018-08-24 07:17:19 UTC  
> Updated at: 2018-09-25 16:19:19 UTC  
> Closed at: 2018-09-25 16:19:19 UTC  
> Url: https://github.com/boostorg/test/issues/150  

I think the previously-fixed Trac ticket [#12228](https://svn.boost.org/trac10/ticket/12228) has regressed.  
  
The [Boost Header Policy](https://www.boost.org/development/header.html) requires:  
  
> Make sure that a translation unit consisting of just the contents of the header file will compile successfully.  
  
Yet, I'm seeing compile errors for quite a few of the headers under Clang and GCC. Eg for `boost/test/data/monomorphic/initializer_list.hpp`:  
  
~~~no-highlight  
boost/test/data/monomorphic/initializer_list.hpp:45:27: error: no type named 'vector' in namespace 'std'  
    typedef typename std::vector<T>::const_iterator iterator;  
            ~~~~~~~~~~~~~~^~~~~~  
boost/test/data/monomorphic/initializer_list.hpp:45:33: error: expected member name or ';' after declaration specifiers  
    typedef typename std::vector<T>::const_iterator iterator;  
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
boost/test/data/monomorphic/initializer_list.hpp:64:5: error: unknown type name 'iterator'  
    iterator        begin() const   { return m_data.begin(); }  
    ^  
boost/test/data/monomorphic/initializer_list.hpp:68:10: error: no template named 'vector' in namespace 'std'  
    std::vector<T> m_data;  
    ~~~~~^  
boost/test/data/monomorphic/initializer_list.hpp:79:18: error: no template named 'vector' in namespace 'std'  
    typedef std::vector<bool>::const_iterator iterator;  
            ~~~~~^  
boost/test/data/monomorphic/initializer_list.hpp:79:32: error: non-friend class member 'const_iterator' cannot have a qualified name  
    typedef std::vector<bool>::const_iterator iterator;  
                             ~~^  
boost/test/data/monomorphic/initializer_list.hpp:79:32: error: typedef declarator cannot be qualified  
    typedef std::vector<bool>::const_iterator iterator;  
                             ~~^  
boost/test/data/monomorphic/initializer_list.hpp:79:46: error: expected ';' at end of declaration list  
    typedef std::vector<bool>::const_iterator iterator;  
                                             ^  
                                             ;  
boost/test/data/monomorphic/initializer_list.hpp:96:5: error: unknown type name 'iterator'  
    iterator        begin() const   { return m_data.begin(); }  
    ^  
boost/test/data/monomorphic/initializer_list.hpp:100:10: error: no template named 'vector' in namespace 'std'  
    std::vector<bool> m_data;  
    ~~~~~^  
10 errors generated.  
~~~  
  
I think there are problems compiling the following 20 headers:  
  
* `boost/test/data/dataset.hpp`  
* `boost/test/data/monomorphic/delayed.hpp`  
* `boost/test/data/monomorphic/grid.hpp`  
* `boost/test/data/monomorphic.hpp`  
* `boost/test/data/monomorphic/initializer_list.hpp`  
* `boost/test/data/monomorphic/join.hpp`  
* `boost/test/data/monomorphic/zip.hpp`  
* `boost/test/data/test_case.hpp`  
* `boost/test/tools/collection_comparison_op.hpp`  
* `boost/test/tools/detail/lexicographic_manip.hpp`  
* `boost/test/tools/detail/per_element_manip.hpp`  
* `boost/test/tree/test_case_template.hpp`  
* `boost/test/utils/runtime/argument_factory.hpp`  
* `boost/test/utils/runtime/cla/argv_traverser.hpp`  
* `boost/test/utils/runtime/cla/parser.hpp`  
* `boost/test/utils/runtime/env/fetch.hpp`  
* `boost/test/utils/runtime/finalize.hpp`  
* `boost/test/utils/runtime/modifier.hpp`  
* `boost/test/utils/runtime/parameter.hpp`  
* `boost/test/utils/xml_printer.hpp`  
  
Thanks for your continued work on this library.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-02 08:26:52 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-417913668  

Hi,  
  
Thanks for raising this again. How do you check if things are able to compile independently? Do you happen to have some magic compiler command? It would save me some time and I'll try to integrate that in the test process.

---

## Comment 2

> Username: dalg24  
> Created at: 2018-09-03 12:37:09 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-418103000  

To check that your header files are self-contained, you could check they work when included on their own.  
```C++  
#include YOUR_HEADER_HERE  
int main() { return 0; }  
```

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-03 19:22:43 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-418180710  

Yeah, but @dalg24 that would take me a day to check :)

---

## Comment 4

> Username: dalg24  
> Created at: 2018-09-04 03:52:11 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-418233602  

Not necessarily, you can probably inspire yourself from something like [this chkhdr script](https://stackoverflow.com/a/1892332).

---

## Comment 5

> Username: tonyelewis  
> Created at: 2018-09-08 13:17:46 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-419641462  

I don't know of any way to get the compiler to check that each header it encounters compiles independently. However, I've sometimes scanned within a Boost repo to identify any such headers with a command like:  
  
~~~bash  
find ./include -iname '*.hpp' | sort | grep detail -vw | grep impl -v | xargs -I VAR clang++ -isystem include -isystem /opt/include -fsyntax-only -x c++ -o /tmp/a.o VAR  
~~~  
  
This is a good approach to manually trigger a search for any problems. But @dalg24's suggested approach of providing you with an automated way of detecting any future regressions.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-09-25 16:19:19 UTC  
> Url: https://github.com/boostorg/test/issues/150#issuecomment-424409148  

Fixed in develop via #161 . Thanks to all for your feedbacks and help!
