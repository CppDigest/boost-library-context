# #167 Update getting_started to reflect the arch-and-model tag [Merged]

> Username: pdimov  
> Created at: 2017-10-20 14:28:00 UTC  
> Updated at: 2017-10-21 14:51:01 UTC  
> Merged at: 2017-10-21 14:44:56 UTC  
> Closed at: 2017-10-21 14:44:56 UTC  
> Url: https://github.com/boostorg/boost/pull/167  

I haven't tested this change because I'm not sure how to generate the HTML from the RST.

---

## Comment 1

> Username: Beman  
> Created_at: 2017-10-21 11:15:50 UTC  
> Url: https://github.com/boostorg/boost/pull/167#issuecomment-338384327  

I tried to generate the html, but found that the docutils toolchain on my Windows machine is broken. After wasting 15 minutes trying to fix it, I switched to Ubuntu where the setup is easy:  
  
* Add `using docutils : /usr/share/docutils ;` to `user-config.jam`  
* `sudo apt-get install docutils-common`  
* `cd ~/boost/develop/more/getting_started` and then `b2`  
  
FWIW, I've long ago switched to pandoc for document markup conversions. It handles a vast number of formats and its Windows installer painlessly takes care of installation, including all dependencies.  
  
Thanks for working on the architecture and model changes. They have been needed for years and years.  
  
I'm leaving the P/R testing to you.  
  
Thanks,  
  
--Beman

---
