# #55 - Boost Atomic 1.78 breaks macOS/iOS integration [Closed]

> Username: jayMcBee  
> Created at: 2022-03-04 16:51:39 UTC  
> Updated at: 2022-03-06 09:25:35 UTC  
> Closed at: 2022-03-04 19:36:52 UTC  
> Url: https://github.com/boostorg/atomic/issues/55  

This recent change in Atomic  
   
'Added a ulock backend for waiting and notifying operations on Darwin systems since Mac OS 10.12, iOS 10.0, tvOS 10.0 or watchOS 3.0'  
  
Pretty much breaks all projects using Boost libraries that in turn depend on Atomic.  
In our scenario we're using Boost Log on iOS/iPadOS and can no further submit updates to the App Store due to this change;  
The *ulock functions are considered private API, thus automated checks detect the methods (doesn't matter if they're referenced or defined as a duplicate by Atomic) and any apps using Atomic are rejected ('Invalid Binary')

---

## Comment 1

> Username: Lastique  
> Created at: 2022-03-04 19:38:41 UTC  
> Url: https://github.com/boostorg/atomic/issues/55#issuecomment-1059459645  

Thank you for the report. I've added a new `BOOST_ATOMIC_NO_DARWIN_ULOCK` configuration macro that disables `ulock`-based implementation of waiting and notifying operations on Darwin systems. `ulock` will still be enabled by default, if supported by the target system.

---

## Comment 2

> Username: jayMcBee  
> Created at: 2022-03-05 06:49:59 UTC  
> Url: https://github.com/boostorg/atomic/issues/55#issuecomment-1059708182  

Wow, that was fast!  
Thanks for the quick fix, I'll apply it immediately.

---

## Comment 3

> Username: jayMcBee  
> Created at: 2022-03-06 08:45:47 UTC  
> Url: https://github.com/boostorg/atomic/issues/55#issuecomment-1059920489  

Confirming that the new BOOST_ATOMIC_NO_DARWIN_ULOCK configuration macro works just fine to circumvent Apple's automated binary checks for private API usage.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-03-06 09:25:35 UTC  
> Url: https://github.com/boostorg/atomic/issues/55#issuecomment-1059926407  

Thanks for the confirmation.
