# #104 - Ambiguity within max_digits10 documentation? [Closed]

> Username: acc987  
> Created at: 2019-01-14 10:41:30 UTC  
> Updated at: 2019-04-18 07:17:38 UTC  
> Closed at: 2019-04-18 07:17:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104  

Within [...]tut/limits/constants.html#boost_multiprecision.tut.limits.constants.max_digits10 you state as of 1.69.0 the following: "For Microsoft Visual Studio before 2013, and default float format, a small range of values approximately 0.0001 to 0.004, with exponent values of 3F2 to 3F6, are wrongly input by one least significant bit, probably every third value of significand."  
  
This statement is not quite clear to me and I would appreciate it if you could clarify the following and augment the documentation where applicable:  
1. I am assuming that "values approximately 0.0001 to 0.004" refers to the decimal value of the significand?  
2. I am assuming that the exponent, unlike the significand, is stated as a hexadecimal value?  
3. The exponents (interpreted as hexadecimal values) are only representable in double precision. Does this issue exist for single-precision floating-point values, too?  
4. Are you really meaning to say that it is "wrongly input"? Or do you rather mean that it is wrongly output?  
  
As I am very interested in floating-point to decimal conversion (and vice-versa) in older versions of Visual Studio myself, is there any further documentation available regarding this issue? I noticed that the documentation above was initially introduced with boost release 1.56 and the information that VS2013 and greater is not affected was added with 1.60, but I was not able to find further information or examples that demonstrate the problem.

---

## Comment 1

> Username: pabristow  
> Created at: 2019-01-14 16:46:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-454074553  

From: acc987 [mailto:notifications@github.com]  
Sent: 14 January 2019 10:42  
To: boostorg/multiprecision  
Cc: Subscribed  
Subject: [boostorg/multiprecision] Ambiguity within max_digits10 documentation? (#104)  
  
  
Within [...]tut/limits/constants.html#boost_multiprecision.tut.limits.constants.max_digits10 you state as of 1.69.0 the following: "For Microsoft Visual Studio before 2013, and default float format, a small range of values approximately 0.0001 to 0.004, with exponent values of 3F2 to 3F6, are wrongly input by one least significant bit, probably every third value of significand."  
  
This statement is not quite clear to me and I would appreciate it if you could clarify the following and augment the documentation where applicable:  
  
  1.  I am assuming that "values approximately 0.0001 to 0.004" refers to the decimal value of the significand?  
Correct – about one third of values in that range of values approximately 0.0001 to 0.004.  
  
  1.  I am assuming that the exponent, unlike the significand, is stated as a hexadecimal value?  
Correct.  
  
  1.  The exponents (interpreted as hexadecimal values) are only representable in double precision. Does this issue exist for single-precision floating-point values, too?  
No 32-bit floats are OK.  
One can carrying out a brute-force ‘loopback’ test for *all* C++ float type representations in a couple of hours and confirm that output of a 32-bit float to a at least 9 decimal digit string and re-input gets you back exactly the same binary representation.  
For 64-bit double there are so many values (and you need 17 digits) it is estimated to take a human lifetime! – certainly longer than any hardware that I have owned ;-)  
So a random sampling method was used to test loopback and after many hours, it became clear that it was only over a narrow range of values that there was a one-bit difference on 1/3 of values in that range.  
  
  1.  Are you really meaning to say that it is "wrongly input"? Or do you rather mean that it is wrongly output?  
Yes "wrongly input"?  
  
As I am very interested in floating-point to decimal conversion (and vice-versa) in older versions of Visual Studio myself, is there any further documentation available regarding this issue? I noticed that the documentation above was initially introduced with boost release 1.56 and the information that VS2013 and greater is not affected was added with 1.60, but I was not able to find further information or examples that demonstrate the problem.  
  
The whole business of conversion between floating-point and binary representations is much more complicated than you want to know.  
  
17 Digits Gets You There, Once You’ve Found Your Way<http://www.exploringbinary.com/17-digits-gets-you-there-once-youve-found-your-way/>  
  
and other links on that site will tell more than you want/need to know.  
  
The issue has practical importance if one stores double as decimal digit strings and expects to get identical binary values by reading these back into double.  The difference is only 1 bit, so unlikely to important in precision as the implied uncertainty is in that last least-significant bit, but it will mean, for example, that checksums of a database will not match.  
  
My moral is be – don’t use out-of-date and known-buggy compilers.  
  
HTH  
  
Paul  
  
PS I could dig out the loopback test program from backup if you are really interested.  
---  
Paul A. Bristow  
Prizet Farmhouse  
Kendal UK LA8 8AB  
+44 (0) 1539 561830

---

## Comment 2

> Username: acc987  
> Created at: 2019-01-15 16:57:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-454467396  

Dear Paul,  
  
thank you for your reply. I tried to reworked the parts, which I perceive as ambiguous, and ended up with the following patch: https://gist.github.com/acc987/aca5e46ccbfb3ca11b5777b90616dfaa.  
  
Do you agree with my proposed changes? It seems like you were involved when this note in the documentation was created, so if you agree with my changes, feel free to create a pull request. Otherwise I would create the request if the changes are OK with you.  
  
If it is not too much trouble for you, I would indeed be interested in trying out your reproducer code for myself.

---

## Comment 3

> Username: pabristow  
> Created at: 2019-01-15 17:32:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-454479938  

That looks very slightly clearer if you feel this is worthwhile I'll accept a pull request.  
  
But it seems of largely historical interest only as all currrent GCC, Clang and MSVC compilers finally get it almost all right now.  Rick Regan writes authoritively at https://www.exploringbinary.com/maximum-number-of-decimal-digits-in-binary-floating-point-numbers/ on this surprisingly difficult subject.  I think you should digest what he says fully before experimenting further.  There is also a new algorithm at https://arxiv.org/pdf/1310.8121.pdf which I believe is already in use by several compilers, so the inaccuracies discovered are truely history.

---

## Comment 4

> Username: acc987  
> Created at: 2019-01-21 20:24:19 UTC  
> Updated at: 2019-01-21 20:29:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-456190084  

Dear Paul,  
  
I just created the pull request, as suggested. I regularly work with older compilers and having access to resources documenting such compiler specific issues is invaluable to me. Thank you for pointing out Rick Regan's articles. I have indeed already studied the first one you linked, having discovered it through similar write-ups by Bruce Dawson such as https://randomascii.wordpress.com/2012/03/08/float-precisionfrom-zero-to-100-digits-2/ and https://randomascii.wordpress.com/2013/02/07/float-precision-revisited-nine-digit-float-portability/ and the second link you posted does provide a good summary, in particular with respect to double precision.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-01-24 11:36:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-457166504  

Paul, did you intend to reopen this?

---

## Comment 6

> Username: pabristow  
> Created at: 2019-01-24 11:52:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-457170447  

Yes, but to ADD further info and links on more recent algorithms, keeping the PR.  
  
I suspect I havemis-GITed.  What should I do to rectify this?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-03-04 18:35:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-469365958  

These changes will be in the next release, any reason not to close?

---

## Comment 8

> Username: acc987  
> Created at: 2019-04-18 07:17:04 UTC  
> Updated at: 2019-04-18 07:17:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/104#issuecomment-484385008  

Dear John,  
  
it looks to me like Paul added his comments directly within the documentation with commit 136f71893547faa56353954e76181b6d39db7c8f. I am thus taking the liberty of closing this issue, since a new boost version has been released on 2019-04-12, which contains the corresponding changes.
