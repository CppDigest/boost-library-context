# #22 string_ref: Add support for boost::container::basic_string. [Closed]

> Username: tavianator  
> Created at: 2015-10-26 21:54:17 UTC  
> Updated at: 2015-12-03 16:13:53 UTC  
> Closed at: 2015-12-03 16:13:45 UTC  
> Url: https://github.com/boostorg/utility/pull/22  

This implements https://svn.boost.org/trac/boost/ticket/11333.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-10-26 22:35:12 UTC  
> Url: https://github.com/boostorg/utility/pull/22#issuecomment-151304333  

I don't like this.  It opens the door to an endless series of requests like "please support `QtString` `MyString`, etc.   I'd much rather have a generic way of adding support for string_ref to an arbitrary string class.

---

## Comment 2

> Username: tavianator  
> Created_at: 2015-10-27 00:01:01 UTC  
> Url: https://github.com/boostorg/utility/pull/22#issuecomment-151318204  

Understood, although I would say there's a difference between Boost supporting its own string class and Qt's.  (See also https://svn.boost.org/trac/boost/ticket/11259 which I think was a similar request.)  
  
But actually, I guess a generic way of supporting arbitrary string classes wouldn't be too difficult.  Something like  
  
``` cpp  
// Specialize for your string type  
template <typename CharT, typename Traits, typename String>  
struct basic_string_refable;  
  
template <typename CharT, typename Traits, typename Allocator>  
struct basic_string_refable<CharT, Traits, std::basic_string<CharT, Traits, Allocator>>  
{  
  typedef std::basic_string<CharT, Traits, Allocator> String;  
  
  const CharT* data(const String& str) {  
    return str.data();  
  }  
  
  std::size_t length(const String& str) {  
    return str.length();  
  }  
};  
```

---

## Comment 3

> Username: mclow  
> Created_at: 2015-10-27 03:55:57 UTC  
> Url: https://github.com/boostorg/utility/pull/22#issuecomment-151365456  

On a different note - we don't accept pull requests into `master`. We make changes on the `develop` branch, and then, after the tests have cycled, merge to master.

---
