# #106 - Replace use of boost type-traits by std type-traits. [Closed]

> Username: stefanseefeld  
> Created at: 2017-02-08 12:41:29 UTC  
> Updated at: 2017-06-25 18:17:42 UTC  
> Closed at: 2017-06-25 18:17:42 UTC  
> Url: https://github.com/boostorg/python/issues/106  

While this is a good idea in general, this also fixes certain issues (e.g. https://github.com/boostorg/python/issues/105).  
  
This may require some code refactoring, to avoid the code being cluttered with #ifdefs.

---

## Comment 1

> Username: shreyans800755  
> Created at: 2017-05-27 15:20:32 UTC  
> Updated at: 2017-05-27 15:21:25 UTC  
> Url: https://github.com/boostorg/python/issues/106#issuecomment-304458532  

@stefanseefeld @dabrahams   
I would like to work on this issue.  
  
Just to confirm and get along with contributing method, I need to create PR against `develop` branch, right ?  
EDIT: Is there anybody else working on this issue ? If so, I can pick up something else.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-05-27 16:07:57 UTC  
> Url: https://github.com/boostorg/python/issues/106#issuecomment-304461058  

@shreyans800755 , great ! I'm not aware of anyone else working on this right now, so this would be a great contribution. Please be sure to conditionalize these substitutions to only be active whenever a compiler with the appropriate language support is used. We need to maintain backward compatibility.  
  
I suggest a new `boost/python/detail/type_traits.hpp` header for this.  
  
PRs need to be created against the develop branch.

---

## Comment 3

> Username: shreyans800755  
> Created at: 2017-06-12 18:31:45 UTC  
> Url: https://github.com/boostorg/python/issues/106#issuecomment-307878312  

@stefanseefeld   
I'm trying to apply the patch like following:  
Adding  
```  
#if __cplusplus < 201103L  
          typename boost::remove_cv<T>::type  
#else  
          typename std::remove_cv<T>::type  
#endif  
```  
and removing  
`typename boost::remove_cv<T>::type`  
  
I don't understand why we need a new file `boost/python/detail/type_traits.hpp`. Can you please explain ? Am I missing something ?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-06-12 18:39:04 UTC  
> Url: https://github.com/boostorg/python/issues/106#issuecomment-307880549  

I expect that with the above approach you would end up with lots of locations in the code that would use such a conditional logic, which IMO makes the code hard to read.  
  
Thus what I'd propose is something like:  
```  
namespace boost { namespace python { namespace detail {  
#if __cplusplus < 201103L  
  using boost::remove_cv;  
  ...  
#else  
  using std::remove_cv;  
  ..  
}}} // namespace boost::python::detail  
```  
such that in the rest of Boost.Python's code you could then simply refer to `boost::python::detail::remove_cv` etc., not having to care which original definition this aliases.  
(Note, however, that this is just an idea that I would try out. I haven't actually tested this, and thus could be convinced that it isn't workable. I'd put the conditional above into a new header `boost/python/detail/type_traits.hpp`...)

---

## Comment 5

> Username: shreyans800755  
> Created at: 2017-06-12 19:13:06 UTC  
> Url: https://github.com/boostorg/python/issues/106#issuecomment-307890742  

That's wonderful idea. I knew, changes were a bit cumbersome. This sounds like an elegant solution. I'll try to move ahead with this.
