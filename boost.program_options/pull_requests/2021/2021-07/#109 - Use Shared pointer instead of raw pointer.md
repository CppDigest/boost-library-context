# #109 Use Shared pointer instead of raw pointer [Open]

> Username: camerbam  
> Created at: 2021-07-01 15:16:15 UTC  
> Updated at: 2021-11-03 16:08:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/109  

Update value_semantic to return a boost shared pointer instead of a raw pointer  
  
This will return a managed pointer to the user instead of an unmanaged pointer.  
This helps static code analyzers from flagged false positive memory leaks  
It is becoming harder for projects to use program_options because some customers require certain static code analyzers. By returning a managed pointer, those projects can continue to use this amazing library. The certain static code analyzer that we are required to use flags this. Because the pointer is being declared in a hpp and being stored into a shared pointer in a cpp, static code analyzers wouldn't be able to see it. I have at least one more static code analyzer results coming (may end up getting two more for a total of three) to see if those static code analyzers also flag this.  
  
I agree that the code with raw pointers is correct, and that we shouldn't degrade our code so that static code analyzers can understand them. I took a stab at trying to update the library to return a shared pointer instead of a raw pointer, and I don't feel like it has made the code worst. I figure at the very least if you don't want this change, you could tell me why using the shared pointers is a bad idea. I also figure that other projects are running into this.  
  
I am sorry if this pull_request is very sloppy. This is my first time making a pull to an open source project... But I hope this to be a good learning experience so that one day I can actually help open source projects.

---

## Review 1 [Commented]

> Username: camerbam  
> Created_at: 2021-07-01 19:23:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/109#pullrequestreview-697372750  

📁 include/boost/program_options/detail/value_semantic.hpp

```diff
   7 | // ../value_semantic.hpp.
   8 | 
   9 |+ #include <boost/smart_ptr/make_shared.hpp>
```

> Username: camerbam  
> Created_at: 2021-07-01 15:17:41 UTC  
> Updated_at: 2021-07-01 19:23:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/109#discussion_r662381972  

This is a downside to using the shared pointers instead of raw pointers. If you include program_options, it will include make_shared (and later on in this pull request enable_shared_from_this)


📁 include/boost/program_options/options_description.hpp

```diff
 170 |+         options_description_easy_init&
 171 |+         operator()(const char* name,
 172 |+                    shared_ptr<const value_semantic> s);
```

> Username: camerbam  
> Created_at: 2021-07-01 15:19:50 UTC  
> Updated_at: 2021-07-01 19:23:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/109#discussion_r662383783  

I left the functions that used const value_semantic* s for backwards compatibility. The last thing I want to do is break someone's code that depends on it.


📁 test/options_description_test.cpp

```diff
 317 | }
 318 | 
 319 |+ void test_backwards_compatibility_with_raw_pointer()
```

> Username: camerbam  
> Created_at: 2021-07-01 19:18:39 UTC  
> Updated_at: 2021-07-01 19:23:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/109#discussion_r662536009  

I thought it was a good idea to add a test case to test the backwards compatibility.


---

## Comment 2

> Username: camerbam  
> Created_at: 2021-07-01 21:28:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/109#issuecomment-872562264  

Looking at the checks, I see that four of them are failing, but I don't know why. It looks like the first two are failing because it is failing to download something. The second two look like they are failing because putenv is not declared. Looking at past pulls that have been merged, they also have the same findings, which makes me think I didn't break them in this pull. I was going to try and fix them but I read in the boost documentation to avoid drive by fixes... so I held off on that for now...

---

## Comment 3

> Username: camerbam  
> Created_at: 2021-11-03 16:08:03 UTC  
> Url: https://github.com/boostorg/program_options/pull/109#issuecomment-959581002  

At this point, the only other static code analysis tool I have been able to use is cppcheck, and it does not have an issue with this because it doesn't look into the header files that you include. I have been unable to get another static code analyzer yet. I am curious though what your thoughts are on this change?

---
