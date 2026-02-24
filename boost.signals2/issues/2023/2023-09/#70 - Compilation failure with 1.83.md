# #70 - Compilation failure with 1.83 [Closed]

> Username: SoapGentoo  
> Created at: 2023-09-10 13:04:02 UTC  
> Updated at: 2024-05-31 13:57:01 UTC  
> Closed at: 2024-05-31 13:57:00 UTC  
> Url: https://github.com/boostorg/signals2/issues/70  

We're seeing one specific failure of Boost.Signals2 in our Gentoo package tree: [Bug 912309](https://bugs.gentoo.org/912309)  
  
This code used to compile fine under Boost 1.82. [The offending line](https://swift.im/git/swift/tree/Swiften/Component/CoreComponent.cpp#n42) is:  
```  
stanzaChannel_->onPresenceReceived.disconnect(boost::ref(onPresenceReceived));  
```  
here `stanzaChannel_->onPresenceReceived` is of type `boost::signals2::signal<void (std::shared_ptr<Message>)>` and the `onPresenceReceived` argument on the right-hand side is of type `boost::signals2::signal<void (std::shared_ptr<Message>)>` too. The error boils down to an ambiguous overload  
```  
/usr/include/boost/function_equal.hpp:17:14: error: no match for 'operator==' (operand types are 'const boost::reference_wrapper<boost::signals2::signal<void(std::shared_ptr<Swift::Presence>)> >' and 'const boost::reference_wrapper<boost::signals2::signal<void(std::shared_ptr<Swift::Presence>)> >')  
   17 |   { return f == g; }  
      |   
```  
Do you have any ideas or guidance that could help us fix this locally? Upstream seems mostly gone, and this is the last bug for us in Boost 1.83. Paging in @pdimov who recommended I file a bug here.

---

## Comment 1

> Username: fmhess  
> Created at: 2023-09-15 14:26:47 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1721372661  

To fix, you'd need to patch boost.function and boost.signals2 from development, or wait for boost 1.84, or compile against a version of the c++ standard which is not c++20

---

## Comment 2

> Username: SoapGentoo  
> Created at: 2023-09-16 09:39:20 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1722189950  

@fmhess thanks, could you point me to the commits that purport to fix this? I can try them locally, since boost 1.84 is still some time away

---

## Comment 3

> Username: ConiKost  
> Created at: 2023-10-04 14:38:39 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1747013467  

@fmhess ping. Could you please have a look, which commits are needed for fix? Thank you!

---

## Comment 4

> Username: thesamesam  
> Created at: 2023-11-29 19:24:03 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1832560469  

Ping

---

## Comment 5

> Username: SoapGentoo  
> Created at: 2023-12-26 10:58:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1869458562  

Unfortunately, the code still fails with boost 1.84 :cry:

---

## Comment 6

> Username: fmhess  
> Created at: 2023-12-30 19:54:34 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1872593362  

Ok, I fixed disconnection by slots which are signals, and slots which are wrapped in a boost::reference_wrapper.  The fix is on the develop branch with the following two commits:  
  
commit 2ba258f7b465e7bde14663ca3a966019b4ec3694  
commit 4d994bbed820882059c7842ba5e230a26d68f2f0

---

## Comment 7

> Username: ConiKost  
> Created at: 2023-12-31 01:40:36 UTC  
> Url: https://github.com/boostorg/signals2/issues/70#issuecomment-1872644360  

I can confirm, that appliying those patches against boost-1.83 (haven't tested boost-1.84), makes it compiling again.
