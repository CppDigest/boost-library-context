# #488 Fix gcc 9 -Wdeprecated-copy warning. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-16 20:19:01 UTC  
> Updated at: 2019-04-20 13:11:32 UTC  
> Closed at: 2019-04-20 13:11:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/488  

Hi,  
  
This fixes an -Wdeprecated-copy warning when using gcc 9, as many other cases were fixed already.  
  
Cheers,  
Romain

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-16 20:30:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483831307  

It seems strange and I cannot reproduce it https://wandbox.org/permlink/onXSNnGFIbqdK2dA  
Do you have explanation to this?

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 20:43:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483835678  

@Kojoley You aren't testing the right thing. Check with the copy constructor instead:  
  
```  
int main()  
{  
    unused_type a;  
    unused_type b(a);  
}  
```  
  
(see in https://wandbox.org/permlink/s6Gk9KAgGmsGhsrG)

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-04-16 20:52:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483838591  

Ok.  
Two concerns:  
  1) With your change we loose trivial copy. I hope `unused_type(unused_type const&) = default;` silences the warning.  
  2) I am sure it is not the only place and if we are going to fix the warning we need to fix it everywhere. I will look if I can upgrade GCC on Travis to ensure that.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 20:53:42 UTC  
> Updated_at: 2019-04-16 20:53:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483839148  

Is this piece of code C++11 only ? (ie can I safely use "= default", or should I check for some macros first to use C++11 features)

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-04-16 20:56:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483840019  

> Is this piece of code C++11 only ? (ie can I safely use "= default", or should I check for some macros first to use C++11 features)  
  
This one no. Can you please check, maybe https://github.com/boostorg/spirit/pull/451 was fixing the issue?

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-04-16 21:08:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483844072  

By the look of it, all assignment operators can just be removed, which will also silence the warning.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2019-04-16 21:12:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483845580  

> By the look of it, all assignment operators can just be removed, which will also silence the warning.  
  
I also do not get why they were added, probably because of some old compilers or it is some misunderstanding of Rule of 3? (I seen such thing everywhere in Boost)  
  
Also, Peter, do know is there a PPA with GCC 9? I do not see one :(

---

## Comment 8

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 21:14:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483846076  

I have updated the pull request in this way. I am still hitting another similar issue in my own code, but this time in boost::phoenix, so right now I can't say 100% it fixes my issue.

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-04-16 21:14:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483846122  

GCC 9 hasn't been released yet, has it been?

---

## Comment 10

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 21:15:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483846441  

No gcc 9 is not yet released. gcc 9 is expected to be released in April though, so quite soon (for many years now gcc has been released in April).

---

## Comment 11

> Username: Kojoley  
> Created_at: 2019-04-16 21:20:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483847882  

> GCC 9 hasn't been released yet, has it been?  
  
Nope. https://www.gnu.org/software/gcc/gcc-9/changes.html  
  
It might be a good idea to use unreleased compilers to catch their bugs that affects Boost, but unfortunately I do not see any frequently updated repositories with GCC.  
  
> No gcc 9 is not yet released. gcc 9 is expected to be released in April though, so quite soon (for many years now gcc has been released in April).  
  
Let's delay this until the GCC release, we have enough time before the next Boost release.

---

## Comment 12

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 21:28:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483850461  

Actually now that I fixed the phoenix issue, I have many other places in spirit that triggers this issue. Always a pattern like that:  
  
```  
class A  
{  
    private:  
        // silence MSVC warning C4512: assignment operator could not be generated                                                                                                      
        A& operator= (A const&);      
}  
```  
  
where surrounding the copy operator with `BOOST_DELETED_FUNCTION(...)` fixes the error, until the next one ;)

---

## Comment 13

> Username: Kojoley  
> Created_at: 2019-04-16 21:32:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483851849  

> surrounding the copy operator with BOOST_DELETED_FUNCTION(...) fixes the error  
  
The warning is not triggered in C++03 mode?

---

## Comment 14

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 21:33:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483852189  

Would you accept a pull request that adds BOOST_DELETED_FUNCTION in all files where this comment about warning C4512 is found ? That should be doable with some sed scripting, and should fix many of my warnings. I have exactly the same ones in Boost fusion as well. And this should be safe for all compiler versions.

---

## Comment 15

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-16 21:34:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483852397  

> > surrounding the copy operator with BOOST_DELETED_FUNCTION(...) fixes the error  
>   
> The warning is not triggered in C++03 mode?  
  
The gcc 9 docs reads it's only for C++ >= 11 code.

---

## Comment 16

> Username: Kojoley  
> Created_at: 2019-04-16 21:39:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-483853835  

> Would you accept a pull request that adds BOOST_DELETED_FUNCTION in all files where this comment about warning C4512 is found ?  
  
Sounds OK to me.  
Reminder for you that X3 code is C++14 and it should just use `= delete;`.

---

## Comment 17

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-17 13:31:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-484090694  

Thanks for merging the other pull request.  
  
So for this one, if I understood correctly, it's on-hold until gcc 9 is released, and you will want to fix everything in one go, no just the single occurence I hit in my code ?

---

## Comment 18

> Username: Kojoley  
> Created_at: 2019-04-17 13:40:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-484094203  

> you will want to fix everything in one go, no just the single occurence I hit in my code ?  
  
I would prefer so. I also prefer to keep all Spirit versions in sync (there is `unused_type` in X3).

---

## Comment 19

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-20 08:40:58 UTC  
> Updated_at: 2019-04-20 08:41:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-485070442  

Hi,  
  
Ubuntu 19.04 was freshly released. The release notes (https://wiki.ubuntu.com/DiscoDingo/ReleaseNotes) reads:   
  
> Ubuntu 19.04 comes with refreshed state-of-the-art toolchain including new upstream releases of glibc 2.29, ☕ OpenJDK 11, boost 1.67, rustc 1.31, and updated GCC 8.3, optional GCC 9  
  
So you may now have a way to test this in your CI builds.  
  
Cheers,  
Romain

---

## Comment 20

> Username: Kojoley  
> Created_at: 2019-04-20 13:11:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/488#issuecomment-485116504  

There is still no package in https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test  
  
I removed explicit copy assignment operators of `unused_type` on develop already. It should have fix the problem if it was the only place with this warning from Spirit for you. Feel free to reopen/open a new PR if it did not.

---
