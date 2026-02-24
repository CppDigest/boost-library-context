# #750 - small CI issues [Closed]

> Username: sdarwin  
> Created at: 2023-05-12 15:12:03 UTC  
> Updated at: 2023-07-18 18:54:57 UTC  
> Closed at: 2023-07-18 18:54:57 UTC  
> Url: https://github.com/boostorg/url/issues/750  

`.github/actions/boost_clone/action.yml` calls "git submodule update --jobs".    Ubuntu 16.04 git doesn't have a "--jobs" flag and crashes.  
  
Solution:  
  
Remove the parameter completely in the cases where it's only processing 1 job anyway.   That would also apply to a "for" loop.  
  
Otherwise, an idea could be the bash idiom "command_1 || command_2".   (If it fails, try again without the flag.)  
  
Also,  
  
apt-get takes a space separated list of packages. apt-get can be called without a "for" loop, just send the full list to install.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-05-12 16:52:19 UTC  
> Url: https://github.com/boostorg/url/issues/750#issuecomment-1546024017  

I've been maintaining these actions on a separate repository to make them reusable but didn't update Boost.URL yet.  
  
> Ubuntu 16.04 git doesn't have a "--jobs" flag and crashes.  
  
Great. That should be simple to fix. Yep. I have to test more and more containers because they always miss some functionality I don't test without containers. Some related news is I'm generating GCC binaries so we might not need 16.04 anymore, but yes, this still needs to be fixed.  
  
> apt-get takes a space separated list of packages. apt-get can be called without a "for" loop, just send the full list to install.  
  
That's what I tried at first and still use in some cases. But when you want to allow failure in apt-get not doing it one by one tends to fail. I'll revisit these cases.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-07-18 18:54:56 UTC  
> Url: https://github.com/boostorg/url/issues/750#issuecomment-1640787098  

Obsoleted by 38103320
