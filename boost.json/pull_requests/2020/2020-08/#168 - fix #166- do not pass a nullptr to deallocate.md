# #168 fix #166: do not pass a nullptr to deallocate [Closed]

> Username: pauldreik  
> Created at: 2020-08-18 18:18:35 UTC  
> Updated at: 2020-08-18 21:37:03 UTC  
> Closed at: 2020-08-18 21:37:03 UTC  
> Url: https://github.com/boostorg/json/pull/168  

Closes #166

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-18 18:39:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/168#pullrequestreview-469725130  

📁 include/boost/json/detail/impl/stack.ipp

```diff
  38 |-     sp_->deallocate(buf_, size_);
  38 |+     if(buf_)
  39 |+         sp_->deallocate(buf_, cap_);
```

> Username: vinniefalco  
> Created_at: 2020-08-18 18:39:01 UTC  
> Updated_at: 2020-08-18 19:23:36 UTC  
> Url: https://github.com/boostorg/json/pull/168#discussion_r472403399  

Consider folding the condition?  
```  
    if( buf_ )  
    {  
        if( size_ > 0 )  
            std::memcpy( buf, buf_, size_ );  
        sp_->deallocate( buf_, cap_ );  
    }  
```

> Username: pauldreik  
> Created_at: 2020-08-18 19:11:02 UTC  
> Updated_at: 2020-08-18 19:23:36 UTC  
> Url: https://github.com/boostorg/json/pull/168#discussion_r472420425  

Or even skip the check for buf_, replacing it with the size check and an assert on buf_?

> Username: pauldreik  
> Created_at: 2020-08-18 19:12:05 UTC  
> Updated_at: 2020-08-18 19:23:36 UTC  
> Url: https://github.com/boostorg/json/pull/168#discussion_r472420948  

The idea being that if size is >0, buf_ should definitely be nonnull or else something has gone haywire somewhere else.

> Username: vinniefalco  
> Created_at: 2020-08-18 19:15:40 UTC  
> Updated_at: 2020-08-18 19:23:36 UTC  
> Url: https://github.com/boostorg/json/pull/168#discussion_r472422839  

That may be possible, but you would have to look at the entire class to know if that precondition is correct. If you do that, use `BOOST_ASSERT`. However, just a guess but it might not be possible, as `size_` can be zero while `buf_` is nonzero. This is the case when the stack grows, and then everything is popped off, leaving it with a non-zero capacity but a zero size.

> Username: pauldreik  
> Created_at: 2020-08-18 19:19:13 UTC  
> Updated_at: 2020-08-18 19:23:36 UTC  
> Url: https://github.com/boostorg/json/pull/168#discussion_r472424635  

I will go for the folded condition :-)


---

## Comment 2

> Username: pauldreik  
> Created_at: 2020-08-18 19:27:52 UTC  
> Url: https://github.com/boostorg/json/pull/168#issuecomment-675669410  

A thought on the size check. I guess it is there to avoid the expense of unnecessarily calling the memcpy function. But how common is that case with zero size and nonzero buffer? The check introduces a branch, which might be bad for performance.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-08-18 19:36:41 UTC  
> Url: https://github.com/boostorg/json/pull/168#issuecomment-675673172  

> how common is that case with zero size and nonzero buffer  
  
Very common actually. It happens every time a parser is re-used after retrieving a value. I'm okay with removing the check for `size_` though, as `memcpy` is safe to call with a zero size. Up to you.

---

## Comment 4

> Username: pauldreik  
> Created_at: 2020-08-18 20:23:58 UTC  
> Url: https://github.com/boostorg/json/pull/168#issuecomment-675694036  

I think the check should remain in that case. Either way, I wouldn't  
remove it without benchmarking first. So please go ahead and merge this  
fix if you are happy with it.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-18 20:46:01 UTC  
> Url: https://github.com/boostorg/json/pull/168#issuecomment-675711530  

I think it is good thanks

---
