# #53 Fix remove_all(path, ec) to not throw filesystem_error via ++itr [Closed]

> Username: b-spencer  
> Created at: 2017-09-19 20:29:46 UTC  
> Updated at: 2018-09-13 17:13:04 UTC  
> Closed at: 2018-09-13 17:13:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53  

This pull request is in relation to [Boost Trac 13217](https://svn.boost.org/trac10/ticket/13217).  
  
According to the documentation, uintmax_t remove_all(const path& p,  
system::error_code& ec) is not supposed to throw filesystem_error. However,  
when the directory_iterator used in the loop inside the implementation helper  
function remove_all_aux() is incremented, it's not done in a way that  
considers the ec argument. So, when incrementing the iterator fails, the  
operator++() on the directory_iterator throws, which leads to remove_all()  
incorrectly throwing.  
  
To fix this, it seems that one can take the same approach as the recent  
changes for Fix #7307  
https://github.com/boostorg/filesystem/commit/4e4374336c640c2717f6a1b168406e11bdf7d6f1  
and replace the ++itr call in the loop with an explicit call to either  
directory_iterator::increment(system::error_code& ec) whenever an ec argument  
is in effect. This should prevent remove_all() from throwing in such cases and  
thus allow it to conform to its interface specification.

---

## Comment 1

> Username: b-spencer  
> Created_at: 2017-09-19 20:32:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-330664620  

I did not search for other places where operator++() is used on directory iterator when no exceptions should be thrown.

---

## Comment 2

> Username: b-spencer  
> Created_at: 2017-10-12 19:16:28 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-336238187  

I found a related issue in `directory_iterator_constructor` and added a fix for that.

---

## Comment 3

> Username: Beman  
> Created_at: 2017-10-13 11:50:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-336430002  

Will look at this sometime next week.  
  
Thanks,  
  
--Beman

---

## Comment 4

> Username: b-spencer  
> Created_at: 2017-11-01 13:06:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-341100923  

Is there anything I can do to help?

---

## Comment 5

> Username: b-spencer  
> Created_at: 2018-04-03 13:28:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-378249966  

@pdimov @Beman: Boost 1.66.0 was released back in December and seems to still lacks this and other fixes.  What are the obstacles to merging this PR?

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-09-06 20:47:56 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419236088  

Something like https://github.com/boostorg/filesystem/commit/350109a99164463caf60af1a90090cce15ef75ae?

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-09-06 20:50:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419236856  

I'm not sure that the returned `count` is correct when the iteration errors out though; the initial value of 1 is presumably because it includes `p` but we never get to removing it.

---

## Comment 8

> Username: b-spencer  
> Created_at: 2018-09-06 21:03:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419240570  

Yes, 350109a is almost exactly what I was going to change this to.  
  
I agree that `count` looks wrong in the directory case.  It seems like `count` should start as `0` and then be incremented just _after_ the `remove_file_or_directory()` statement, unless `ec && *ec`.

---

## Comment 9

> Username: b-spencer  
> Created_at: 2018-09-06 21:07:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419241581  

But, note the return value of the [standardized version](https://en.cppreference.com/w/cpp/filesystem/remove).  It always returns `uintmax(-1)` on error in the non-throwing case.  Perhaps this should just do the same (and say so in the docs).

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-09-06 22:36:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419263437  

Returning -1 instead of number removed until the error seems to throw away information for no reason, so I won't be switching to that.

---

## Comment 11

> Username: b-spencer  
> Created_at: 2018-09-07 13:23:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-419437858  

Ok.  I have no complaint with `remove_all()` continuing to return the count on error.  On the other hand, I can see how someone would value `boost::filesystem` behaving as close as possible to `std::filesystem` even when the reason for the standard to differ isn't apparent.    
  
Perhaps the thought is that the remove could be somehow parallel and collecting the count might require paying for something you don't use in the error case?  I'm just guessing.  Perhaps @Beman would know since AFAIK he's the author of [the TS](https://isocpp.org/files/papers/P0218r1.html#fs.op.remove_all).

---

## Comment 12

> Username: b-spencer  
> Created_at: 2018-09-13 15:53:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-421057500  

@pdimov, do you need anything else from me here?  I think you've taken over, which works for me.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-09-13 17:13:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/53#issuecomment-421082613  

Should be fixed in develop by https://github.com/boostorg/filesystem/commit/350109a99164463caf60af1a90090cce15ef75ae and https://github.com/boostorg/filesystem/commit/e6df54ce81bf78b7034a11cad74735723be4fe4a.

---
