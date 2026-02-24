# #207 Final Cleanup [Merged]

> Username: austin-beer  
> Created at: 2018-02-13 00:08:50 UTC  
> Updated at: 2018-02-14 16:59:09 UTC  
> Merged at: 2018-02-14 03:38:30 UTC  
> Closed at: 2018-02-14 03:38:30 UTC  
> Url: https://github.com/boostorg/thread/pull/207  



---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2018-02-13 17:25:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/207#pullrequestreview-96228762  

📁 include/boost/thread/detail/thread.hpp

```diff
 715 | #ifdef BOOST_THREAD_PLATFORM_PTHREAD
 721 |-     thread::id thread::get_id() const BOOST_NOEXCEPT
 716 |+     inline thread::id thread::get_id() const BOOST_NOEXCEPT
```

> Username: viboes  
> Created_at: 2018-02-13 17:25:40 UTC  
> Updated_at: 2018-02-13 17:26:09 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167939398  

There is no need to add inline in the definition. It is better (more portable?) to add it on the declarations AFAIK.

> Username: austin-beer  
> Created_at: 2018-02-13 17:29:44 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167940608  

According to this FAQ, it is better to place inline next to the definition instead of the declaration. Do you think this FAQ is correct, as far as you know? https://isocpp.org/wiki/faq/inline-functions#where-to-put-inline-keyword

> Username: viboes  
> Created_at: 2018-02-13 18:04:36 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167950878  

I was not aware of this. I agree that the code is more readable.  
As it is in the isocpp.org site, lets say that it should be portable. I've no time to do search.


---

## Comment 2

> Username: viboes  
> Created_at: 2018-02-13 17:32:44 UTC  
> Url: https://github.com/boostorg/thread/pull/207#issuecomment-365342351  

Thanks for working on this PR.  
I don't agree with all the inline changes. I believe that it is better to declare the function inline than defining it inline. I know that most compilers accept it, but I believe this is not portable.

---

## Review 3 [Changes requested]

> Username: viboes  
> Created_at: 2018-02-13 17:34:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/thread/pull/207#pullrequestreview-96229650  

I would like to merge this branch to the develop branch as soon as you have fix the inline issues.  
What do you think?

📁 include/boost/thread/pthread/thread_data.hpp

```diff
 266 |         template<typename TimeDuration>
 267 |-         void sleep(TimeDuration const& rel_time)
 267 |+         inline void sleep(TimeDuration const& rel_time)
```

> Username: viboes  
> Created_at: 2018-02-13 17:28:26 UTC  
> Updated_at: 2018-02-13 17:34:31 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167940208  

No need for inline here, it is a template

> Username: austin-beer  
> Created_at: 2018-02-13 18:00:11 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167949625  

I'm doing a little more research on the inline keyword as we speak. I want to make sure I understand it fully before backing out the inline changes.

> Username: austin-beer  
> Created_at: 2018-02-13 18:00:49 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167949812  

But I agree. I want to see this branch merged back into develop as soon as possible.

> Username: viboes  
> Created_at: 2018-02-13 18:05:44 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167951176  

I propose to merge it. We could improve the inline later if needed.  
Do you agree?

> Username: austin-beer  
> Created_at: 2018-02-13 18:06:58 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167951521  

Yes, I agree. We can merge now and improve later if need be.

> Username: austin-beer  
> Created_at: 2018-02-13 19:03:55 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r167967972  

Just an FYI...  
  
Unlike non-template functions, template functions that are defined in multiple translation units (i.e. in a header file) do *not* need to use the inline keyword to satisfy the One Definition Rule (which I'm sure you're well aware of). However, I haven't found any documentation online to indicate that these template functions are treated as inline from an *optimization* perspective. So I think that template function definitions that we want the compiler to inline to improve performance should still be explicitly marked as inline.

> Username: viboes  
> Created_at: 2018-02-14 03:38:05 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r168070619  

I believe the compiler is free to do whatever concerns with optimizations independently of whether we add inline or not. I believe it is as for the member functions defined directly on the declaration.  
  
The fact that I don't see this as an idiom in any library (including) let me think that it is not useful.   
Have you see some libraries using it?  
Have you see a case where adding inline has as consequence that the compiler inline the template and don't if not added?  
  
A very different thing could be to use some kind of FORCE_INLINE.  But I would prefer to not go this way, as it is very dependent on the compiler.

> Username: austin-beer  
> Created_at: 2018-02-14 16:58:55 UTC  
> Updated_at: 2018-02-14 16:58:56 UTC  
> Url: https://github.com/boostorg/thread/pull/207#discussion_r168241609  

Ok, you make a good point. No, I haven't seen this idiom anywhere else so I think you are correct that we shouldn't do it here either. I can remove it in a future PR after we merge back to develop.


---
