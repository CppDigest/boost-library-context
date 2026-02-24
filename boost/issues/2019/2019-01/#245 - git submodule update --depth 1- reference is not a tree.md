# #245 - git submodule update --depth 1: reference is not a tree [Closed]

> Username: fidergo-stephane-gourichon  
> Created at: 2019-01-30 15:38:58 UTC  
> Updated at: 2019-05-14 21:06:08 UTC  
> Closed at: 2019-03-07 21:19:57 UTC  
> Url: https://github.com/boostorg/boost/issues/245  

This was first reported by someone else on https://svn.boost.org/trac10/ticket/13293 with:  
  
> Not sure if this is intended to be supported - but if so it's an issue with the git super-project - please file an issue on github against that.   
  
## Summary  
  
Getting shallow local copy of boost submodules fails.  
  
## How to reproduce  
  
```bash  
git clone --depth 1 --branch boost-1.69.0 https://github.com/boostorg/boost.git boost  
cd boost  
git submodule init libs/config  
# so far so good  
git submodule update --depth 1  
# boom  
```  
  
## Expected  
  
Clone okay  
  
## Observed  
  
```bash  
fatal: reference is not a tree: 67102d043643c51104663c5b00e375c2f476cdf5  
Unable to checkout '67102d043643c51104663c5b00e375c2f476cdf5' in submodule path 'libs/config'  
```  
  
## Additional information  
  
### Workaround: full-history submodule  
  
Somehow wastes bandwidth, which will not suit everyone.  
  
```bash  
git clone --depth 1 --branch boost-1.69.0 https://github.com/boostorg/boost.git boost_works  
cd boost_works  
git submodule init libs/config  
# so far so good  
git submodule update  
```  
  
### Git version  
  
```bash  
git --version  
  
git version 2.7.4  
```  
  
### Background information  
  
Background information on [How to make shallow git submodules? - Stack Overflow](https://stackoverflow.com/questions/2144406/how-to-make-shallow-git-submodules "How to make shallow git submodules? - Stack Overflow"), your mileage may very depending on your version of git.  
  
Any comment welcome.

---

## Comment 1

> Username: mclow  
> Created at: 2019-01-30 18:13:06 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-459049620  

In that SO post that you referenced, it says:  
  
>> As far as I can tell this option isn't usable for submodules which don't track master very closely. If you set depth 1, then submodule update can only ever succeed if the submodule commit you want is the latest master. Otherwise you get "fatal: reference is not a tree".  
>   
> That is true.  
> That is, until git 2.8 (March 2016). With 2.8, the submodule update --depth has one more chance to succeed, even if the SHA1 is directly reachable from one of the remote repo HEADs.  
  
Have you tried a newer version of git?

---

## Comment 2

> Username: mclow  
> Created at: 2019-01-30 18:15:19 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-459050318  

Seems to work ok for me with git 2.17.2

---

## Comment 3

> Username: fidergo-stephane-gourichon  
> Created at: 2019-01-30 19:01:15 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-459066673  

> Have you tried a newer version of git?  
  
I believe indeed that it's the sanest general answer, yet not an option here (we're stuck with old Ubuntu due to ROS robotics libraries that aren't yet available on newer releases).  
  
We use the most up-to-date version of git available on the platform. The issue happens on Ubuntu 16.04 which is not latest but still [maintained for the next two years](https://en.wikipedia.org/wiki/Ubuntu_version_history#Version_timeline).   
  
We'd rather invest time moving everything to newer releases than backporting a newer git on an old release. Until that, we have high bandwidth here, so the deep clone workaround is acceptable.   
  
This conversation here probably still has value as a hint for other people who experience the problem on a git-based workflow with limited bandwidth, which is a fading population like many things happening in this world.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-03-07 19:52:28 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-470669455  

There's nothing we can do about this issue. It's just how `git` works.

---

## Comment 5

> Username: fidergo-stephane-gourichon  
> Created at: 2019-03-07 21:19:57 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-470698714  

Agreed somehow. I'd rather say it's just how **old** versions of git work.   
  
That said, newer versions of git might just compile as easily on old platforms as on new ones.  
  
Back to the cause: this very issue is an implementation of that suggestion on svn.boost.org: [#13293 (git submodule update --depth 1: reference is not a tree) – Boost C++ Libraries](https://svn.boost.org/trac10/ticket/13293 "#13293 (git submodule update --depth 1: reference is not a tree) – Boost C++ Libraries")  
  
> Not sure if this is intended to be supported - but if so it's an issue with the git super-project - please file an issue on github against that.   
  
I suggest we just close the issue and let by-passers comment. Silence here will be a hint that it's not a real issue.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-03-07 22:11:19 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-470716527  

`--depth 1` doesn't work reliably on new versions of `git` too, because Github doesn't allow direct SHA fetch.

---

## Comment 7

> Username: cirosantilli  
> Created at: 2019-05-14 21:01:59 UTC  
> Updated at: 2019-05-14 21:06:08 UTC  
> Url: https://github.com/boostorg/boost/issues/245#issuecomment-492408347  

The exact same bug in question is also mentioned at: https://stackoverflow.com/questions/2155887/git-submodule-head-reference-is-not-a-tree-error/25875273#25875273  
  
It works in Ubuntu 18.04 which has Git 2.17.  
  
What I've been doing to work around the SHA fetch is to just keep a mirror on my GitHub account, and push a branch pointing to the commit that I want to fetch, keep one branch per release branch.
