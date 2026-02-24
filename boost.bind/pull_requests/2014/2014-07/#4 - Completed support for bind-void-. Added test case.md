# #4 Completed support for bind<void>. Added test case. [Merged]

> Username: K-ballo  
> Created at: 2014-07-12 23:27:15 UTC  
> Updated at: 2015-01-22 19:11:49 UTC  
> Merged at: 2015-01-22 19:11:49 UTC  
> Closed at: 2015-01-22 19:11:49 UTC  
> Url: https://github.com/boostorg/bind/pull/4  

While working on this I noticed that `bind<void>` did not work for member functions. Curiously, bind_mf2_cc introduces support for `bind(type<Rt2>, R (T::*f)(...), ...)` and there are tests for `bind(type<void>(), ...)`, but not for the equivalent `bind<void>(...)`. I introduced these additional overloads to bind_mf_cc (since they are not `type<>` based). I fear this might trigger some new overload resolution errors on old compilers.  
  
I composed a test out of pieces of other tests. I included a compilation test for data members, but I don't see a way to test any runtime behavior.  
  
I would like to add some notes to the documentation explicitly stating support for `bind<void>`, any suggestions?

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-07-13 16:22:11 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-48844933  

Why did you close it?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-07-13 17:07:36 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-48846206  

I intend to reopen it at a later time. I'd appreciate the feedback meanwhile.

---

## Comment 3

> Username: pdimov  
> Created_at: 2014-07-15 12:37:26 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-49025043  

It might be better if we separate the test that just checks for the already existing functionality from the member function changes.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-08-08 13:45:59 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-51602169  

Separated existing functionality test from the member function changes.

---

## Comment 5

> Username: pdimov  
> Created_at: 2014-08-21 11:36:47 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52908430  

What I had in mind was separate `bind_void_test` that only has `function_test` and `function_object_test` - this I could have merged immediately were it in its own pull request - and `bind_void_mf_test` along with the changes required to make it pass. But I can extract the former myself if you don't have time.

---

## Comment 6

> Username: K-ballo  
> Created_at: 2014-08-21 17:30:09 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52953886  

I'll take care of that. If I understand it correctly, it's just a matter of moving the fun-mem-ptr tests into it's own file.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2014-08-21 17:50:22 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52956571  

What should I do about the `member_data_test`? Put it in a `bind_void_dm_test`, leave it in `bind_void_mf_test`, or drop it?

---

## Comment 8

> Username: pdimov  
> Created_at: 2014-08-21 17:54:10 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52957095  

Good question. What is our use case for `bind<void>` for data members? Does it make sense for them?

---

## Comment 9

> Username: pdimov  
> Created_at: 2014-08-21 17:57:30 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52957554  

> I'll take care of that. If I understand it correctly, it's just a matter of moving the fun-mem-ptr tests into it's own file.  
  
I also would like the function-only changes in their own commit so that the rest can be reverted if necessary without affecting that part.

---

## Comment 10

> Username: K-ballo  
> Created_at: 2014-08-21 18:10:24 UTC  
> Url: https://github.com/boostorg/bind/pull/4#issuecomment-52959439  

> Good question. What is our use case for bind<void> for data members? Does it make sense for them?  
  
I don't generally `bind` data members, I don't have a use case for that. I guess since the functionality is in place already, consistency would be a motivation. The test doesn't test anything but that `bind<void>` compiles for them, though.  
  
> I also would like the function-only changes in their own commit so that the rest can be reverted if necessary without affecting that part.  
  
This was done already, the function-only tests are in its own commit. I'll clean some things up, but I'll stick to two separate commits.

---
