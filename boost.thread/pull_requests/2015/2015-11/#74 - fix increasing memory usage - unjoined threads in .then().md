# #74 fix increasing memory usage / unjoined threads in .then() [Merged]

> Username: tvbuehler  
> Created at: 2015-11-06 12:44:49 UTC  
> Updated at: 2015-11-14 07:37:25 UTC  
> Merged at: 2015-11-14 07:37:25 UTC  
> Closed at: 2015-11-14 07:37:25 UTC  
> Url: https://github.com/boostorg/thread/pull/74  

- in a long list of .then().then()...then() calls the most outer future  
  kept all parents (including threads) alive; for future<void> and  
  launch::async this also leaks the threads as usually you wouldn't call  
  wait() on them  
- don't keep parent future around after continuation was run  
- drop centinel - similar to parent it just keeps the parent state alive;  
  889c178173ae27515d216553b5d3e5a610641958 doesn't mention how this  
  centinel would fix any problem.

---

## Comment 1

> Username: tvbuehler  
> Created_at: 2015-11-09 07:24:06 UTC  
> Updated_at: 2015-11-12 08:18:12 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-154978142  

I used this on debian testing (~~found a bug with unwrap too~~ - this seems to be fixed in git); I set a debugger breakpoint after the `.then` calls and took a look at the future state (also we have leaking thread ids on windows when we keep the last future alive for a long time):  
  
``` c++  
#define BOOST_THREAD_PROVIDES_FUTURE_CONTINUATION  
#define BOOST_THREAD_PROVIDES_FUTURE_UNWRAP  
  
#include "future.hpp"  
  
#include <iostream>  
  
typedef boost::unique_future<int> F;  
  
typedef int (*Step)(F);  
  
int step1(F const&) {  
    std::cout << "step 1\n";  
    return 1;  
}  
  
int step2(F const&) {  
    std::cout << "step 2\n";  
    return 2;  
}  
  
int main() {  
    F x = boost::make_ready_future(0);  
    x = x.then(boost::launch::async, (Step) step1);  
    x = x.then(boost::launch::async, (Step) step2);  
    //x = x.then(boost::launch::async, [](F) {  
    //  std::cout << "step 3\n";  
    //  return boost::make_ready_future(3);  
    //});  
  
    x.wait();  
    std::cout << x.get() << "\n";  
  
    return 0;  
}  
```  
  
Makefile:  
  
``` Makefile  
override LDFLAGS=-lboost_thread -lboost_system  
override CXXFLAGS=-g -std=c++11  
  
all: test-future  
.PHONY: all  
  
test-future: test-future.cpp  
  
clean:  
    rm -f test-future  
  
.PHONY: clean  
```

---

## Comment 2

> Username: viboes  
> Created_at: 2015-11-09 18:43:26 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-155151495  

I don't see why do you say that there is a leak yet. It is normal that the future thread is not released as we join the thread at future destructor. I'm a branch where the future resulting from a then doesn't blocks, but is is not yet stable.    
  
For the unwrap issue, I didn't wanted to be able to do that  
  
```  
    x = x.then(boost::launch::async, (Step) step1);  
```  
  
The implicit unwrapping was not intended. It should be explicit :(  
  
The wait issue is yet there or was it already fixed?  
  
```  
// x.wait(); /* waiting on a unwrapped future destroys internal state, breaking .get() */  
```

---

## Comment 3

> Username: tvbuehler  
> Created_at: 2015-11-10 08:38:09 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-155358446  

Regarding `unwrap`: I'm only testing with debian where the bug is still present. Looking at git HEAD the bug should be fixed. So let's ignore this for now.  
  
Back to the main issue: It isn't a "classic" memory leak as in there being no reference to the data anymore, but it keeps more resources than it needs and a user would expect.  
  
As long as I keep the outer future returned by the last `then()` call alive, it will keep _all_ intermediate futures (and inner states) alive. If I call `wait()` on this future, it will just join the thread for the most outer continuation, not for the inner continuations (even if they are already finished).  
  
If I keep this future alive in a long living state and keep adding continuations with `x = x.then(...)` it will grow further on every call and never release the resources until I free the state completely.

---

## Comment 4

> Username: viboes  
> Created_at: 2015-11-10 18:21:45 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-155521496  

Ok, I see the waste. As soon as the continuation is launched, the parent future could be released.  
  
Thanks for catching this issue.

---

## Comment 5

> Username: tvbuehler  
> Created_at: 2015-11-11 11:21:25 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-155739661  

A boost::move does NOT guarantee to trigger a move operation (it is only a static_cast!), so you MUST reset it manually (pass `F` by `const &` in my example for testing).  
  
Not giving any credit in the commit (not even linking the pull request) is pretty lame too, and makes sure no one will understand your commit in the future - as happens right now with the sentinel, which I'm still convinced is absolutely useless as it is already part of the parent!  
  
Also why do you commit commented code (without explanations even)? It doesn't make any sense.  
  
I've got the feeling you're just throwing patches without deeper understanding at the code trying to fix random errors, and that is not what using "high-quality libraries" (quoting http://www.boost.org/users/) should feel like; I know that the specific features we're discussing here are marked experimental, but it still is all in the same file.

---

## Comment 6

> Username: viboes  
> Created_at: 2015-11-11 21:47:10 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-155919894  

I try to do my best, even if you think the contrary. I have see that I can release the sentinel in one case. and I have applied it. What is wrong with this approach?  
  
boost::move doesn't do the move it self. It is the move assignment that do the work. If you declare the continuation having a future<T> as parameter the future will be moved.  
  
Maybe the commented code has no sense for you, it has for me.  
  
If you want to participate to the maintenance of the library you are welcome, there is a lot to do.If you find a solution to remove the sentinel or whatever will improve the library I will apply the PR. No problem.  
  
Sorry for not referencing this issue on the commit. I will try to think about that in the future.  
  
Please open as many issues as you think are needed to improve the library and of course any PR that works is welcome.

---

## Comment 7

> Username: tvbuehler  
> Created_at: 2015-11-12 08:59:24 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-156037708  

There is nothing wrong with releasing the sentinel, but having the sentinel in the first place is wrong as far as I can see.  
  
I changed the signature of my continuations to take `F const &`, so they still take the future as parameter, but it certainly won't be moved (the `F&&` returned by `boost::move` is implicitly converted to `F const&`, both values are simple pointers on the low level), so you really need to explicitly release the parent; either clear it with an assignment or move it to a local variable like you did in `future_deferred_continuation_shared_state::execute`.  
  
Even if `//this->sentinel.reset();` makes sense to you right now (which I still doubt), I don't think you will know it some months from now - same as you don't remember what the sentinel was for in the first place.

---

## Comment 8

> Username: viboes  
> Created_at: 2015-11-12 11:59:18 UTC  
> Updated_at: 2015-11-12 12:03:04 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-156085140  

The continuation future parameter shouldn't be a reference. I must see if I can enforce this check. The future must be moved to the continuation. It is the continuation that must be the owner.  
  
Well, for the sentinel, you can see why it is there. Just remove it and run the regression test. This is what I did, and I reached only to limit the scope.   
  
Ah, BTW, I know that this is only a workaround, and that must be fixed. The commit I did, is not a final solution, but I hope that it helps.

---

## Comment 9

> Username: tvbuehler  
> Created_at: 2015-11-13 14:49:24 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-156451959  

> The continuation future parameter shouldn't be a reference. I must see if I can enforce this check. The future must be moved to the continuation. It is the continuation that must be the owner.  
  
I know of nothing in the standard library or anywhere else that would require a callback to take a parameter by value, and I think it is a bad idea to try to enforce this, and I see no valid reason for your "It is the continuation that must be the owner." claim.  
  
> Well, for the sentinel, you can see why it is there. Just remove it and run the regression test. This is what I did, and I reached only to limit the scope.   
  
Took me a while to figure how to run the unit tests. I guess if it doesn't complain in the end everything went well? It ends with: `...updated 2370 targets...` (running `../../../b2` in `boost/libs/thread/test`).  
  
Anyway, I found the real bug the sentinel workaround was made for and fixed it (4ba8415); the sentinel was basically hiding a real bug instead of actually fixing it.

---

## Comment 10

> Username: viboes  
> Created_at: 2015-11-13 20:41:07 UTC  
> Updated_at: 2015-11-13 20:50:15 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-156550618  

You are right and you have a fix. Marvelous.  
I have check it and it works.

---

## Comment 11

> Username: viboes  
> Created_at: 2015-11-13 22:19:18 UTC  
> Url: https://github.com/boostorg/thread/pull/74#issuecomment-156574890  

This is the best PR I have never received.   
Thank you very much.

---
