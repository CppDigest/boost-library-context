# #1 - Strange pull_coroutine iterators [Closed]

> Username: LEW21  
> Created at: 2013-12-18 00:15:39 UTC  
> Updated at: 2013-12-19 08:47:54 UTC  
> Closed at: 2013-12-19 08:47:54 UTC  
> Url: https://github.com/boostorg/coroutine/issues/1  

Iterators of pull coroutines don't work in the way iterators are expected to work. This test works for vector, and fails for the coroutine (check *it1 == *it2 failed [2 != 3]).  
  
``` c++  
void test_pull_coroutine_iterator_copy()  
{  
    {  
        auto range = std::vector<int>{1, 2, 3};  
  
        auto it1 = std::begin(range);  
        auto it2 = it1;  
        ++it1;  
        ++it2;  
        BOOST_CHECK_EQUAL( *it1, *it2 );  
    }  
  
    {  
        auto range = coro::coroutine<int>::pull_type  
        {[](coro::coroutine<int>::push_type& yield){  
            yield(1); yield(2); yield(3);  
        }};  
  
        auto it1 = std::begin(range);  
        auto it2 = it1;  
        ++it1;  
        ++it2;  
        BOOST_CHECK_EQUAL( *it1, *it2 );  
    }  
}  
```  
  
I think that the correct way to fix it would be to make iterators move-only - as it's impossible to correctly support copying iterators because the coroutine is not copyable.  
  
But there is one problem - BOOST_FOREACH currently requires copyable iterators. Which I fixed - https://github.com/boostorg/foreach/pull/1

---

## Comment 1

> Username: LEW21  
> Created at: 2013-12-18 00:28:26 UTC  
> Url: https://github.com/boostorg/coroutine/issues/1#issuecomment-30805903  

I've done it on my C++11-only fork, and it works: https://github.com/LEW21/coroutine/commit/0b6a4dc5b716c17671fba7ea9d5c74bbd0ca4edc  
  
I can port it to the non-C++11 version if you want to make this change.

---

## Comment 2

> Username: olk  
> Created at: 2013-12-18 06:54:44 UTC  
> Url: https://github.com/boostorg/coroutine/issues/1#issuecomment-30820835  

yes, because coroutines are moveable-only, copying iterators would not give the expected results.  
if your fix for boosts's foreach is merged I would be happy if I could merge your fix for coroutines (C++11 and non-C++11) too.
