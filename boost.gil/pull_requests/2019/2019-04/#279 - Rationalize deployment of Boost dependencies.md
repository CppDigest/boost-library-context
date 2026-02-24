# #279 [CI] Rationalize deployment of Boost dependencies [Merged]

> Username: mloskot  
> Created at: 2019-04-12 23:14:25 UTC  
> Updated at: 2019-04-13 11:12:15 UTC  
> Merged at: 2019-04-13 09:51:26 UTC  
> Closed at: 2019-04-13 09:51:27 UTC  
> Url: https://github.com/boostorg/gil/pull/279  

Introduce `get-boost.sh` as Boost downloader for all our CI services.  
  
Credits for `get-boost.sh` idea go to @djarek and @boostorg/beast  
  
### References  
  
* Closes #276  
* Solves issue explained in https://github.com/boostorg/gil/pull/274#issuecomment-482022037  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-04-12 23:39:27 UTC  
> Url: https://github.com/boostorg/gil/pull/279#issuecomment-482753652  

@djarek Thanks for the tip. I just kept the depth as used in the original script   
  
https://github.com/boostorg/beast/blob/2d4293c5141efeabf7394990aa21056bf6dff2b9/tools/get-boost.sh#L12-L14  
  
Hmm, I don't see `--depth` used "on submodule updates". Or, is `git clone`-s use of `--depth` affect the `git submodule`? It doesn't seem like it for me from the Git docs.

---

## Comment 2

> Username: djarek  
> Created_at: 2019-04-12 23:44:58 UTC  
> Url: https://github.com/boostorg/gil/pull/279#issuecomment-482754425  

Ah, shit, looks like I dropped it when I copied the script from the original repo:  
https://github.com/djarek/certify/blob/master/tools/get_boost.sh#L13  
  
It should be `--depth=1` for the superproject and `--depth=10`(or a larger value if you want to be safer) for the submodules.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-04-13 00:02:21 UTC  
> Url: https://github.com/boostorg/gil/pull/279#issuecomment-482756747  

No value for `submodule update` is guaranteed to work. Or at least this was the case last I tried.

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-04-13 09:48:44 UTC  
> Url: https://github.com/boostorg/gil/pull/279#issuecomment-482794642  

@djarek Thanks for checking.  
  
@pdimov Is this related to git version?  
  
Anyway, I'll try optimise the superproject download a bit later. Thanks for the tips.

---
