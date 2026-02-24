# #937 - `doc/html/boostbook.html` redirects to itself [Closed]

> Username: Lastique  
> Created at: 2024-08-15 12:53:23 UTC  
> Updated at: 2024-08-28 14:44:06 UTC  
> Closed at: 2024-08-28 14:44:06 UTC  
> Url: https://github.com/boostorg/boost/issues/937  

This is related to https://github.com/boostorg/boostbook/issues/29.  
  
The [`doc/html/boostbook.html`](https://github.com/boostorg/boost/blob/f400245a6b907e503289281f62ca8176081b8c15/doc/html/boostbook.html) file on both develop and master redirects to http://www.boost.org/doc/libs/master/doc/html/boostbook.html, which is the same file on master. In other words, this file performs infinite redirection to itself.  
  
I'm not sure if this file is supposed to be overwritten on the website, but I didn't find anything to that effect in the superproject docs build scripts and release-tools. If it is supposed to somehow work, please let me know how.  
  
If this file is not used then it should probably be removed. Otherwise, if it is supposed to redirect to BoostBook docs, then it should redirect to either `tools/boostbook/index.html` or `tools/boostbook/doc/html/index.html` (preferably, the former).  
  
Note that `tools/boostbook/index.html` was previously redirecting to `../../doc/html/boostbook.html` (i.e. the problematic file in the superproject), and this was changed to `doc/html/index.html` (i.e. the correct file of the generated BoostBook docs in `tools/boostbook`).  
  
Additionally, there is [`tools/index.html`](https://github.com/boostorg/boost/blob/f400245a6b907e503289281f62ca8176081b8c15/tools/index.html) that has a link to `doc/html/boostbook.html`. This file needs to be updated if `doc/html/boostbook.html` is removed.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-08-15 14:00:13 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2291321396  

In terms of boostbook related files, if everything was reverted to boost 1.85.0, it should presumably work again.  
  
After any updates observe the results of the snapshot:  https://www.boost.org/doc/libs/develop/doc/html/boostbook.html

---

## Comment 2

> Username: Lastique  
> Created at: 2024-08-15 15:03:35 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2291479944  

I don't want to revert back to 1.85 state. The issue needs to be fixed at its root.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-08-15 18:32:46 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2291929606  

> I don't want to revert back to 1.85 state. The issue needs to be fixed at its root.  
  
Sure.  Keep this in mind though: in previous versions of the boost superproject `/doc/html/boostbook.html `was not a fully-formatted, correct, static document.        
Rather, was just that odd redirect to "http://www.boost.org/doc/libs/master/doc/html/boostbook.html".       
Which means in those previous versions, during a build, the documentation was being "generated" and replacing the redirect page.       
Going forward in the future, that should also occur. The pages get built or generated.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-08-15 22:44:40 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2292412905  

Thing is, I cannot find where `tools/boostbook/doc` build is invoked. I tried building `doc` in the superproject, at current version and with 08330f3f0c6ed9ae4f36ea1ea67ad6bdf082ad9e and https://github.com/boostorg/boostbook/commit/2ba31e6c17e445b30f25692bb310237d421d7fc2#diff-414a16a059e4645ca1574c6f4ba6cff4efbaf75047da905407f1337096af30d8 reverted, and in both cases `tools/boostbook/doc` is not being built (meaning its html docs are not generated) and `doc/html/boostbook.html` remains the same circular redirect page. I can see https://www.boost.org/doc/libs/develop/tools/boostbook/doc/html/index.html does not exist, which means BoostBook docs are not being built by release scripts either. I really have no idea how this build is supposed to work, some help would be appreciated.  
  
Now, regarding the dummy `doc/html/boostbook.html` redirect page, it seems that similar circular redirect pages are also present for other libraries, e.g. `doc/html/atomic.html`. Specifically Boost.Atomic builds its docs in `libs/atomic/doc/html`, and this circular redirect page remains unused as long as all links on the website point to the `libs/atomic/doc/html` location. I suspect, this is what was done when Boost.Atomic switched to the standalone docs build, i.e. links on the website were updated and the dummy redirect page was left behind unused. The same could be done with BoostBook as well, but first we need to figure out how to build its docs during the upload to the website.

---

## Comment 5

> Username: Lastique  
> Created at: 2024-08-15 23:11:23 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2292434760  

@Kojoley Could you have a look, please? In particular, is this line:  
  
https://github.com/boostorg/boost/blob/f400245a6b907e503289281f62ca8176081b8c15/doc/Jamfile.v2#L201  
  
supposed to invoke the build of html documentation in `tools/boostbook/doc`? And if yes, why is this not happening? I'm invoking `b2 -j16 release` in `doc` of superproject.  
  
Other than this line, I cannot see where `tools/boostbook/doc` build could have been invoked.

---

## Comment 6

> Username: sdarwin  
> Created at: 2024-08-16 00:09:50 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2292487974  

Earlier you pointed out a change in the superproject:  
  
https://github.com/boostorg/boost/commit/2403dacaa7c37e8207180a5b5e19ef2b062e21ea  
  
That removed "boostbook.xml".  
  
"boostbook.xml" contains the content of the generated pages.   
  
Reverting the commit ought to fix it, right?     
  
Go back to referencing that file again.     
  
But then... your reply is that solution is not optimal. Or not the root of the problem.   Ok.

---

## Comment 7

> Username: Lastique  
> Created at: 2024-08-16 08:11:47 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2293041278  

> Reverting the commit ought to fix it, right?  
  
It doesn't. That is, it doesn't result in `tools/boostbook/doc` being built when `doc` is built. And `doc/html/boostbook.html` is not being replaced either way.

---

## Comment 8

> Username: Kojoley  
> Created at: 2024-08-17 14:39:46 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2294878390  

It's been a while since I had touched docs stuff really, I don't even remember how to build release docs. Could you point me to a starting point? `b2 doc --release-build`?  
  
I suspect the culprit is missing `boostdoc` or `boostrelease` target in `boostdoc/doc/Jamfile` but it's just a guess of mine. I don't know why there is no sanitization for that kind of stuff. I also see that most if not all libraries define one of these target and other as empty, I have no idea why.  
  
Looking at https://github.com/boostorg/boost/blame/master/doc/Jamfile.v2 history the docs building stuff seems to had been implemented by @grafikrobot so he might help untangle this?

---

## Comment 9

> Username: grafikrobot  
> Created at: 2024-08-17 15:02:29 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2294884328  

> It's been a while since I had touched docs stuff really, I don't even remember how to build release docs. Could you point me to a starting point? `b2 doc --release-build`?  
  
The b2 command is in a release python script. Here's where in CI is executes it: https://app.circleci.com/pipelines/github/boostorg/boost/12704/workflows/8e3c6e40-a1f5-48d3-8f34-3e3858e74dce/jobs/27422?invite=true#step-108-11803_107  
  
> I suspect the culprit is missing `boostdoc` or `boostrelease` target in `boostdoc/doc/Jamfile` but it's just a guess of mine. I don't know why there is no sanitization for that kind of stuff. I also see that most if not all libraries define one of these target and other as empty, I have no idea why.  
  
The doc/jamfile deals with it by only building the targets that exist for each library.  
  
> Looking at https://github.com/boostorg/boost/blame/master/doc/Jamfile.v2 history the docs building stuff seems to had been implemented by @grafikrobot so he might help untangle this?  
  
I could try.. Although if it used to work and doesn't now not sure why that would be.

---

## Comment 10

> Username: Kojoley  
> Created at: 2024-08-17 15:06:15 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2294885558  

> > I suspect the culprit is missing `boostdoc` or `boostrelease` target in `boostdoc/doc/Jamfile` but it's just a guess of mine. I don't know why there is no sanitization for that kind of stuff. I also see that most if not all libraries define one of these target and other as empty, I have no idea why.  
>   
> The doc/jamfile deals with it by only building the targets that exist for each library.  
  
So if a library misses documentation it doesn't care? That's not good...  
  
> > Looking at [`master`/doc/Jamfile.v2 (blame)](https://github.com/boostorg/boost/blame/master/doc/Jamfile.v2) history the docs building stuff seems to had been implemented by @grafikrobot so he might help untangle this?  
>   
> I could try.. Although if it used to work and doesn't now not sure why that would be.  
  
There was a change https://github.com/boostorg/boost/commit/2403dacaa7c37e8207180a5b5e19ef2b062e21ea, we fixed one thing and seems to have broken other.

---

## Comment 11

> Username: Lastique  
> Created at: 2024-08-17 20:56:37 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2294983882  

> I suspect the culprit is missing `boostdoc` or `boostrelease` target in `boostdoc/doc/Jamfile` but it's just a guess of mine.  
  
Thanks for the idea. Those targets were never there. I've added them and in my local build *with `--release-build` command line option* `tools/boostbook/doc` got built. I've pushed the [commit](https://github.com/boostorg/boostbook/commit/c693753132f71365a61640c8491eb56cb2a1ee68), we'll see if the docs are uploaded to the website.  
  
(BTW, in my local build, `tools/quickbook/doc` does get built, and `tools/quickbook/doc/Jamfile.v2` is also missing these targets. Go figure.)  
  
However, even though `tools/boostbook/doc` got built, `doc/html/boostbook.html` was not replaced and remained the self-referring stub.

---

## Comment 12

> Username: Kojoley  
> Created at: 2024-08-18 06:25:00 UTC  
> Updated at: 2024-08-18 06:31:15 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2295134056  

Try this:  
```  
alias boostdoc : boostbook/<format>docbook ;  
explicit boostdoc ;  
alias boostrelease ;  
explicit boostrelease ;  
```  
or  
```  
alias boostdoc : boostbook.xml : : <dependency>reference ;  
explicit boostdoc ;  
alias boostrelease ;  
explicit boostrelease ;  
```

---

## Comment 13

> Username: Lastique  
> Created at: 2024-08-18 09:18:53 UTC  
> Updated at: 2024-08-18 09:21:17 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2295190532  

I think this will include BoostBook in the unified Boost documentation twice [here](https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L194-L203) - first by [including](https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L125-L132) BoostBook in `lib-doc-boostdoc-refs` and then in `tools`.  
  
My current `boostdoc` and `boostrelease` targets [worked](https://www.boost.org/doc/libs/develop/tools/boostbook/doc/html/index.html) on the website, so hopefully after updating links in the [superproject](https://github.com/boostorg/boost/pull/938) and the [website](https://github.com/boostorg/website/pull/872) this should be resolved.

---

## Comment 14

> Username: Kojoley  
> Created at: 2024-08-18 10:40:30 UTC  
> Url: https://github.com/boostorg/boost/issues/937#issuecomment-2295214390  

> I think this will include BoostBook in the unified Boost documentation twice [here](https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L194-L203) - first by [including](https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L125-L132) BoostBook in `lib-doc-boostdoc-refs` and then in `tools`.  
>   
  
Then every library is already included twice and it looks like it was intended, in `lib-doc-boostdoc-refs` and then in `libraries` https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L194-L203 https://github.com/boostorg/boost/blob/30c8b4588826e7c8d0b45fd2c47c467296950463/doc/Jamfile.v2#L210-L214.
