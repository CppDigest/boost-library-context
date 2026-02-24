# #845 - Where is https://archives.boost.io/ hosted? [Open]

> Username: cortinico  
> Created at: 2024-01-02 10:33:32 UTC  
> Updated at: 2024-12-16 12:43:53 UTC  
> Url: https://github.com/boostorg/boost/issues/845  

Hi all,  
Nicola from the React Native team here,  
  
Over the last ~2 days, the JFrog Boost repository was down/slow-to-respond and caused significant disruption to the whole React Native ecosystem (iOS apps could not build due to failing CocoaPods installation, Android CI was broken, etc.)  
  
I've seen that your official website now recommends to use https://archives.boost.io/ instead of the JFrog repository.  
  
I'm wondering where is https://archives.boost.io/ hosted. Is it going to be maintained long term or is it just a temporary measure while JFrog was down?  
  
We're considering moving our download URL to point to https://archives.boost.io/ but we're wondering if this is now the recommended repository where to download Boost sources from:  
- https://github.com/facebook/react-native/pull/42118

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-01-02 21:02:59 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1874562764  

The original jfrog links should be re-activated and working now.  It was a mistake on their end.  
  
(archives.boost.io used a major cloud cdn)

---

## Comment 2

> Username: cortinico  
> Created at: 2024-01-03 08:15:24 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1874988742  

Thanks for the clarification @sdarwin   
  
> (archives.boost.io used a major cloud cdn)  
  
I'm wondering what is the recommended source from now on. Should React Native users download from archives.boost.io or from JFrog?

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-01-03 12:34:56 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1875304792  

> what is the recommended source   
  
At this moment, the preferred choice is JFrog.  The boost website ought to reflect that soon.  
  
However...  
  
We hope to switch the JFrog hosting to use a Fastly CDN because they are concerned about the amount of bandwidth.  When that happens  the download links will change again.

---

## Comment 4

> Username: cortinico  
> Created at: 2024-01-03 15:17:21 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1875534845  

> > what is the recommended source  
>   
> At this moment, the preferred choice is JFrog. The boost website ought to reflect that soon.  
>   
> However...  
>   
> We hope to switch the JFrog hosting to use a Fastly CDN because they are concerned about the amount of bandwidth. When that happens the download links will change again.  
  
Thanks for the clarification. Will there be an announcement or should we just check the download link regularly?

---

## Comment 5

> Username: sdarwin  
> Created at: 2024-01-03 15:30:49 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1875554595  

Hoping to enable the new downloads within the next week if possible, but not guaranteed.  At least, check the main website.

---

## Comment 6

> Username: cortinico  
> Created at: 2024-01-08 08:56:03 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1880596544  

@sdarwin we're back with JFrog being down. Any official updates on your end? Should we move everyone to archives.boost.io?

---

## Comment 7

> Username: mgovers  
> Created at: 2024-01-10 14:42:25 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-1884979091  

The [PowerGridModel/power-grid-model](https://github.com/PowerGridModel/power-grid-model) moved to https://archives.boost.io/ but it also appears to be flaky. Restarting our CI once or twice does fix the issue, but it's not really the optimal solution.  
  
We've had similar issues before with `nlohmann-json` and `msgpack-cxx` and decided to push the packaged files to PyPI (see https://github.com/PowerGridModel/nlohmann-json-pypi/tree/main and https://github.com/PowerGridModel/msgpack-cxx-pypi). PyPI has a good track record in terms of availability and is completely free for open source projects and is great for us because we're creating a Python package, anyways.  
  
We're likely going to do the same for `boost` in the coming days so that our CI is no longer dependent on archives with flaky uptime. I will update here when it is done. If the boost maintainers like to take ownership of this - now or in the future - we're very much open to that. We do not seek to have this as our responsibility, anyways, and are more than happy to migrate it to the boost organisation.  
  
For the moderators: if this is not the right location to post this, feel free to move and/or request me to post somewhere else.

---

## Comment 8

> Username: mgovers  
> Created at: 2024-12-13 12:10:09 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-2541310825  

> The [PowerGridModel/power-grid-model](https://github.com/PowerGridModel/power-grid-model) moved to https://archives.boost.io/ but it also appears to be flaky. Restarting our CI once or twice does fix the issue, but it's not really the optimal solution.  
>   
> We've had similar issues before with `nlohmann-json` and `msgpack-cxx` and decided to push the packaged files to PyPI (see https://github.com/PowerGridModel/nlohmann-json-pypi/tree/main and https://github.com/PowerGridModel/msgpack-cxx-pypi). PyPI has a good track record in terms of availability and is completely free for open source projects and is great for us because we're creating a Python package, anyways.  
>   
> We're likely going to do the same for `boost` in the coming days so that our CI is no longer dependent on archives with flaky uptime. I will update here when it is done. If the boost maintainers like to take ownership of this - now or in the future - we're very much open to that. We do not seek to have this as our responsibility, anyways, and are more than happy to migrate it to the boost organisation.  
>   
> For the moderators: if this is not the right location to post this, feel free to move and/or request me to post somewhere else.  
  
FYI: due to the latest release resulting in issues in our pipeline again (1.87.0 was available for some platforms but not for all of them), we have finally decided to implement the solution proposed in the above comment in https://github.com/PowerGridModel/boost-pypi (note: the `conda-forge` version of `libboost-headers` was not sufficient for us; I can elaborate further if needed but it basically amounts to not every Python end user having conda available). The library uploads a repackaged version of only the headers in the `brew` packaged version of `boost` to https://pypi.org/project/libboost-headers/.  
  
For moderators or admins of Boost: we are more than willing to donate this project to you. Please contact us if you are open to that.

---

## Comment 9

> Username: sdarwin  
> Created at: 2024-12-13 15:55:22 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-2541753013  

Hi @mgovers ,  
  
Thanks for the comment!  
  
The new download URL is at https://archives.boost.io/ .  I see in github actions you hit this error.  
  
```  
requests.exceptions.HTTPError: 404 Client Error: Not Found for url: https://archives.boost.io/release/1.87.0/source/boost_1_87_0.tar.gz  
```  
  
It should be possible to take various steps to troubleshoot. The first one, I modified the Fastly VCL configuration to cache 404s for 10 mins.  Not the "default" time period which is 1 hr, or the quite long (a month) for archive zip files.  Let's see how this goes, please report back errors.

---

## Comment 10

> Username: mgovers  
> Created at: 2024-12-16 06:43:37 UTC  
> Updated at: 2024-12-16 06:44:06 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-2544732966  

Hi @sdarwin ,  
  
Thank you for your feedback  
  
> The new download URL is at https://archives.boost.io/ . I see in github actions you hit this error.  
>   
> ```  
> requests.exceptions.HTTPError: 404 Client Error: Not Found for url: https://archives.boost.io/release/1.87.0/source/boost_1_87_0.tar.gz  
> ```  
>   
> It should be possible to take various steps to troubleshoot. The first one, I modified the Fastly VCL configuration to cache 404s for 10 mins. Not the "default" time period which is 1 hr, or the quite long (a month) for archive zip files. Let's see how this goes, please report back errors.  
  
Our main issue is that we do not have the capacity to debug. It's good that this may solve the error, but we did get the 404 over the course of several hours (first noticed Thursday 2:45PM UTC+1, and again/still Friday 8:30AM UTC+1), so I don't think caching is the only issue.  
  
Anyways, we now migrated to the alternative solution, which pretty well suits our use case of having it as a `build-dependency` for the C/C++ core of our Python package.

---

## Comment 11

> Username: sdarwin  
> Created at: 2024-12-16 12:19:39 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-2545481824  

@mgovers  the traffic levels on the download are significant, in the TBs per day, which is the reason we have preferred to use a CDN (Fastly) and not shift the burden to possibly unsuspecting hosting services, pypi or even github, although both of those organizations may be equipped to handle the bandwidth load. "again/still Friday 8:30AM UTC". ok, that time was still before the most recent update.

---

## Comment 12

> Username: mgovers  
> Created at: 2024-12-16 12:43:52 UTC  
> Url: https://github.com/boostorg/boost/issues/845#issuecomment-2545534599  

> [@mgovers](https://github.com/mgovers) the traffic levels on the download are significant, in the TBs per day, which is the reason we have preferred to use a CDN (Fastly) and not shift the burden to possibly unsuspecting hosting services, pypi or even github, although both of those organizations may be equipped to handle the bandwidth load. "again/still Friday 8:30AM UTC". ok, that time was still before the most recent update.  
  
  
Hi @sdarwin ,  
  
Thank you again for the reply. I understand your point and I did not know about the most recent update. We'll have to discuss amongst ourselves whether to migrate back or to keep it as is, as we were thinking of migrating away from the intermediate downloader (`pybuild-header-dependency`) anyways. I will keep you up to date.
