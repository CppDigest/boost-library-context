# #70 - Inheriting from boost::noncopyable also implies deleted move operations [Closed]

> Username: mbamelis  
> Created at: 2020-01-02 10:51:36 UTC  
> Updated at: 2020-01-16 17:38:17 UTC  
> Closed at: 2020-01-16 15:52:26 UTC  
> Url: https://github.com/boostorg/core/issues/70  

Hi  
  
In my code, I inherit from boost::noncopyable to remove the defaulted copy operations based on the properties of a template parameter. However, I noticed this also makes the defaulted move operations of my class implicitly deleted. This is due to boost::noncopyable not explicitly defining its move operations, and as a result, they are implicitly deleted. And my class inherits this.  
  
Is this intended or should boost::noncopyable explicitly mark its move operations as default?

---

## Comment 1

> Username: pdimov  
> Created at: 2020-01-02 17:16:26 UTC  
> Url: https://github.com/boostorg/core/issues/70#issuecomment-570275207  

If we mark move operations as default, won't this be a breaking change for people who rely on the current behavior? Their classes will unexpectedly acquire move constructors, which quite probably will be broken.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-01-02 20:55:35 UTC  
> Url: https://github.com/boostorg/core/issues/70#issuecomment-570344835  

IMHO, we should just mark `noncopyable` deprecated and eventually remove it. It is no longer relevant in C++11 and later era and it encourages bad programming practices.

---

## Comment 3

> Username: mbamelis  
> Created at: 2020-01-16 15:52:26 UTC  
> Url: https://github.com/boostorg/core/issues/70#issuecomment-575215825  

It avoids boilerplate because SFINAE tricks with `std::enable_if` cannot be applied on special member functions. With the introduction of [requires clauses](https://en.cppreference.com/w/cpp/language/constraints#Requires_clauses) in C++20, special member functions can be made conditionally available more easily and intuitively.  
  
And indeed, many classes may in some way rely on `noncopyable` also removing the move operations. Changing that behavior could be detrimental... and I actually had not considered that.  
  
Thanks for the input.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-01-16 17:38:17 UTC  
> Url: https://github.com/boostorg/core/issues/70#issuecomment-575263305  

> SFINAE tricks with `std::enable_if` cannot be applied on special member functions.  
  
Just for completeness, (a) they can and (b) they are not needed to prohibit copyability.
