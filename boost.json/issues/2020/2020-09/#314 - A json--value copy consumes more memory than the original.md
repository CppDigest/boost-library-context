# #314 - A json::value copy consumes more memory than the original [Closed]

> Username: hadrielk  
> Created at: 2020-09-11 13:30:36 UTC  
> Updated at: 2020-09-14 02:09:18 UTC  
> Closed at: 2020-09-14 02:09:18 UTC  
> Url: https://github.com/boostorg/json/issues/314  

I loaded a large JSON document into a `json::value` via `json::parse()`; the `json::value` consumed ~300MB. I then copied it via the `operator=()` copy assignment operator (ie., `json::value clone = original;`). The cloned copy consumes 400MB.  
  
Compiler: gcc 7.3, with its libstdc++  
Platform: Intel x64, CentOS  
Boost lib: latest  
Json-lib git hash: 07214ad235b0ea43a1ef49a11ee3807d8198cebe

---

## Comment 1

> Username: hadrielk  
> Created at: 2020-09-11 14:02:36 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691113707  

Additional info: I used `tcmalloc-debug` to profile it.  
  
I can provide the large JSON document if you need it, though there's nothing unusual about it other than size, as far as I can tell.  
  
I've been comparing this `boost::json` library vs. Facebook's [Folly library](https://github.com/facebook/folly) `folly::dynamic`, which did not exhibit this behavior. (though `boost::json` has much better parsing/serialization performance and overall memory footprint, so far in my tests 🥇)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-11 14:06:31 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691115889  

Well, I think it can be due to just one thing. A difference in how an array, object, or string is allocated when a copy is made, versus when it is parsed. The code paths are slightly different. If you could figure out which one of these types has the difference, that would be helpful. In the meanwhile, I am inspecting the code.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-11 14:50:22 UTC  
> Updated at: 2020-09-11 14:54:23 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691141392  

I wrote a quick test to see if parsed strings, arrays, and objects require differing amounts of memory compared to making a copy, and the tests pass: https://github.com/vinniefalco/json/commit/ed921691f2752d4c57147715f875802a3f28e82e

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-11 14:55:54 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691144523  

If you could try making a small program that reproduces the discrepancy, using the `fail_resource` from the test.hpp file as in my commit above, where `bytes` is different for the parsed version relative to the copied version, then I can track it.

---

## Comment 5

> Username: sdkrystian  
> Created at: 2020-09-11 15:48:27 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691173478  

The reason for this is becuase it is unlikely for users to modify more than a few values after parsing a document, so when they are created,  we allocate only what is needed and no more than that.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-09-11 15:51:52 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691175171  

> when they are created, we allocate only what is needed and no more than that.  
  
But that is also true for copies, I looked at the code path (and you can too). The test also checks this, and the tests pass.

---

## Comment 7

> Username: hadrielk  
> Created at: 2020-09-11 16:22:41 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691190839  

(Sorry I was away from my computer for a bit)  
  
Let me do more digging on my side. I get a segfault trying to use the `fail_resource` checker.  
  
I hadn't run the unit tests in `boost:json` itself - my dev environment's not setup for that. I had only copied over the run-time code so I could re-use existing tests my company already had for performance+memory for `folly::dynamic` (which we currently use a lot in our codebase).  
  
So this may be a false alarm.

---

## Comment 8

> Username: hadrielk  
> Created at: 2020-09-11 17:09:28 UTC  
> Updated at: 2020-09-11 17:09:46 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691212477  

Got past the segfault. I can get `fail_resource` to correctly detect a mismatch.  
  
Not for your test file's tests, but test for an array of array(s).  
  
For example change this in your test file's array-test portion:  
```  
            s.insert(s.begin(), '[');  
            s.append(1, ']');  
```  
To this:  
```  
        s.insert(s.begin(), "[[");  
        s.append("]]");  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-09-11 17:16:15 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691215626  

Yes... now that IS interesting!!! Investigating...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-09-11 17:23:40 UTC  
> Url: https://github.com/boostorg/json/issues/314#issuecomment-691218900  

This is because `array` public member functions enforce a minimum capacity. But when going through `value_stack` (used by `parser`) this is bypassed. No memory is leaked, but I agree that copies should take up the same space.
