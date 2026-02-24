# #212 - docs for static_pool_allocator are rendered incorrectly [Closed]

> Username: vinniefalco  
> Created at: 2022-06-27 21:41:49 UTC  
> Updated at: 2022-07-16 02:22:41 UTC  
> Closed at: 2022-07-16 02:22:41 UTC  
> Url: https://github.com/boostorg/url/issues/212  

https://master.url.cpp.al/url/ref/boost__urls__static_pool_allocator.html  
  
![image](https://user-images.githubusercontent.com/1503976/176040526-c0898c47-d268-4a5c-bd03-21204e2fc876.png)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-28 01:20:30 UTC  
> Url: https://github.com/boostorg/url/issues/212#issuecomment-1168102923  

That's weird. The code looks fine:  
  
```cpp  
/** The type of allocator returned by static_pool  
*/  
#ifdef BOOST_URL_DOCS  
template<class T>  
using static_pool_allocator = __see_below__  
#else  
template<class T>  
class static_pool_allocator;  
#endif  
```  
  
The Doxygen XML file also looks file, so this is probably a Docca issue:  
  
```xml  
<section id="url.ref.boost__urls__static_pool_allocator">  
      <title><link linkend="url.ref.boost__urls__static_pool_allocator">static_pool_allocator</link></title>  
      <para>  
        <indexterm><primary>static_pool_allocator</primary></indexterm>  
      </para>  
      <para>  
        The type of allocator returned by static_pool.  
      </para>  
      <bridgehead renderas="sect4" id="url.ref.boost__urls__static_pool_allocator.h0">  
        <phrase id="url.ref.boost__urls__static_pool_allocator.synopsis"/><link linkend="url.ref.boost__urls__static_pool_allocator.synopsis">Synopsis</link>  
      </bridgehead>  
      <para>  
        Defined in header <literal>&lt;<ulink url="https://github.com/CPPAlliance/url/blob/master/include/boost/url/static_pool.hpp">boost/url/static_pool.hpp</ulink>&gt;</literal>  
      </para>  
<programlisting><phrase role="keyword">template</phrase><phrase role="special">&lt;</phrase>  
    <phrase role="keyword">class</phrase> <phrase role="identifier">T</phrase><phrase role="special">&gt;</phrase>  
<phrase role="keyword">using</phrase> <phrase role="identifier">static_pool_allocator</phrase> <phrase role="special">=</phrase> <emphasis>see-below</emphasis> <phrase role="keyword">class</phrase> <phrase role="identifier">basic_static_pool</phrase> <phrase role="special">{</phrase> <phrase role="keyword">char</phrase> <phrase role="special">*</phrase><phrase role="identifier">begin_</phrase><phrase role="special">;</phrase>  
</programlisting>  
      <para>  
        Convenience header <literal>&lt;<ulink url="https://github.com/CPPAlliance/url/blob/master/include/boost/url.hpp">boost/url.hpp</ulink>&gt;</literal>  
      </para>  
    </section>  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-28 01:33:20 UTC  
> Url: https://github.com/boostorg/url/issues/212#issuecomment-1168109048  

missing semicolon bro
