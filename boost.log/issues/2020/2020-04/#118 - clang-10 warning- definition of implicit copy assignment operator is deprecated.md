# #118 - clang-10 warning: definition of implicit copy assignment operator is deprecated [Closed]

> Username: k15tfu  
> Created at: 2020-04-20 16:22:38 UTC  
> Updated at: 2020-04-20 19:07:36 UTC  
> Closed at: 2020-04-20 17:34:32 UTC  
> Url: https://github.com/boostorg/log/issues/118  

```  
include/boost/log/attributes/attribute_set.hpp:176:9: error: definition of implicit copy assignment operator for 'iter<false>' is deprecated because it has a user-declared copy constructor [-Werror,-Wdeprecated-copy]  
        iter(iter< false > const& that) BOOST_NOEXCEPT : m_pNode(that.m_pNode) {}  
        ^  
/home/user/p/dotnet-products/3/Packages/JetBrains.boost.include.1.71.0.65/build/native/include/boost/log/attributes/scoped_attribute.hpp:82:27: note: in implicit copy assignment operator for 'boost::log::v2s_mt_posix::attribute_set::iter<false>' first required here  
            m_itAttribute = res.first;  
                          ^  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-20 16:35:47 UTC  
> Url: https://github.com/boostorg/log/issues/118#issuecomment-616669613  

This is a bug in clang, as there is an explicitly defined assignment operator in this class. Please, report it to clang devs.

---

## Comment 2

> Username: k15tfu  
> Created at: 2020-04-20 17:09:29 UTC  
> Url: https://github.com/boostorg/log/issues/118#issuecomment-616690174  

from https://en.cppreference.com/w/cpp/language/copy_assignment:  
> A copy assignment operator of class T is a non-template non-static member function  
  
@Lastique Sorry, I don't see such definition in `iter` class. Could you point me?

---

## Comment 3

> Username: Lastique  
> Created at: 2020-04-20 17:16:22 UTC  
> Updated at: 2020-04-20 17:18:05 UTC  
> Url: https://github.com/boostorg/log/issues/118#issuecomment-616694356  

It's not a copy assignment, it's an assignment operator defined here: https://github.com/boostorg/log/blob/c068c2a5aa94fcdcfe554414487e5d0d60e8a0e8/include/boost/log/attributes/attribute_set.hpp#L179-L184  
  
That operator works for both conversion and copy.

---

## Comment 4

> Username: k15tfu  
> Created at: 2020-04-20 17:54:51 UTC  
> Url: https://github.com/boostorg/log/issues/118#issuecomment-616715385  

@Lastique I see you fixed it. So there was nothing to report to clang, yep?

---

## Comment 5

> Username: Lastique  
> Created at: 2020-04-20 19:07:36 UTC  
> Url: https://github.com/boostorg/log/issues/118#issuecomment-616750989  

I added a workaround, but I still think clang is issuing a bogus warning.
