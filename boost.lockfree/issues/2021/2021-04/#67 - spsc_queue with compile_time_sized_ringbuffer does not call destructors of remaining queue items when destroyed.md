# #67 - spsc_queue with compile_time_sized_ringbuffer does not call destructors of remaining queue items when destroyed [Closed]

> Username: jwdevel  
> Created at: 2021-04-30 21:28:28 UTC  
> Updated at: 2021-05-01 07:25:06 UTC  
> Closed at: 2021-05-01 04:55:39 UTC  
> Url: https://github.com/boostorg/lockfree/issues/67  

Unlike `runtime_sized_ringbuffer`, `compile_time_sized_ringbuffer` does not have a custom destructor, and so it does not clean up any outstanding queue items when destroyed.  
  
I am not a lockfree guru, so I do not know if there is some hidden limitation that makes this impossible, or if it is simply a bug.  
I could not find any documentation for this behavior, so I suspect the latter.  
  
I can make  a PR for the proposed fix below, if it seems correct.  
  
Here is a small test that fails unexpectedly for me:  
  
```  
int g_numObjs = 0;  
  
struct Obj {  
	Obj() {  
		++g_numObjs;  
	}  
	Obj(const Obj&) {  
		++g_numObjs;  
	}  
	~Obj() {  
		--g_numObjs;  
	}  
};  
  
typedef boost::lockfree::spsc_queue<  
		Obj  
		// Note: commenting out this line makes it into a  
		// runtime_sized_ringbuffer, and the test passes.  
		,boost::lockfree::capacity<10>  
	> MyQueue;  
  
TEST_CASE("boost spsc bug") {  
	{  
		REQUIRE(g_numObjs == 0);  
		MyQueue queue;  
		queue.push(Obj());  
		REQUIRE(g_numObjs == 1);  
	} // queue gets destroyed  
  
	// FAILS  
	REQUIRE(g_numObjs == 0);  
}  
```  
  
If I add a destructor to `compile_time_sized_ringbuffer`, such as below (copied from `runtime_sized_ringbuffer`), then the test passes and everything seems fine:  
  
```  
~compile_time_sized_ringbuffer(void)  
{  
    // destroy all remaining items  
    T out;  
    while (pop(&out, 1)) {}  
}  
```  
  
Note: I suppose another implemenation might be to call `reset()` from inside `~spsc_queue()` itself, which would benefit from the optimization of not calling trivial destructors. Or maybe a similar optimization would be warranted in the destructors for `compile_time_sized...` and `runtime_sized_...` as well?

---

## Comment 1

> Username: jwdevel  
> Created at: 2021-04-30 23:48:23 UTC  
> Updated at: 2021-04-30 23:53:16 UTC  
> Url: https://github.com/boostorg/lockfree/issues/67#issuecomment-830463457  

Note: using the fix I wrote above does add the requirement that `T` must be default-constructable (this requirement already existed with `runtime_sized_ringbuffer`, it would seem). Perhaps better would be:  
  
```  
while (consume_one([](const T&){})) {}  
```  
  
(assuming lambdas are available)  
  
This same approach could be used for `runtime_sized_ringbuffer`, which might be nice both for consistency and because default-constructability it's a needless extra requirement.

---

## Comment 2

> Username: timblechmann  
> Created at: 2021-05-01 04:55:39 UTC  
> Url: https://github.com/boostorg/lockfree/issues/67#issuecomment-830538129  

afaict this one is a duplicate of #66

---

## Comment 3

> Username: jwdevel  
> Created at: 2021-05-01 07:07:03 UTC  
> Updated at: 2021-05-01 07:25:06 UTC  
> Url: https://github.com/boostorg/lockfree/issues/67#issuecomment-830568506  

Ah, so it is; sorry I missed that ( :  
That's what I get for not pulling latest first.  
  
If you're interested in a PR for a version that doesn't require T to be default constructable, I can send it (it's what I settled on locally), but it's small peanuts.
