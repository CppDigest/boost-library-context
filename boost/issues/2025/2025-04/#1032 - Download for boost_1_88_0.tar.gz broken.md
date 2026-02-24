# #1032 - Download for boost_1_88_0.tar.gz broken [Closed]

> Username: VReichelt  
> Created at: 2025-04-28 10:37:26 UTC  
> Updated at: 2025-05-03 09:16:35 UTC  
> Closed at: 2025-05-03 09:16:35 UTC  
> Url: https://github.com/boostorg/boost/issues/1032  

The download link for the boost_1_88_0.tar.gz version is broken.  
Although .zip and .tar.bz2 work fine, the .tar.gz version download results in a 404 error.  
I can even see the file in the directory listing https://archives.boost.io/release/1.88.0/source/, but I cannot download it.  
Maybe there's a permission problem with the file?

---

## Comment 1

> Username: sdarwin  
> Created at: 2025-04-28 11:27:34 UTC  
> Updated at: 2025-04-28 11:50:12 UTC  
> Url: https://github.com/boostorg/boost/issues/1032#issuecomment-2834936525  

Hi @VReichelt ,  
  
It would be helpful to chat with you about this.  Are you in the Cpplang slack workspace?  https://cppalliance.org/slack/   I am `@Sam Darwin` there, you could reply in Slack.  
  
What is the result of this command? Copy and paste the result  
  
`wget -S --spider https://archives.boost.io/release/1.88.0/source/boost_1_88_0.tar.gz`  
  
Which browser, operating system, and region are you in?    
  
The current analysis of the situation is the following:  the file exists on the "origin" servers.  Thus, it should be served and cached by the CDN.   Now, let's say there's a glitch such as an attempted download before the file even exists.   That is, you tried to download boost_1_88_0.tar.gz a month ago.    That would result in a 404 since the file is not available.  The CDN may cache the 404, and it would explain the problem.  However, I have added a 404 cache setting from this page https://www.fastly.com/blog/cache-recipe-success to only cache a 404 for 10 minutes.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-04-28 11:55:04 UTC  
> Url: https://github.com/boostorg/boost/issues/1032#issuecomment-2835002086  

@VReichelt  I have re-examined the CDN code.  The 404 setting (Cache Settings) was at priority 10, while the longer TTL of most download files was at priority of 100 (VCL Snippets).  
  
Based on priorities the 404 should happen first.   
  
But in fact (in the raw VCL code) the longer TTL was happening first.   This may be due to how they choose to implement "VCL Snippets" versus "Cache Settings".  They don't compare priorities between these two groups of settings.  
  
I have moved the 404 into snippets. And cleared the cache.  Try now.

---

## Comment 3

> Username: VReichelt  
> Created at: 2025-04-28 19:38:08 UTC  
> Url: https://github.com/boostorg/boost/issues/1032#issuecomment-2836314365  

Thanks, it's working fine now! From both my local machine as well as AzureDevOps agents.  
Issue can be closed.

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-04-28 19:49:27 UTC  
> Url: https://github.com/boostorg/boost/issues/1032#issuecomment-2836349346  

Glad to hear!  
  
> Issue can be closed.  
  
Perhaps you can try to close it, I'm not an administrator of this particular repository.

---

## Comment 5

> Username: VReichelt  
> Created at: 2025-05-03 09:16:20 UTC  
> Url: https://github.com/boostorg/boost/issues/1032#issuecomment-2848535469  

Issue was fixed.
