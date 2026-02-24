# #29 Add test cases for std::reverse_iterator [Closed]

> Username: bminard  
> Created at: 2017-07-08 14:24:54 UTC  
> Updated at: 2017-07-09 18:40:03 UTC  
> Closed at: 2017-07-09 10:41:46 UTC  
> Url: https://github.com/boostorg/utility/pull/29  

Extend next_prior_test.cpp to demonstrate std::reverse_iterator with boost::next() and boost::prior().    
  
Motivated by [bug 13002](https://svn.boost.org/trac10/ticket/13002).

---

## Comment 1

> Username: mclow  
> Created_at: 2017-07-08 14:26:11 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313858776  

This looks good to me, but I'm going to wait for the CI build to complete.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-07-08 18:30:49 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313873375  

Wow, even `boost::next` cannot be used with `std::reverse_iterator` of BidirectionalIterator!  
  
It seems that `std::reverse_iterator` of BidirectionalIterator has `operator+, +=, -, -=` templated entities. (The base iterator is required to be RandomAccessIterator, when those templated entities get instantiated.) The current implementation of `boost::next/prior` dispatches based on operator availabilities of `+, +=, -, -=` using type traits. The `has_plus_operator` trait and friends get deceived and return true for `std::reverse_iterator` of BidirectionalIterator. Then `boost::next` tries to instantiate `operator+`, which leads to hard errors.  
  
Instead, `boost::next/prior` should dispatch based on iterator tags. Incidentally, Boost.Iterator have just implemented `boost::advance`, which is parameterized by Boost's IteratorTraversalCategory. So how about making (binary) `boost::next/prior` delegate to `boost::advance`  
```  
template <typename InputIterator>  
inline BOOST_CXX14_CONSTEXPR InputIterator  
next(InputIterator it, typename iterator_difference<InputIterator>::type n)  
{  
    return boost::advance(it, n);  
}  
  
template <typename BidirectionalIterator>  
inline BOOST_CXX14_CONSTEXPR BidirectionalIterator  
prior(BidirectionalIterator it, typename iterator_difference<BidirectionalIterator>::type n)  
{  
    return boost::advance(it, -n);  
}  
```  
?  
  
The downside of this change is that the Boost.Utility module becomes dependent on the Boost.Iterator module.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-08 18:37:42 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313873734  

> Instead, `boost::next/prior` should dispatch based on iterator tags.  
  
These functions are intended to be compatible with integers and integer-like user's types as well. Basically, they should be compatible with anything that provides arithmetic operators. Hence, iterator category cannot be used unless we are sure the type is an iterator. But we can't know that, so that's a dead end.

---

## Comment 4

> Username: morinmorin  
> Created_at: 2017-07-08 19:03:22 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313875008  

> These functions are intended to be compatible with integers and integer-like user's types as well.  
  
Oh, didn't know that. Thanks for pointing it out.  
  
> Hence, iterator category cannot be used unless we are sure the type is an iterator. But we can't know that, so that's a dead end.  
  
I think the following would be reasonable approximation:  
- For types with `iterator_category` nested type, dispatch based on iterator tags.   
- For other types (including pointers), dispatch based on operator availabilities.

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-07-08 19:27:00 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313876190  

> I think the following would be reasonable approximation:  
>   
>   * For types with |iterator_category| nested type, dispatch based on  
>     iterator tags.  
>   * For other types (including pointers), dispatch based on operator  
>     availabilities.  
  
Strictly speaking, iterators are not required to have   
`iterator_category` nested type, but I think this approach would work   
quite well in practice. Thanks for the suggestion. I'll see if I can   
come up with a patch.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-07-09 00:14:33 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313888855  

I've created #30, which includes reworked `next/prior` and the tests from this PR.

---

## Comment 7

> Username: Lastique  
> Created_at: 2017-07-09 10:41:46 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313912214  

Merged to develop as part of #30. Thanks.

---

## Comment 8

> Username: morinmorin  
> Created_at: 2017-07-09 13:47:38 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313920923  

Thanks for the fix, Andrey!  
  
Regards,  
Michel

---

## Comment 9

> Username: bminard  
> Created_at: 2017-07-09 18:40:03 UTC  
> Url: https://github.com/boostorg/utility/pull/29#issuecomment-313937515  

Thanks, Andrey!

---
