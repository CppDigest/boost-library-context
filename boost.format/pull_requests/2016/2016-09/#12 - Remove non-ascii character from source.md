# #12 Remove non-ascii character from source [Merged]

> Username: tsondergaard  
> Created at: 2016-09-15 08:52:35 UTC  
> Updated at: 2016-11-28 11:18:01 UTC  
> Merged at: 2016-11-02 18:25:53 UTC  
> Closed at: 2016-11-02 18:25:53 UTC  
> Url: https://github.com/boostorg/format/pull/12  

Fixes C4828 warning emitted by Visual C++ compiler using /utf8 or  
/validate-charset flag.

---

## Comment 1

> Username: tsondergaard  
> Created_at: 2016-09-15 08:54:31 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247273298  

A similar fix was applied in 2010 in commit 6b0b95873608.

---

## Comment 2

> Username: mclow  
> Created_at: 2016-09-15 13:40:10 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247330631  

If you're going to change the comment, I'd rather see a reference to "fpos.operations" rather than the section number. In C++11, that is section 27.5.4.2 (instead of 27.4.3.2)

---

## Comment 3

> Username: tsondergaard  
> Created_at: 2016-09-15 13:59:38 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247336047  

The boost contribution guidelines recommends avoiding drive-by-fixes.  
  
https://svn.boost.org/trac/boost/wiki/StartModPatchAndPullReq#AvoidDrive-byFixes  
  
Do you still want me to amend the pull request with a reference to fpos.operations instead of the section number?

---

## Comment 4

> Username: mclow  
> Created_at: 2016-09-15 14:12:03 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247339592  

Yes.  Because the section number is out of date, and has been for five years.

---

## Comment 5

> Username: tsondergaard  
> Created_at: 2016-09-15 14:22:59 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247342837  

Added as separate commit.

---

## Comment 6

> Username: tsondergaard  
> Created_at: 2016-09-16 08:44:38 UTC  
> Updated_at: 2016-09-16 08:45:49 UTC  
> Url: https://github.com/boostorg/format/pull/12#issuecomment-247549283  

Okay, I misunderstood that. Fixed.

---
