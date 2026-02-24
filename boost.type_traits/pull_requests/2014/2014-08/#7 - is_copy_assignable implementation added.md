# #7 is_copy_assignable implementation added. [Merged]

> Username: igaztanaga  
> Created at: 2014-08-26 21:34:38 UTC  
> Updated at: 2014-08-31 20:43:45 UTC  
> Merged at: 2014-08-31 17:52:21 UTC  
> Closed at: 2014-08-31 17:52:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/7  

If I haven't done something incorrectly when creating this pull request, this adds boost::is_copy_assignable trait.  
  
I've roughly followed boost::is_copy_constructible implementation and tests as a guide.  
  
boost::is_copy_assignable can't be correctly implemented unless deleted expreesion and SFINAE are available. One possible implementation is:  
  
template< class T>  
struct is_copy_assignable  
    : std::is_assignable< typename std::add_lvalue_reference<T>::type,  
                          typename std::add_lvalue_reference<const T>::type> {};  
  
which is translated in the implementation to a test to see if the expression:  
  
declval<T&>() = declval<const T&>()  
  
is well formed.  
  
For non-conforming/old compilers arrays,  const element types, types derived from noncopyable or tagged with BOOST_MOVEABLE_BUT_NOT_COPYABLE  are detected as non-assignable. Otherwise they are declared as copy assignable.  
  
Compiled in several MSVC, GCC and Clang compilers.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-31 17:53:25 UTC  
> Url: https://github.com/boostorg/type_traits/pull/7#issuecomment-53995624  

Thanks for that.  I wonder, can any of these techniques be used for default-constructable as well?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2014-08-31 20:43:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/7#issuecomment-54000663  

We need decltype to implement is_default_constructible. The implementation hsould not be complicated for conforming compilers.  
  
For C++03 compilers we might aproximate with:  
-> Arrays are default constructible if remove_all_extents<T>::type is default constructible.  
-> Non-object and const non-object types (function types, reference types, and void) are never DefaultConstructible  
-> Const non-class object types are not DefaultConstructible.  
-> Otherwise (class types) we default to DefaultConstructible.  
  
AFAIK, there is no boost utility that we could detect to mark a class as non-default constructible. Do you think this is a good approach? I could try another pull request when I find time in a couple of weeks.  
  
Const non-class types are not DefaultConstructible.  
  
Const aggregate types are not DefaultConstructible if any of their members is an object of non-class type.  
  
Non-object types (function types, reference types, and the (possibly cv-qualified) type void) as well as the const non-object types are never DefaultConstructible

---
