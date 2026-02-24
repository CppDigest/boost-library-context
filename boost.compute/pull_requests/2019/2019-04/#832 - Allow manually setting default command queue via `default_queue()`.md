# #832 Allow manually setting default command queue via `default_queue()` [Merged]

> Username: rosenrodt  
> Created at: 2019-04-21 12:40:51 UTC  
> Updated at: 2019-05-12 12:24:28 UTC  
> Merged at: 2019-05-12 09:22:50 UTC  
> Closed at: 2019-05-12 09:22:50 UTC  
> Url: https://github.com/boostorg/compute/pull/832  

By doing so, the associated default device and context will automatically match the user-provided command queue. It  
  
Guarantees unique default queue/device/context is returned when calling from multiple threads (requires config macro BOOST_COMPUTE_THREAD_SAFE)  
  
Resolves issue #827

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-04-21 14:11:14 UTC  
> Updated_at: 2019-05-09 13:43:50 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-485254837  

[![Coverage Status](https://coveralls.io/builds/23285279/badge)](https://coveralls.io/builds/23285279)  
  
Coverage increased (+0.2%) to 84.226% when pulling **2583e312146fdfa8169ee1918dc4ab12cb0e3e9d on rosenrodt:pr-set-default-command-queue** into **5a29a4d0a1ad8adf9cc19a115252930a628d8b48 on boostorg:develop**.

---

## Comment 2

> Username: rosenrodt  
> Created_at: 2019-04-28 06:21:03 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-487348649  

@jszuppe any comment on it? 😃

---

## Review 3 [Commented]

> Username: jszuppe  
> Created_at: 2019-05-05 08:28:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/832#pullrequestreview-233766556  

📁 test/test_system.cpp

```diff
  42 |+         compute::system::default_queue(&queue1);
  43 |+         compute::command_queue default_queue = compute::system::default_queue();
  44 |+         BOOST_ASSERT(queue1 == default_queue);
```

> Username: jszuppe  
> Created_at: 2019-05-05 07:52:30 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281009324  

This should be `BOOST_CHECK`

> Username: jszuppe  
> Created_at: 2019-05-05 07:53:00 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281009346  

or `BOOST_CHECK_EQUAL`

> Username: rosenrodt  
> Created_at: 2019-05-07 16:30:55 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281719022  

Agreed. Will revise soon this week

---

📁 test/test_system.cpp

```diff
  44 |+         BOOST_ASSERT(queue1 == default_queue);
  45 |+     }
  46 |+ #ifdef NDEBUG
```

> Username: jszuppe  
> Created_at: 2019-05-05 07:52:45 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281009334  

Why is this required?

> Username: jszuppe  
> Created_at: 2019-05-05 08:20:27 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010287  

Aaa, ok, I guess we don't want to have asserts.

> Username: rosenrodt  
> Created_at: 2019-05-07 16:31:09 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281719120  

Yep :)


📁 include/boost/compute/system.hpp

```diff
 380 |+         {
 381 |+             // Try invoking default_queue() before anything else
 382 |+             BOOST_THROW_EXCEPTION(context_error(&user_context,
```

> Username: jszuppe  
> Created_at: 2019-05-05 08:03:46 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281009789  

I'm not sure if `context_error` is right error here.  I think it should be custom exception similar in design to `no_device_found`.

> Username: rosenrodt  
> Created_at: 2019-05-07 16:29:57 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281718653  

Glad you pointed that out. The original design was to set user-provided context so it sort of fits in. But now it's been user-provided command queue already. I think we can throw a more explicit exception `set_default_queue_error`

> Username: jszuppe  
> Created_at: 2019-05-07 19:37:04 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281798299  

The name of the error sounds ok.

---

📁 include/boost/compute/system.hpp

```diff
 172 |+     /// The default queue is created once on the first time this function is
 173 |+     /// called. Calling this function multiple times will always result in the
 174 |+     /// same command queue object being returned.
```

> Username: jszuppe  
> Created_at: 2019-05-05 08:14:20 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010130  

I would add information about potential exception if default context and.or default device don't match with device and context of `user_queue`.

> Username: jszuppe  
> Created_at: 2019-05-05 08:14:32 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010134  

You can point to the test you created.

> Username: rosenrodt  
> Created_at: 2019-05-07 16:31:43 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281719341  

Agreed. Will revise soon this week

---

📁 include/boost/compute/system.hpp

```diff
 173 |+     /// called. Calling this function multiple times will always result in the
 174 |+     /// same command queue object being returned.
 175 |+     static command_queue& default_queue(command_queue* user_queue = 0)
```

> Username: jszuppe  
> Created_at: 2019-05-05 08:28:11 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010545  

Shouldn't `user_queue` be `const`? Also have we/you considered using constant references instead of pointers?

> Username: jszuppe  
> Created_at: 2019-05-05 08:28:38 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010566  

This also applies to other functions which take pointer to queue/device/context.

> Username: rosenrodt  
> Created_at: 2019-05-07 16:41:13 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281722794  

I agree it should be made `const`. As for constant reference, I don't think it's possible because the intent is to double `default_queue()` as both means to get/set the default queue. And we don't have things like null reference.   
  
That leaves us with two options:  
1. We spin out the "set" function which takes const reference  
2. Keep `default_queue()` and make the input pass-by-value, with default value set to be a null command queue

> Username: jszuppe  
> Created_at: 2019-05-07 19:35:43 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281797843  

https://github.com/boostorg/compute/blob/master/include/boost/compute/command_queue.hpp#L108 This ctor creates "null" command queue. It should work with constant reference.

> Username: rosenrodt  
> Created_at: 2019-05-08 17:00:09 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r282158323  

Yeah that should work. Didn't figure that out myself


📁 test/test_attach_user_queue_error.cpp

```diff
  29 |+     compute::system::default_device(); 
  30 |+ 
  31 |+     try
```

> Username: jszuppe  
> Created_at: 2019-05-05 08:15:15 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281010150  

Use `BOOST_CHECK_THROW`

> Username: rosenrodt  
> Created_at: 2019-05-07 16:31:50 UTC  
> Updated_at: 2019-05-09 08:09:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#discussion_r281719377  

Agreed


---

## Comment 4

> Username: jszuppe  
> Created_at: 2019-05-05 08:30:04 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-489403274  

I'm really sorry for delay. I had a busy week and then a few days of holidays without PC.

---

## Comment 5

> Username: rosenrodt  
> Created_at: 2019-05-07 16:44:22 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-490158664  

@jszuppe Glad to hear you back from your holiday. I have left some reply under your comments and I am eager to start working on the feature again

---

## Comment 6

> Username: rosenrodt  
> Created_at: 2019-05-09 08:18:52 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-490805708  

I just force-pushed a one line fix for test_attach_user_queue_error.cpp due to CI check failure  
  
```c++  
compute::device user_device = compute::system::devices().front();  
compute::context user_context(user_device);  
compute::command_queue user_queue(user_context, user_device);  
  
//compute::system::default_device();  
compute::system::default_context();   
// ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
// purposefully set the default context that is incompatible with user-provided command queue  
  
BOOST_CHECK_THROW(  
    compute::system::default_queue(user_queue),  
    compute::set_default_queue_error);  
```

---

## Comment 7

> Username: jszuppe  
> Created_at: 2019-05-12 09:22:41 UTC  
> Url: https://github.com/boostorg/compute/pull/832#issuecomment-491579344  

Fails in CI are unrelated to the PR. Merging. Good job!

---
