# #27 update readme and ci scripts to use new pull methods [Merged]

> Username: jeking3  
> Created at: 2018-05-31 21:08:48 UTC  
> Updated at: 2018-06-06 18:31:20 UTC  
> Merged at: 2018-05-31 21:40:46 UTC  
> Closed at: 2018-05-31 21:40:46 UTC  
> Url: https://github.com/boostorg/signals2/pull/27  

The ci files are now pulled from https://www.github.com/jeking3/boost-ci during the build process.  This allows them to be updated "en masse".  
  
This PR updates the readme badges and I have enabled both Appveyor and Travis CI build jobs, so they should kick in normally on the PR.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-06-01 00:01:46 UTC  
> Updated_at: 2018-06-01 00:07:18 UTC  
> Url: https://github.com/boostorg/signals2/pull/27#issuecomment-393719129  

Yep, so, merging was a bad thing.  The builds failed.  In the future, let's wait for the builds to pass.  It looks like my scripts weren't ready for the slug naming convention of appveyor.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-06-05 19:18:44 UTC  
> Url: https://github.com/boostorg/signals2/pull/27#issuecomment-394828518  

Good news - all the builds are working again:  
  
Appveyor: https://ci.appveyor.com/project/jeking3/signals2-db91c/build/1.0.7-develop  
Travis: https://travis-ci.org/boostorg/signals2/builds/387260353

---

## Comment 3

> Username: fmhess  
> Created_at: 2018-06-06 16:54:57 UTC  
> Url: https://github.com/boostorg/signals2/pull/27#issuecomment-395139486  

So should it be merged to master now?

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-06-06 18:22:28 UTC  
> Url: https://github.com/boostorg/signals2/pull/27#issuecomment-395166564  

It is stable and passes all builds successfully.  I've added a once-monthly develop branch cron job as well to get a Coverity Scan analysis (it is supposed to happen on any push to develop, but a squash or rebase in the GitHub UI may not trigger it - not sure yet).

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-06-06 18:31:20 UTC  
> Url: https://github.com/boostorg/signals2/pull/27#issuecomment-395169216  

I'm working with Travis support to figure out why the Coverity Scan job isn't running, if an additional change is needed (like in `.travis.yml`) there I will submit another PR.

---
