# #47 Fix issue documented in Debian bug #1059133 [Closed]

> Username: NatUni  
> Created at: 2024-01-09 23:34:41 UTC  
> Updated at: 2024-01-10 14:08:15 UTC  
> Closed at: 2024-01-10 14:08:14 UTC  
> Url: https://github.com/boostorg/function/pull/47  

I am surely out of my depth here, but this Works4Me™. See: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1059133  
I would love to get some feedback as to why I'm wrong and what this change might break elsewhere.  
  
Full disclosure: I do not run Debian and I did not have _that_ exact issue, per se. I encountered an essentially identical error message (although I'd used -std=c++17) while building a FreeBSD port for https://github.com/BTCGPU/BTCGPU/releases/tag/v0.17.3 against boost-libs-1.83.0_1.pkg. A little Google-fu led me to the bug report linked above, sadly unresolved. I made this modification which I've proposed in this pull request to the devel/boost-libs port on my FreeBSD 13.2-RELEASE-p9/amd64 system in order to resolve the error and build Bitcoin Gold successfully. I then ran the daemon and command line utility exactly once, to liquidate my meager holdings in one wallet-sweeping transaction; so, by no means a thorough test of the software's functionality.  
  
I'm sorry I didn't keep more detailed information for you. I just left the browser tab open to that Debian bug report because it is the _exact_ same error output I was getting. I had this diff to boost-libs laying around at the end of it so I figured the responsible thing to do would be to bring it up to the experts.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-01-09 23:56:06 UTC  
> Url: https://github.com/boostorg/function/pull/47#issuecomment-1883978068  

Does this same error reproduce with 1.84? Because this looks like https://github.com/boostorg/function/issues/46 (resp. https://github.com/boostorg/signals2/issues/68) to me.

---

## Comment 2

> Username: NatUni  
> Created_at: 2024-01-10 00:18:17 UTC  
> Url: https://github.com/boostorg/function/pull/47#issuecomment-1883996033  

> Does this same error reproduce with 1.84? Because this looks like #46 (resp. [boostorg/signals2#68](https://github.com/boostorg/signals2/issues/68)) to me.  
  
Thank you for that insight. I will follow up as soon as someone [else, hopefully] updates the FreeBSD port for Boost. :)

---

## Comment 3

> Username: NatUni  
> Created_at: 2024-01-10 14:08:14 UTC  
> Url: https://github.com/boostorg/function/pull/47#issuecomment-1884917931  

Yes, 1.84.0 worked without [this] modification (other previously-solved FreeBSDisms notwithstanding). Thank you

---
