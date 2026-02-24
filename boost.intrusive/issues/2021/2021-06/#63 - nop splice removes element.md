# #63 - nop splice removes element [Closed]

> Username: J-Richter  
> Created at: 2021-06-04 08:45:25 UTC  
> Updated at: 2021-06-28 06:31:28 UTC  
> Closed at: 2021-06-19 17:39:47 UTC  
> Url: https://github.com/boostorg/intrusive/issues/63  

Calling boost::intrusive::list<>::splice()   
`  
   void splice(const_iterator p, list_impl&x, const_iterator f, const_iterator e)  
`  
with e == p will not leave the list unchanged as it should be.  
  
```  
  template<class T>  
  using ilist = boost::intrusive::list<T, boost::intrusive::base_hook<ilist_node>,  
       boost::intrusive::constant_time_size<false> >;  
  ilist<S> blist;  
  S s1( 1 );  
  S s2( 2 );  
  S s3( 3 );  
  blist.push_back( s1 );  
  blist.push_back( s2 );  
  blist.push_back( s3 );  
  blist.splice( next(blist.begin()), blist, blist.begin(), next(blist.begin()) );  
  for( auto& s : blist )  
    std::cout << s.val << std::endl;  
```  
  
Results in "2 3" and not "1 2 3" as it should be.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-06-19 17:41:43 UTC  
> Url: https://github.com/boostorg/intrusive/issues/63#issuecomment-864441212  

Many thanks for the report!

---

## Comment 2

> Username: J-Richter  
> Created at: 2021-06-22 07:24:24 UTC  
> Url: https://github.com/boostorg/intrusive/issues/63#issuecomment-865667183  

Many thanks!  
  
But I think the p != e comparison is unnecessary because it must not occur due to the precondition: p not in [b,e)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2021-06-25 21:25:00 UTC  
> Url: https://github.com/boostorg/intrusive/issues/63#issuecomment-868841321  

[b, e) It is a half-open range a la STL, so I interpret that included in [b, e) means p can be b but not e, because the half-open range does not include e.

---

## Comment 4

> Username: J-Richter  
> Created at: 2021-06-28 06:31:28 UTC  
> Url: https://github.com/boostorg/intrusive/issues/63#issuecomment-869399541  

Yes, p not in [b, e) means that p should never be b. But p can be e. So a check for p == b should not be necessary. Other list implementations do the same. It is the responsibility of the developer to ensure p != b. But the responsibility of the implementation to ensure correctness when p == e.
