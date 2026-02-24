# #241 - FEATURE REQUEST: Add empty [Closed]

> Username: henryiii  
> Created at: 2019-10-23 20:31:30 UTC  
> Updated at: 2019-10-27 08:42:24 UTC  
> Closed at: 2019-10-27 08:42:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/241  

I would like an `empty(coverage)` function or method for histograms, something like this:  
  
```cpp  
bh::empty(hist, bh::coverage::all);  
// OR  
hist. empty(bh::coverage::all);  
```  
  
It could look something like this:  
  
```cpp  
template<class histogram_t>  
bool empty(const histogram_t &h, bh::coverage cov) {  
    using value_type = typename histogram_t::value_type;  
    value_type zero = value_type();  
    for(auto&& ind : bh::indexed(h, cov)) {  
        if(*ind != zero) {  
            return false;  
        }  
    }  
    return true;  
}  
```  
  
This should be faster than sum if the histogram is filled, and would be useful for checking to see if an item fell into the histogram (inner), or for checking to see if this was filled yet (all).

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-24 14:45:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/241#issuecomment-545953114  

Sorry, but I think this is not good to have in boost::histogram, because the expectation in C++ is that `.empty()` is a fast O(1) operation. Here it is a very slow operation. The style of the stdlib is to not offer methods at all which have bad performance. Think of std::vector, which does not have `push_front`.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-10-24 14:47:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/241#issuecomment-545953836  

Ah wait, on second glance, this is a free function not a method, so it would not create the conflict in expectation that I was mentioning. Yes, we can definitely have that as a free function.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-10-24 14:48:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/241#issuecomment-545954511  

If you want to add it, be my guest, but I can also do it.

---

## Comment 4

> Username: henryiii  
> Created at: 2019-10-24 14:50:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/241#issuecomment-545955222  

Okay, I'll add it in a PR in a day or two then you can improve it. :)
