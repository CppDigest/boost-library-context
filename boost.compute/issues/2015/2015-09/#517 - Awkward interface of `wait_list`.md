# #517 - Awkward interface of `wait_list` [Closed]

> Username: pisto  
> Created at: 2015-09-23 17:14:35 UTC  
> Updated at: 2017-04-17 09:12:56 UTC  
> Closed at: 2017-04-17 09:12:56 UTC  
> Url: https://github.com/boostorg/compute/issues/517  

The current interface of `wait_list` makes is in my opinion very awkward. Specifically, it lacks an `operator[]`. As of now, the only way to merge two `wait_list`s is the following:  
  
```  
using namespace boost::compute;  
wait_list merge_wait_lists(const wait_list& a, const wait_list& b){  
  auto a_native = a.get_event_ptr();    //cannot iterate without getting the raw native array  
  wait_list c = b;  
  for(unsigned int i = 0; i < a.size(); i++) c.insert(event(a_native[i]));   //the explicit cast is necessary  
  return c;  
}  
```  
  
Also, `wait_list::wait()` is non-const, and I don't think that that makes sense.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-09-24 04:43:17 UTC  
> Url: https://github.com/boostorg/compute/issues/517#issuecomment-142810568  

Yeah, those both make sense. I'll add the `operator[]` and make `wait_list::wait()` const. Thanks for reporting this!

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-09-24 04:50:00 UTC  
> Url: https://github.com/boostorg/compute/issues/517#issuecomment-142811247  

PR #518 changes `event::wait()` and `wait_list::wait()` to both be `const`.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-04-17 09:12:56 UTC  
> Url: https://github.com/boostorg/compute/issues/517#issuecomment-294437376  

Fixed by https://github.com/boostorg/compute/pull/577.
