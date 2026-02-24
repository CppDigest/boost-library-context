# #351 - Links to Boost 1.70 and 1.71 documentation still broken [Closed]

> Username: mloskot  
> Created at: 2019-07-23 19:48:42 UTC  
> Updated at: 2019-10-23 21:42:56 UTC  
> Closed at: 2019-09-30 17:35:11 UTC  
> Url: https://github.com/boostorg/gil/issues/351  

Reminder about the broken links I reported here https://lists.boost.org/Archives/boost/2019/04/245985.php. Shortly, https://www.boost.org/doc/libs/1_70_0/libs/gil/ yields HTTP 404.  
  
I've fiddled with the URL to figure out if the redirects needs an update, but it seems there are no GIL documentation files on the server. In fact, there are no docs in the Boost 1.70 source distribution archive:  
  
![image](https://user-images.githubusercontent.com/80741/61742935-833ba500-ad94-11e9-9b40-1e71d3af4612.png)  
  
A redirect or upload of the missing docs to the server should be arranged to fix it ASAP!  
  
### References  
  
- #272 needs verification after fixing this

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-24 20:35:52 UTC  
> Updated at: 2019-08-09 19:02:36 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-514790015  

@stefanseefeld Do you know answer to Glen's question below?  
I fear Boost 1.71 will ship with broken docs for GIL. Second time in row does not sound like a good thing.   
  
![Screenshot_20190724-223226](https://user-images.githubusercontent.com/80741/61826621-18a56a80-ae63-11e9-9547-85225ae89e3f.jpg)

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2019-07-24 20:50:00 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-514794716  

I can certainly make sure our process to update http://boostorg.github.io/gil is working correctly (both for the `master` as well as the `develop` branches). I'm not sure what is required to generate the monolithic boost documentation, beyond the above. Is that formally documented anywhere ? I know I have struggled to find the answer to that before...

---

## Comment 3

> Username: mloskot  
> Created at: 2019-07-24 23:54:45 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-514841447  

> I'm not sure what is required to generate the monolithic boost documentation, beyond the above.  
> Is that formally documented anywhere ?   
  
Trying to learn it  
https://lists.boost.org/Archives/boost/2019/07/246658.php  
  
Meanwhile, @mjcaisse is also looking at this.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-08-09 19:57:13 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-520044637  

Posted [GIL docs missing again (was: [release] Boost 1.71.0 Release Candidate 1)](https://lists.boost.org/Archives/boost/2019/08/246764.php)

---

## Comment 5

> Username: mloskot  
> Created at: 2019-09-06 08:56:21 UTC  
> Updated at: 2019-09-06 09:05:21 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-528772808  

On Slack, @glenfe @mjcaisse were discussing GIL docs missing from the releases. Below I'm copying [my response](https://cpplang.slack.com/archives/C27KZLB0X/p1567759821323700):  
  
-----  
I think I managed to pin point what was the problem in https://lists.boost.org/Archives/boost/2019/08/246764.php  
  
Shortly, the missing docs for GIL `master` are due to this top-level https://github.com/boostorg/gil/blob/master/Jamfile which @pdimov rightly blames for the `requires`-s.  
Apparently, these checks fail for the docs building environment.  
  
This has been addressed by this commit removing the top-level `Jamfile` in https://github.com/boostorg/gil/commit/4ed7701b47764edf327e7c82f65cf9815026bdc2  
  
Unfortunately, this commit is not yet in `master` because `develop` was having number of changes not ready for release with 1.70/1.71.  
  
I realised that a week before the release and I did not know what to do.  
Would cherry-picking the 4ed7701b47764edf327e7c82f65cf9815026bdc2 commit do the job or only true merges are kosher?  
I could not get in touch with @stefanseefeld  to decide what shall we do (summer holidays etc.)  
  
-----  
  
@stefanseefeld We need to solve it ASAP. @pdimov suggests we should cherry-pick the fix from develop to master.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2019-09-17 22:33:00 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-532427994  

@mloskot , I'm fine with that fix. (I would prefer if there was some sort of "test" (even if it's not automated) that we can use to check whether a given revision exhibits the problem or not, so as to have a measure of success without actually committing to master...)

---

## Comment 7

> Username: mloskot  
> Created at: 2019-09-18 10:39:12 UTC  
> Updated at: 2019-09-18 11:07:07 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-532627701  

@stefanseefeld   
> I would prefer if there was some sort of "test" that we can use to check whether a given revision exhibits the problem or not  
  
Here is one possible:  
  
1. Get Linux environment   
2. Install GCC 4.8 so `gcc --version` prints 4.8. I hope I correctly remember this is the default toolset used on the docs building environment. Let's see if it is confirmed in this thread: [Details of environment building release docs and website](https://lists.boost.org/Archives/boost/2019/09/247037.php).  
3. `cd libs/gil && git checkout master && git pull origin master && cd ../..`  
4. `b2 libs/gil/doc`  
  
Locally on Ubuntu 18.04, I have verified that, in the current `master` branch, our `libs/gil/Jamfile` with its all C++ checks, is the showstopper:  
  
![image](https://user-images.githubusercontent.com/80741/65141632-4e08a780-da11-11e9-98c4-b9b765a4c3f0.png)

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2019-09-18 12:21:30 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-532659175  

@mloskot , what is the above output telling me ? I'v been running this kind of process many times, so I'm rather familiar with it. The piece I'm missing is a check that tells me specifically that the documentation build (as it is performed during the overall boost release website update) is broken. So other than @pdimov telling us what needs to be fixed, is there a check that can be run that yields the same information ?

---

## Comment 9

> Username: mloskot  
> Created at: 2019-09-18 12:38:41 UTC  
> Updated at: 2019-09-18 12:47:55 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-532665146  

@stefanseefeld   
>  what is the above output telling me ?  
  
It tells that this https://github.com/boostorg/gil/blob/master/Jamfile will fail our docs to build on the Boost's global docs building environment where presumably `b2` picks GCC **4.8** (or possibly 4.9 too) **by default**.  
  
> The piece I'm missing is a check that tells me specifically that the documentation build (as it is performed during the overall boost release website update) is broken.   
  
The test above shows exactly that!  
  
In other words, it shows this:  
if Boost uses  Ubuntu 14.04  (or possibly 16.04 too), then it will fail to build docs for our current `master` branch.  
  
Shortly, `b2` skips building docs for GIL from current `master` due to the C++ checks from our `Jamfile` not being satisfied.

---

## Comment 10

> Username: mloskot  
> Created at: 2019-09-26 07:47:17 UTC  
> Updated at: 2019-09-26 09:07:56 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-535383358  

On Gitter, we've further clarified the comments above with @stefanseefeld , see [September 18, 2019 2:54 PM](https://gitter.im/boostorg/gil?at=5d8228f0ab4244767bbfae7e) chat.  
  
I assume all is clear now.  
I'm going to cherry-pick the fixing commits from develop and push it to master:  
  
- 4ed7701b47764edf327e7c82f65cf9815026bdc2 (excluding `test/core/color_base` present in develop only)  
- 628bfc2b653b8d85b50f8fc8283ab1ba8dd95cf3

---

## Comment 11

> Username: mloskot  
> Created at: 2019-09-30 17:35:11 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-536667576  

I assume this one has been fixed by 1d9fe2cff11ae83657e3b0f4a83275a2cbb372d1  
Otherwise, it should be re-opened.

---

## Comment 12

> Username: glenfe  
> Created at: 2019-09-30 19:59:24 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-536726710  

Looks like 1.72 will have GIL documentation: https://www.boost.org/doc/libs/master/libs/gil/doc/html/index.html

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2019-09-30 20:10:05 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-536730556  

Great to hear. Thanks @mloskot for pushing this through !

---

## Comment 14

> Username: mloskot  
> Created at: 2019-09-30 20:27:50 UTC  
> Url: https://github.com/boostorg/gil/issues/351#issuecomment-536737184  

Thanks @glenfe for the very good news!
