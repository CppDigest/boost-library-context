# #80 - compiling issue 2 [Closed]

> Username: GregKon  
> Created at: 2020-03-10 19:58:51 UTC  
> Updated at: 2021-05-23 22:23:09 UTC  
> Closed at: 2021-05-23 22:23:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80  

Dear Robert  
  
boost 1_72_0  
Another issue with compiling, compiler is IAR for ARM (https://www.iar.com/iar-embedded-workbench/)  
in file safe_compare.hpp  
  
`constexpr less_than(const T & lhs, const U & rhs) {  
    return safe_compare_detail::less_than<  
        std::is_signed<T>::value,  
        std::is_signed<U>::value  
    >::template invoke(lhs, rhs);  
`  
  
keyword template make here compiler error. I have commercial support, guys from support point me paragraph in  c++17 standard: 17.2.5: "A name prefixed by the keyword template shall be a template-id or the name shall refer to a class template or an alias template", the same in C++14.  
removing "template" keyword: compile without errors, also on gcc 9.2.  
  
/Grzegorz

---

## Comment 1

> Username: robertramey  
> Created at: 2020-03-11 03:05:55 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-597420751  

Can you run the whole test suite including the examples.  I don't think anyone has tested this on an ARM processor up until now.  I'll try out your changes.  
  
What about line 159 in the same file?

---

## Comment 2

> Username: robertramey  
> Created at: 2020-03-11 03:20:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-597423975  

Since you have a good contact with IAR, maybe you can get them to run the tests suite

---

## Comment 3

> Username: GregKon  
> Created at: 2020-03-11 16:00:24 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-597718481  

There is compiler error in both cases in that file, after removing extra "template" its compile and few example works.  
On ARM the GCC is king, so it does not have to be true that me first testing on ARM, on gcc it compile well.  
  
Do you mean IAR will test BOOST?  
indeed I have good contact with with IAR support, they don't hide their aversion to BOOST, claiming BOOST base on extensions of compilers and hosts. They do not have motivation to do this.  
I partially agree with them, most of modules in boost relay on threads, mutex, atomic, rtti, exceptions, what are not expected or supported well or supported at all on embedded compilers. I see effort in boost to try be as much close to ISO standard as possible lately (specially your numerics). Consider situation to compile safe_numerics only  (assuming you already introduced fixed) it will still not compile on IAR (strict ISO) compiler due:   
`  
 if defined( _MSC_VER ) && !defined( __clang__ )  
 define BOOST_MP11_UNREACHABLE() __assume(false)  
 else  
 define BOOST_MP11_UNREACHABLE() __builtin_unreachable()  
 endif  
`  
no option for ISO standard and if you are not motivated or not experience with compilers you become discouraged and give up.  
  
Anyway, as I already talk with you some months ago, I am going to test and I hope deploy some boost libs, specially numerics. This is the time.  
  
1. just let me explain what do you mean by "test suite", test from path: libs\safe_numerics\test\ ?  
2. is it cmake projects? if yes I will first try run on gcc than try to make some scripts for test under IAR.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-03-12 04:31:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-598000911  

a) I don't think very many libraries rely on threads, mutex, atomic.  Some rely on exceptions and very few on rtti.   
b) I do not think that all boost libraries need to be supported on IAR.  I'm sure a subset (including safe numerics of course) would be useful.  
c) the boost standard directory structure includes a directory named "test".  The test suite is all the programs in this directory.  Example see [https://www.boost.org/development/tests/develop/developer/safe_numerics.html](url)  
d) I don't think that the "if defined ..." you cite is a big barrier.  
e) Boost is tested exhaustively on various versions gcc so I would expect that IAC should compile on any compiler consistent with gcc - I presume that includes IAR.  
  
The problem is the boost build system - b2. It can be a major pain to set up.  I'm not sure what's involved in a cross compilation situation.  My libraries, safe numerics and serialization include cmake scripts - but this is not required.  
  
IAR should get over their aversion to Boost.  If they were able to say (mostly) compatible with Boost, it would be very helpful to their sales.  And I think it would be mostly just a question of dropping libraries mentioned above.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-03-12 04:32:36 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-598001240  

BTW - safe numerics would be HUGE in an embedded environment!!!

---

## Comment 6

> Username: GregKon  
> Created at: 2020-03-12 18:39:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-598355304  

We will run test suits, CMake should be is easy to run scripts. Give me 1-2 weeks.

---

## Comment 7

> Username: GregKon  
> Created at: 2020-03-18 17:58:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-600778051  

Hi Robert  
  
My team friend help me to run cmake, we have small success to build some of examples with IAR, still some work have to be done with, let you know later on.  
However I always need to second compiler for reference, so we run test on Mingw 9.2.0 64bit.  
build cmake  
than build project  
than make test  
  
1. First of all example 92 and 93 are very long. For our internal test will be excluded.   
  
2. Example 14 failed, I am almost sure that assumption is wrong:  
  
        const int x = 1;   
        const int y = 0;  
        // will emit warning at compile time  
        // will leave an invalid result at runtime.  
        std::cout << x / y; // will display "0"!  
        std::cout << "error NOT detected!" << std::endl;  
  
  
divide by zero is undefined (8.6/4 in c++17 ISO) and in my gcc it crash (no exception). Guessing that processor get exception and windows 10 finish task.  
on integers it crashes, when I try float than simply do nothing.  
I get the same on ARM (without test I am sure), it generate hardware exception (not connected with C++ exception) every time but it depend on uC.  
  
3. all other test on mingw passed  
  
let you know about IAR later on.

---

## Comment 8

> Username: robertramey  
> Created at: 2020-03-19 03:53:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-600974313  

This looks pretty good to me.  
  
example 14 is going to be different depending on the processor/compiler.  On my gcc compiler the divide by zero just returns an undefined result.  But maybe with different command line switches things might be different.  A smart compiler should just see a compile time that x can only be 1 and why can only be 0.  Maybe this is what's going on.  It's undefined behavior for a reason.  Would be interesting to investigate it on GodBolt where that little code snippet can be run on many compilers.  If you haven't used this, I think you find it to be a small, easy, fun and worthwhile project.  
  
I'm not sure why examples 92 and 93 are "too long".    
  
The real torture are the test programs.  They push everything to the limit.  Sounds like they all passed for you.  
  
You might send a note to IAR and see what they say.  Embedded systems are a big target for safe numerics.  
  
Thanks for your efforts.  They are very helpful to me and I hope you've enjoyed making an contribution to safe numerics.  
  
Robert Ramey

---

## Comment 9

> Username: GregKon  
> Created at: 2020-03-19 20:16:18 UTC  
> Updated at: 2020-03-19 20:17:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-601395540  

Hi  
I have found reason: 92 and 93 use thread, something went wrong on my environment, however I am not going investigating since I am not interesting in thread, it does not build on embedded compiler at all.  
  
Regarding undefined behaviour: I think it is misunderstand between us due different understand UB.  
My statement is: when divide by zero x/y, this statement and every next statement after is completely undefined, program flow may done everything, and on my environment (mingw64 9.2) crash) I feel your statement is: "only result is undefined and next instructions is OK". Fix me if I wrong.  
  
However, my claims is: it is not portable example and if you upgrade compiler or change optimization or compiler flags it may crash without information.  
  
Cmake works good for building, but it still in progress because it took few hours to build it in IAR with high optimization (so far 90%).  
running is a next battle because it is not so easy to run from command line but we will do this!

---

## Comment 10

> Username: GregKon  
> Created at: 2020-03-21 18:01:57 UTC  
> Updated at: 2020-03-21 18:08:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-602080384  

Robert  
  
I am announce full success on ARM6 arch with IAR.  
I think we may close this issue. I hope you are not sick of me, because I probably will back in few weeks.  
I think your test are very comprehensive and easy to run. impressive! many thanks!  
  
QUESTION:  
What is the best channel to communicate with author of:  
1. mp11 (some portability issue)  
2. b2 tool (I think it may sense to run whole test for boost on embedded target)  
github or dev list?  
  
NOTES/OBSERVATION:  
1. compile test + example takes few hours, running on host simulator:  single hour  
2. compile with highest optimization cause "out of memory" on my windows 10.  
3. we develop full automatic scripts so we may test with any optimization and on any ARM variation (ARM6/7/8 we use).  
4. I do understand if "success" is printed than test passed, it is not possible for me to catch return value from debugging, so scripts calcualte number of success and failures  and compare with reference results: host gcc, successes are 44 now  
5. "typeid" used in tests is c++ implementation specific, so output from gcc and IAR are a little bit different what not allowed to simply compare outputs and   
6. ratio of learning per hour with this job is comparable to 15 years ago when I started programming professional!  
  
/Greg

---

## Comment 11

> Username: robertramey  
> Created at: 2020-03-21 19:53:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-602095093  

mp11 - it's a boost library by one of our genius developers.  So if you have an issue, just post it on that repository - just as you did on this one.  
  
b2 - hmmm I'm not sure where the best place is.  The developer in charge is often on slack.  The b2 source repository should permit you to post issues.  But my question is - if CMake works for you, why bother the b2?  None of these build systems  are really good enough for my taste and are a pain to deal with.  Consider just sticking to CMake.  
  
1. compile test + example takes few hours, running on host simulator: single hour  
  
rephrase: how much time does it take on which machine.  Note that on my mac development machine - it takes about 5 min  
  
2. compile with highest optimization cause "out of memory" on my windows 10.   
  
I'm guessing that's a Mingv issue  
  
3. we develop full automatic scripts so we may test with any optimization and on any ARM variation (ARM6/7/8 we use). I do understand if "success" is printed than test passed, it is not possible for me to catch return value from debugging, so scripts calcualte number of success and failures and compare with reference results: host gcc, successes are 44 nowj  
  
Besides printing "SUCCESS"  passing tests return 0 while failing tests return 1.  So you should have no problem.  
  
4. "typeid" used in tests is c++ implementation specific, so output from gcc and IAR are a little bit different what not allowed to simply compare outputs.  
  
Right, you can't compare them.  The return code should be good enough.  All tests should return 0  
  
5. and ratio of learning per hour with this job is comparable to 15 years ago when I started programming professional!  
  
RIght - that's why we become boost developers.  If your interested, you can join the boost mailing list and Slack channel.  If there is a boost library that your company depends upon, you could become the official maintainer of that library.  This is very educational, rewarding, and not a huge consumer of time.  If your company is actually depending on that library, you can get them to let you do it on company time.  You can also participate in reviews of submitted boost libraries.  Again, very educational and not a huge time commitment.  You can also run tests and post them to the official tests matrix .  This would be good as we don't have an IAR compiler there.  BUT it's a big time commitment to get it setup so it may not be worth it.  
  
Please consider contacting IAR about your work.  You can tell them they can contact me if they want.  I'm and embedded developer myself and safe numerics is targeted at this kind of application.  They should be promoting this big time!.  
  
And welcome to the Boost community - Once you've invested the effort you're a member.  Worse, it's like the mafia - you can never leave!  
  
Good Luck and thanks a a lot for your efforts.  
  
Robert Ramey

---

## Comment 12

> Username: GregKon  
> Created at: 2020-03-21 20:27:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-602099970  

Thanks for advice!  
I can not catch return value from main() on embedded target (debugger depend) so I catch "success" string.  
Cmake is great indeed. B2 is just for build full boost collections, when ever I would like to use something from boost I already have unit test done.  
  
  
/GReg

---

## Comment 13

> Username: GregKon  
> Created at: 2020-11-28 16:59:43 UTC  
> Updated at: 2020-11-28 16:59:54 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-735256138  

Dear Robert  
  
I have just merge from master, this patch is still not there.  
It does not break anything, tested on GCC and MSVC.

---

## Comment 14

> Username: robertramey  
> Created at: 2020-11-28 22:28:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-735298799  

"Example 14 failed, I am almost sure that assumption is wrong:"  
  
Check the documentation page : Compile Time Arithmetic is Not Always Correct.  
  
example 8: cannot detect compile time arithmetic errors  
Not using safe numerics  
0 error NOT detected!  
Using safe numerics  
error detected:positive overflow error  
Program ended with exit code: 0  
  
Seems like you get a gcc crash here instead of invalid result.  I don't know if this is gcc in general or just your particular configuration for it.  Perhaps I just need to update the text referring to the example.  Divide by 0 is ... undefined so it's hard to make a portable test program for it.  BUT the second part using safe numerics should always work and trap the issue both at runtime and compile time.  After all that is the point of the safe numerics library.  Perhaps we should invert the sequence to have the part which always passes be first.  We're splitting hairs here.  I think this example is good example of trapping bad behavior, but fails differently for different compilers - this making it hard to make the example portable.

---

## Comment 15

> Username: robertramey  
> Created at: 2020-11-28 22:30:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-735298903  

"I have just merge from master, this patch is still not there.  
It does not break anything, tested on GCC and MSVC."  
  
Hmmm - what patch are we referring to.  I don't find any patches in this issue.

---

## Comment 16

> Username: GregKon  
> Created at: 2020-11-29 15:44:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-735413574  

well,  maybe because I did not explicitly defined problem:  
  
file: safe_compare.hpp  
is:  
`constexpr less_than(const T & lhs, const U & rhs) {  
    return safe_compare_detail::less_than<  
        std::is_signed<T>::value,  
        std::is_signed<U>::value  
    >::template invoke(lhs, rhs);`  
  
shall be:  
`constexpr less_than(const T & lhs, const U & rhs) {  
    return safe_compare_detail::less_than<  
        std::is_signed<T>::value,  
        std::is_signed<U>::value  
    >:: invoke(lhs, rhs);`  
  
Lines: 79 and 159. no extra 'template' key word

---

## Comment 17

> Username: robertramey  
> Created at: 2021-05-23 22:23:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/80#issuecomment-846633202  

OK - I've made these changes on the develop branch and tested them with clang and gcc on my local machine.  They should appear on GitHub soon.
