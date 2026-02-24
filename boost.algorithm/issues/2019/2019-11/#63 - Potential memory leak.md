# #63 - Potential memory leak? [Closed]

> Username: mathstuf  
> Created at: 2019-11-26 12:54:13 UTC  
> Updated at: 2020-04-08 03:47:11 UTC  
> Closed at: 2020-04-08 03:47:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63  

`clang-tidy` on a project brought this to my attention:  
  
```  
/builds/ben.boeckel/smtk/build/../smtk/attribute/Attribute.cxx:234:3: note: Calling 'split<std::vector<std::__cxx11::basic_string<char>, std::allocator<std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char> &, boost::algorithm::detail::is_any_ofF<char>>'  
  boost::split(tree, path, boost::is_any_of(seps));  
  ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/split.hpp:161:17: note: Calling 'token_finder<boost::algorithm::detail::is_any_ofF<char>>'  
                ::boost::algorithm::token_finder( Pred, eCompress ) );           
                ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/finder.hpp:219:55: note: Calling copy constructor for 'is_any_ofF<char>'  
            return detail::token_finderF<PredicateT>( Pred, eCompress );  
                                                      ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:114:21: note: Taking false branch  
                    if(use_fixed_storage(m_Size))  
                    ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:123:44: note: Memory is allocated  
                        m_Storage.m_dynSet=new set_value_type[m_Size];  
                                           ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/finder.hpp:219:55: note: Returning from copy constructor for 'is_any_ofF<char>'  
            return detail::token_finderF<PredicateT>( Pred, eCompress );  
                                                      ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/finder.hpp:219:55: note: Calling '~is_any_ofF'  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:135:51: note: Left side of '&&' is false  
                    if(!use_fixed_storage(m_Size) && m_Storage.m_dynSet!=0)  
                                                  ^  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:139:17: note: Potential memory leak  
                }  
                ^  
```  
  
There is apparently some way to assign to `m_Storage.m_dynSet` while not freeing it on the way out. I suspect some kind of condition mismatch. Or is this a false-positive?

---

## Comment 1

> Username: mclow  
> Created at: 2019-11-27 21:22:15 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63#issuecomment-559255148  

I'm not seeing any way to assign to `m_Storage.m_dynSet` w/o having `use_fixed_storage(m_Size)` be false.  Are you?  
  
This is not how I would write this code today, but I don't see a bug there.

---

## Comment 2

> Username: mathstuf  
> Created at: 2019-11-28 00:01:59 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63#issuecomment-559290656  

That may indeed be true. Maybe this is actually a clang-tidy bug. See this conflicting set of logic:  
  
```  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:114:21: note: Taking false branch  
                    if(use_fixed_storage(m_Size))  
                    ^  
```  
  
and then later, assuming the inverse of it:  
  
```  
/root/misc/root/smtk-deps/include/boost/algorithm/string/finder.hpp:219:55: note: Calling '~is_any_ofF'  
/root/misc/root/smtk-deps/include/boost/algorithm/string/detail/classification.hpp:135:51: note: Left side of '&&' is false  
                    if(!use_fixed_storage(m_Size) && m_Storage.m_dynSet!=0)  
                                                  ^  
```  
  
It seems there are 2 report instances in the description; I'll trim it to just one.

---

## Comment 3

> Username: mclow  
> Created at: 2019-11-29 15:00:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63#issuecomment-559816030  

You could test out your theory by inverting the test at classification.hpp:114 (just change the test to `if(!use_fixed_storage(m_Size))` and swap the if and else code blocks) and see if clang-tidy still complains.

---

## Comment 4

> Username: mathstuf  
> Created at: 2019-12-07 20:55:19 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63#issuecomment-562885647  

Filed upstream: https://bugs.llvm.org/show_bug.cgi?id=44247

---

## Comment 5

> Username: mclow  
> Created at: 2020-04-08 03:47:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/63#issuecomment-610736057  

Closing this, since it's not our bug.
