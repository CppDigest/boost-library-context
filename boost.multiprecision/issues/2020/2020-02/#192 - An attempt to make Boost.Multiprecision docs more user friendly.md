# #192 - An attempt to make Boost.Multiprecision docs more user friendly [Closed]

> Username: pabristow  
> Created at: 2020-02-04 17:31:13 UTC  
> Updated at: 2020-03-29 17:27:30 UTC  
> Closed at: 2020-03-29 17:27:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/192  

I have fretted that the Boost.Multiprecision Docs don't seem easy to get started  
(unashamedly pandering to the users who 'just want more bits' - a significant potential user group).  
Novice users do not need to get frightened (perhaps frightened-off!) by front- and back- ends or expression templates,  
So I have done some work trying to reduce the steepness of the learning curve, adding many internal and external hyper-links to make navigation easier,  
and a new example warning of the difference between fundamental_types and multiprecision 'dropins'.  
  
(En route I have tried to resolve lots of minor editorial matters like typos (I swear that they breed), grammar, spelling, punctuation, compielr warnings etc.)  
  
My best efforts so far are in a new branch from develop/multiprecision called docs_update_1.  
Maybe some time you can glance and see if you think this is an improvement.

---

## Comment 1

> Username: madhur4127  
> Created at: 2020-02-04 17:43:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/192#issuecomment-582029908  

There are also subtleties like fixed precision really means a backend without allocator and has nothing to do with the bits.  
  
This caused confusion because I initially thought fixed precision implies a backend with `MinBits==MaxBits` much of which arises from these lines in the documentation.  
  
```cpp  
// Fixed precision unsigned types:  
typedef number<cpp_int_backend<128, 128, unsigned_magnitude, unchecked, void> >   uint128_t;  
typedef number<cpp_int_backend<256, 256, unsigned_magnitude, unchecked, void> >   uint256_t;  
typedef number<cpp_int_backend<512, 512, unsigned_magnitude, unchecked, void> >   uint512_t;  
typedef number<cpp_int_backend<1024, 1024, unsigned_magnitude, unchecked, void> > uint1024_t;  
....  
// Fixed precision signed types:  
typedef number<cpp_int_backend<128, 128, signed_magnitude, checked, void> >       checked_int128_t;  
typedef number<cpp_int_backend<256, 256, signed_magnitude, checked, void> >       checked_int256_t;  
typedef number<cpp_int_backend<512, 512, signed_magnitude, checked, void> >       checked_int512_t;  
typedef number<cpp_int_backend<1024, 1024, signed_magnitude, checked, void> >     checked_int1024_t;  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-02-07 18:24:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/192#issuecomment-583540581  

I've made this a draft PR as I couldn't seem to comment on the changes in the branch where as I can in the PR.  See https://github.com/boostorg/multiprecision/pull/193.  
  
Thanks!

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-03-29 13:14:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/192#issuecomment-605633797  

Can this issue be closed?  
Were all points handled in #193  
Thanks!

---

## Comment 4

> Username: pabristow  
> Created at: 2020-03-29 14:18:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/192#issuecomment-605643315  

I believe all in current develop now, so yes.  I think John (who created it as a PR from the branch) needs to close it?  
  
Paul  
  
  
From: Christopher Kormanyos <notifications@github.com>  
Sent: 29 March 2020 14:14  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/multiprecision] An attempt to make Boost.Multiprecision docs more user friendly (#192)  
  
  
Can this issue be closed?  
Were all points handled in #193<https://github.com/boostorg/multiprecision/pull/193>  
Thanks!  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/issues/192#issuecomment-605633797>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4ANY36NEQSFD2XVJUHLRJ5CSLANCNFSM4KP26RDA>.
