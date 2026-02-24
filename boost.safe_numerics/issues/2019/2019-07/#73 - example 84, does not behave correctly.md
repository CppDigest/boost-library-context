# #73 - example 84, does not behave correctly [Closed]

> Username: GregKon  
> Created at: 2019-07-12 18:43:37 UTC  
> Updated at: 2019-12-14 18:48:38 UTC  
> Closed at: 2019-12-14 18:48:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73  

I think it may be related to:  
https://github.com/boostorg/safe_numerics/issues/69  
  
GCC 8.2.0 Mingw, 64bit  
  
`  
type in values in format x y:123 125  
  
x<signed char>[-24,82] = 123  
  
y<signed char>[-24,82] = 125  
  
z = <short>[-48,164] = 248  
  
(x + y) = <short>[-48,164] = 248  
  
(x - y) = <signed char>[-106,106] = -2  
  
<short>[-48,164] = 248  
`

---

## Comment 1

> Username: GregKon  
> Created at: 2019-07-13 16:42:28 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73#issuecomment-511136395  

It is file: example84.cpp from boost 1.70.  
It caches exception only if value is over int8_t, e.g. 128, but I am expect exception if value is over range -24 ..82, correct?  
  
If you want, I may test it on VS 2017 in the middle of week.

---

## Comment 2

> Username: robertramey  
> Created at: 2019-07-13 17:15:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73#issuecomment-511138924  

I've take an cursory look at this and resolved to spend some more time on it later.  I looked at both my bjam and CMake test suites and the test/example is commented out because I couldn't figure out how to use console input in these environments.  I believe that CMake at least can handle this so I'll try to enable this which would solve the problem once and forever.  
  
Just now I looked into the implementation and see a problem at line #1723 in file safe_base_operations.  I think it should be  
  
m_t = x;  
  
But I'll have to investigate further and right know I've been bogged down in other things.  
  
From your attention to very obscure corner cases and interest/attention to the documentation, looks like you've taken an interest in the library.  I'm sort of curious about your motivation as I've found it difficult to find many other people who consider this problem important and this proposed solution attractive.  In any case, I much appreciate your interest and help.  
  
Robert Ramey

---

## Comment 3

> Username: GregKon  
> Created at: 2019-07-15 11:27:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73#issuecomment-511365007  

Dear Robert  
  
I am interested because I would like to use it in commercially  :)  
Expanding my story: I am software engineer with ~15 years in embedded development, last years in safety critical devices. I am also a little bit geek regarding safety special in arithmetic, so my eyes is teach how to find such errors during review.  
Currently I am preparing myself to lead my team to move from C to C++ and also looking some extra libs/tools to build motivation of my team to easier movement. This library may be one of trump.  
Long time ago I was looking similar functionality, however found only SafeInt, but I am not expect using exception so this library falls.   
  
I have found  safe_integers by accident, I read about boost regarding different library and enter on front page, found on front page that in 1.68 released "safe_numeric", safe means always something interesting for me and I think I liked. specially bounded functionality flexibility in policy, unit tests! etc. I found only few hours to looking your library but already liked. I already have some toughs regarding automatic policy, will be post later.  
So in general: I will try to smooth move in new project into C++ in next months, we always use unit test on dual target PC/gcc and commercial compiler on ARM embedded target. Obviously before  we use this lib we will do the same so I hope I will let you know how it works in real life if successfully introduce this lib.  
I also have to learn how to run boost unit test, so far I found 2-3 different ways and I am confused.  
  
BTW: have you seen this project:   
http://doublewise.net/c++/bounded/  
It probably something very similar, I was not able to compile since it require gcc 9.1 (propably C++20), this disqualifies it from my world (also not active community at all and no contact with author).  
But it maybe something worth to look at.  
  
/Grzegorz

---

## Comment 4

> Username: robertramey  
> Created at: 2019-07-15 15:25:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73#issuecomment-511448726  

"I am software engineer with ~15 years in embedded development, last years in safety critical devices."  
  
You are my target audience.  
  
"Currently I am preparing myself to lead my team to move from C to C++"  
  
Note that the library can be very useful even in the development of C programs.  This is illustrated by the presentation at CppCon 2019 and the example which uses the safe numerics library to build/verify stepper motor for a 16k 8 bit processor.  The usage of safe numerics is totally orthogonal to usage of C++.  You might want to consider as a first step in your evolution using safe numerics in your C code as illustrated in the stepper motor example.  It might be helpful in easing your team into C++.  Moving code from C to C++ will almost certainly result in an entirely new code base.  
  
"boost unit test"  Note that I do not use boost unit test.  I found it problematic for various reasons.  There are other options available that you should consider.  Note also that I don't use any rest frame work at all.  I include one stand along program to test each aspect of functionality.  This seems pretty tedious - and it is.  But it's worked out best for me as when a test fails for someone else, the identity of the test failure  points directly to the problem.  
  
"BTW: have you seen this project:"  
  
I have.  I believe the documentation bibliography includes links to all known libraries of similar functionality. I was at the 2014 C++Now presentation given by David.  I complimented on a great presentation. I started work on this library in 2012 so we were working in parallel.  There are several alternative libraries which do this.  But I believe that after advent and promotion via boost, most if not all of these efforts have been abandoned.  
  
Getting some feedback from potential users convinced me that a library with significant runtime overhead was never going to attractive to potential users.  Being fairly conversant with template meta programming (I'm also the author of the boost serialization library), I refocuses my efforts to move as much "proof" to compile time as possible.  As one might imagine, this is quite an effort.  
  
Thanks for you interest in this.  I look forward to your observations and experience.  Such observations  
  
a) will be misunderstandings of how the library is to be used and/or what it can actually do or not do.  These will be addressed via enhancements to the documentation.  
b) bugs where things do work as advertised.  These will be fixed.  My general goal is to try to make "no known bugs" as a fixed point.  Discovery of a bug almost always starts with adding and/or correcting a test so that bug is detected by the tests.  The goal of this approach is to ensure that the library never gets into the "whack-a-mole" scenario where fixing one thing breaks another so the bugs are just shifted around forever.  That is the number of bugs in the library must be monotonically decreasing.  
c) suggestions for enhancements.  A mixed bag.  Some are so hard and or narrowly focused so as too not be practical.  Some change/expand the scope of the in a way that clouds is obvious purpose and/or compromises its conceptual integrity.  Some are small, easy to do and useful - these will be included.  
  
Feedback from real world projects is much needed.  
  
I'm now working on inclusion of floating point arithmetic in safe numerics.  This not a straight forward extension/enhancement.  It's a lot of work - much of which is extending/generalizing the test frame work.

---

## Comment 5

> Username: robertramey  
> Created at: 2019-12-14 18:48:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/73#issuecomment-565742312  

I made some changes to address other bugs.  Now I've gone back and retested your inputs above and verified that it detects the error as it should - at least on my mac os machine.  As the fix on the other issue seems directly related to the problem here, I'm guessing that this is fixed also.  So I'm going to close this issue.  Feel free to re-open if it fails in your environment.  
Robert Ramey
