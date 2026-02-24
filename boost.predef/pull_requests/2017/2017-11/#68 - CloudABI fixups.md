# #68 CloudABI fixups [Merged]

> Username: jeking3  
> Created at: 2017-11-09 13:42:29 UTC  
> Updated at: 2017-11-10 12:18:10 UTC  
> Merged at: 2017-11-10 04:48:36 UTC  
> Closed at: 2017-11-10 04:48:36 UTC  
> Url: https://github.com/boostorg/predef/pull/68  

CloudABI support hasn't reached a release yet, and I have a couple fixups - one typo fix and one to align the filenames with the macros for cloudlibc.  This is based on actual use, getting Boost.Uuid to build under CloudABI with cloudlibc.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-11-09 15:01:48 UTC  
> Url: https://github.com/boostorg/predef/pull/68#issuecomment-343181056  

@grafikrobot hopefully we can pull this into 1.66.0, given the typo that's been fixed which makes it unusable if not resolved.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2017-11-09 15:56:16 UTC  
> Url: https://github.com/boostorg/predef/pull/68#issuecomment-343199092  

It will make it into 1.66.. But not the beta of 1.66. Since that closed yesterday, AFAIK.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-11-09 16:52:03 UTC  
> Url: https://github.com/boostorg/predef/pull/68#issuecomment-343217776  

Was anything in repository "master" for each project automatically included in that, and does the release manager create the tags in each repo?  (Never been through this before as a maintainer)

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2017-11-09 17:01:01 UTC  
> Url: https://github.com/boostorg/predef/pull/68#issuecomment-343220784  

(1) yes, (2) yes. At some point the release team disables the bot that automatically updates all the submodule masters to the superproject. Then a release is built from that state (and tagged everywhere).

---
