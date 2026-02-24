# #33 Promote recent changes to master [Closed]

> Username: jeking3  
> Created at: 2017-09-10 21:44:25 UTC  
> Updated at: 2017-10-18 19:50:29 UTC  
> Closed at: 2017-09-11 03:12:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/33  

CI builds of this merge: https://github.com/jeking3/uuid/pull/2

---

## Comment 1

> Username: mclow  
> Created_at: 2017-09-10 23:32:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328380415  

Usually what we do is to request someone to merge a previous PR (or set of commits) (or all the changes) from `develop` to `master`.  We don't do pull requests for `master`.  
  
This *appears* to be all the changes between `develop` and `master` - is that correct?

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-11 01:06:25 UTC  
> Updated_at: 2017-09-11 01:06:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328390299  

@mclow Correct, however I like to use a pull request to validate the merge through CI which is what the PR in my own account (in the description) is and it built clean.  In the future I can do this on my own account, wait for the results, and promote it without a PR.  I'll close this and do that, does that sound ok?

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-11 03:13:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328404794  

Closing - this was done only to get a CI build set for verification before promoting to master.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-09-11 03:32:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328406690  

What I usually do is watch the testbots results; that way you get to see a bunch of compilers/OSes, not just one.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-09-11 03:37:53 UTC  
> Updated_at: 2017-09-11 03:38:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328407267  

@mclow I'll leverage that in the future.  There seem to be a few errors in other modules showing up here however, and I'm curious why one would build with clang++-4.0 using libstdc++-4.8 (because the latter is quite old) - for example in this result:  
  
http://www.boost.org/development/tests/develop/developer/output/CI-p-7-clang-4-0--std%3Dc++14-boost-bin-v2-libs-uuid-test-test_serialization-test-clang-gnu-linux-4-0-debug.html

---

## Comment 6

> Username: mclow  
> Created_at: 2017-09-11 03:41:44 UTC  
> Updated_at: 2017-09-11 03:41:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/33#issuecomment-328407682  

> I'm curious why one would build with clang++-4.0 using libstdc++-4.8  
  
I agree, it's weird - but we've got lots of users, some with odd set-ups. Some of them have good reasons for them. (I don't know if this is one of the ones with a good reason)

---
