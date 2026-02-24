# #632 Various fixes [Merged]

> Username: jszuppe  
> Created at: 2016-07-16 21:42:32 UTC  
> Updated at: 2016-07-21 07:53:02 UTC  
> Merged at: 2016-07-21 05:16:03 UTC  
> Closed at: 2016-07-21 05:16:03 UTC  
> Url: https://github.com/boostorg/compute/pull/632  

This fixes 3 things:  
- `boost::compute::array<T>` was not working correctly with context different than `system::default_context()` since it was using `default_queue()` in its methods (so wild `Invalid context` errors could appear).  
- I added  
  
``` cpp  
template<class OtherAlloc>  
vector& operator=(const vector<T, OtherAlloc> &other)  
{  
}  
```  
  
for `boost::compute::vector<T, A>, so now something like this:  
  
``` cpp  
vector<int, some_allocator> b(context);  
vector<int, different_allocator> a(context);  
vector<int, some_allocator> c = a; // this works in `develop`  
a = b; // this does not work in `develop`, works now  
```  
  
works.  
- I changed several tests so in the future we can change `Context::context, Context::queue, Context::device` and tests will run using those context, queue and device, not the default context, queue and device.  
  
In October I will work more on Boost.Compute tests and try to somehow organize them and also increase coverage.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-07-16 22:07:49 UTC  
> Updated_at: 2016-07-16 22:11:31 UTC  
> Url: https://github.com/boostorg/compute/pull/632#issuecomment-233153504  

I added one more commit. https://github.com/boostorg/compute/pull/632/commits/4c21633bf9351afdd2e4d201dbcabda15ced0b61 fixes problem with copy ctor of `boost::compute::vector`which is causing `Invalid Context` error when context of the vector that is being copied is not default context.   
  
Examples:  
  
``` cpp  
vector<int> a(my_context); // non-default context  
// fill a with data  
vector<int> b = a; // this does not work in master/develop  
  
command_queue my_queue(my_context, my_device);  
vector<int> c(1, my_queue);   
vector<int> d = c; // this does not work in master/develop  
```

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-07-16 23:40:41 UTC  
> Url: https://github.com/boostorg/compute/pull/632#issuecomment-233156842  

[![Coverage Status](https://coveralls.io/builds/7038221/badge)](https://coveralls.io/builds/7038221)  
  
Coverage decreased (-0.003%) to 80.119% when pulling **4c21633bf9351afdd2e4d201dbcabda15ced0b61 on haahh:pr_various_test_fixes** into **d303097000552ae4e965b380d1c2954385e18205 on boostorg:develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-07-17 01:05:33 UTC  
> Url: https://github.com/boostorg/compute/pull/632#issuecomment-233159410  

[![Coverage Status](https://coveralls.io/builds/7038467/badge)](https://coveralls.io/builds/7038467)  
  
Coverage decreased (-0.003%) to 80.119% when pulling **4c21633bf9351afdd2e4d201dbcabda15ced0b61 on haahh:pr_various_test_fixes** into **d303097000552ae4e965b380d1c2954385e18205 on boostorg:develop**.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-07-21 05:16:02 UTC  
> Url: https://github.com/boostorg/compute/pull/632#issuecomment-234158934  

Looks good, thanks for all the fixes!

---
