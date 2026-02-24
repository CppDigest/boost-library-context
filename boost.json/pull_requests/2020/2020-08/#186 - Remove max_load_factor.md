# #186 Remove max_load_factor [Closed]

> Username: sdkrystian  
> Created at: 2020-08-22 15:20:21 UTC  
> Updated at: 2020-08-22 21:53:58 UTC  
> Closed at: 2020-08-22 21:53:58 UTC  
> Url: https://github.com/boostorg/json/pull/186  

close #150

---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-08-22 17:36:58 UTC  
> Url: https://github.com/boostorg/json/pull/186#issuecomment-678669599  

Fuzzing failure is because of a UTF-8 validation issue fixed by #185

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-08-22 18:55:35 UTC  
> Url: https://github.com/boostorg/json/pull/186#issuecomment-678678269  

The commit message should read "max_load_factor is always 1" since this says basically the same thing and is more informative.

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 18:56:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/186#pullrequestreview-472945072  

📁 include/boost/json/detail/impl/object_impl.ipp

```diff
  41 |+         capacity *
  42 |+         (sizeof(value_type) + 
  43 |+         sizeof(index_t));
```

> Username: vinniefalco  
> Created_at: 2020-08-22 18:56:30 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475119952  

This is confusing, prefer:  
```  
    const auto n =   
        sizeof(table) +   
        capacity * (  
            sizeof(value_type) + sizeof(index_t));  
```

> Username: vinniefalco  
> Created_at: 2020-08-22 18:56:54 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475119989  

Or:  
```  
    const auto n =   
        sizeof(table) +   
        capacity * (  
            sizeof(value_type) +  
            sizeof(index_t));  
```

> Username: sdkrystian  
> Created_at: 2020-08-22 20:52:30 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475137357  

I'll indent but I prefer to keep parentheses grouped with the expression they enclose

> Username: vinniefalco  
> Created_at: 2020-08-22 21:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475139464  

The reason I leave behind the opening parenthesis on the previous line is to help the reader understand that the following line is a continuation

> Username: sdkrystian  
> Created_at: 2020-08-22 21:31:18 UTC  
> Updated_at: 2020-08-22 21:31:19 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475140218  

Yeah, while changing that it seems to be that way. Change it again

> Username: sdkrystian  
> Created_at: 2020-08-22 21:31:38 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475140244  

Ah, nevermind. It seems you cherry-picked it


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 19:02:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/186#pullrequestreview-472945474  

📁 include/boost/json/impl/object.ipp

```diff
 506 |-         std::ceil(new_buckets * max_load_factor()));
 485 |+     const unsigned long long* prime = 
 486 |+         +object_impl::bucket_sizes();
```

> Username: vinniefalco  
> Created_at: 2020-08-22 19:02:58 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475120558  

Why the plus sign? I dislike this

> Username: sdkrystian  
> Created_at: 2020-08-22 20:52:55 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475137401  

I'll remove that


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 19:05:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/186#pullrequestreview-472945619  

📁 include/boost/json/impl/object.ipp

```diff
 485 |+     const unsigned long long* prime = 
 486 |+         +object_impl::bucket_sizes();
 487 |+     for(; new_capacity > *prime; ++prime);
```

> Username: vinniefalco  
> Created_at: 2020-08-22 19:05:32 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475120808  

No I don't like this loop at all, it has an empty body. Use:  
```  
while(new_capacity > *prime)  
    ++prime;  
```

> Username: vinniefalco  
> Created_at: 2020-08-22 19:05:53 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475120836  

Why did you replace the call to `lower_bound` with a linear search?

> Username: vinniefalco  
> Created_at: 2020-08-22 20:32:03 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475135770  

hmm... maybe linear is better, since it is an increasing function

> Username: sdkrystian  
> Created_at: 2020-08-22 20:53:40 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475137436  

Yup, that's exactly why


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 19:18:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/186#pullrequestreview-472946384  

📁 include/boost/json/detail/object_impl.hpp

```diff
 180 |-         BOOST_ASSERT(tab_);
 181 |-         return bucket_sizes()[tab_->prime_index];
 179 |+         return capacity();
```

> Username: vinniefalco  
> Created_at: 2020-08-22 19:18:33 UTC  
> Updated_at: 2020-08-22 21:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475121927  

are we even using this function anymore

> Username: sdkrystian  
> Created_at: 2020-08-22 20:54:16 UTC  
> Updated_at: 2020-08-22 21:31:50 UTC  
> Url: https://github.com/boostorg/json/pull/186#discussion_r475137473  

We are (in like 2 places. You said to keep it, so it's here :)


---
