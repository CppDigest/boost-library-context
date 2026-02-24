# #72 - Post-increment operator fails on an rvalue iterator [Closed]

> Username: ghost  
> Created at: 2022-06-01 01:51:26 UTC  
> Updated at: 2022-06-02 01:29:54 UTC  
> Closed at: 2022-06-01 08:55:37 UTC  
> Url: https://github.com/boostorg/iterator/issues/72  

We have a custom container and a `begin` function that produces a `iterator_facade`-derived type:  
```cpp  
Container c;  
  
// This fails:  
auto i = begin(c)++;  
  
// This succeeds:  
auto j = begin(c);  
j++;  
```  
  
Could the `operator++` in the following code take a universal reference (`&&`) instead of a standard reference (`&`)?:  
```cpp  
  template <class I, class V, class TC, class R, class D>  
  inline typename boost::iterators::detail::postfix_increment_result<I,V,R,TC>::type  
  operator++(  
      iterator_facade<I,V,TC,R,D>& i   
    , int  
  )  
  {  
      typename boost::iterators::detail::postfix_increment_result<I,V,R,TC>::type  
          tmp(*static_cast<I*>(&i));  
  
      ++i;  
  
      return tmp;  
  }  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-06-01 08:53:06 UTC  
> Updated at: 2022-06-01 08:56:34 UTC  
> Url: https://github.com/boostorg/iterator/issues/72#issuecomment-1143319193  

> ```  
> auto i = begin(c)++;  
> ```  
  
You're discarding the result of increment here, `i` is initialized with `begin(c)` and not the incremented iterator. Lvalue reference prevented you from this mistake. So I don't see a motivating example for the requested change - quite the opposite.  
  
If you want to obtain an incremented/decremented iterator you can use `std::next`/`std::prev` or Boost [equivalents](https://github.com/boostorg/iterator/blob/d175ba245026adf22d7cea7d992079162f5110ea/include/boost/next_prior.hpp#L175-L191).

---

## Comment 2

> Username: ghost  
> Created at: 2022-06-02 01:29:53 UTC  
> Url: https://github.com/boostorg/iterator/issues/72#issuecomment-1144322187  

Thanks for your quick response!  
  
Now that you point it out I realise that I didn't actually consider whether it was "useful" or not - only whether it was "correct" or not!!!  We stumbled across the error in a test-case for the iterator we'd written so the use-case is contrived.  The equivalent call works in the case where `Container` is `std::vector` (on VS2019)  so I suspected this case should work too.  Probably fine to keep it as is 👍
