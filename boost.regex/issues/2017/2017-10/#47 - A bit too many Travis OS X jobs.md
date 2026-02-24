# #47 - A bit too many Travis OS X jobs [Closed]

> Username: pdimov  
> Created at: 2017-10-24 14:00:32 UTC  
> Updated at: 2017-12-23 23:54:21 UTC  
> Closed at: 2017-12-23 23:54:21 UTC  
> Url: https://github.com/boostorg/regex/issues/47  

Mac OS X jobs on Travis are a bottleneck, so you might wish to consider lowering their number somewhat. :-)

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-24 21:36:37 UTC  
> Url: https://github.com/boostorg/regex/issues/47#issuecomment-339140060  

Also, the new `cxxstd` feature of Boost.Build allows one to simplify considerably, see f.ex. https://github.com/boostorg/smart_ptr/commit/e88227b5063ac7f05183327346567a2393e3a23d

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-10-25 17:17:40 UTC  
> Url: https://github.com/boostorg/regex/issues/47#issuecomment-339403804  

Duly lowered.  
  
The ability to test multiple std values in one go is great, but probably not applicable in this case as I suspect the tests would just time out if we try to test too many variants in go?

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-25 17:33:42 UTC  
> Url: https://github.com/boostorg/regex/issues/47#issuecomment-339408656  

Looking at https://travis-ci.org/boostorg/regex/builds/292223115, the jobs don't take that much time, so it should be possible to run them two at a time, if not four. The Travis job limit is 49 minutes, and there's some common startup time that will be saved.
