# #31 - Unnecessary parenthesis warning in gcc-8.0.0 powerpc matrix build [Closed]

> Username: jeking3  
> Created at: 2018-01-24 14:57:08 UTC  
> Updated at: 2019-11-19 14:55:53 UTC  
> Closed at: 2018-01-26 00:29:43 UTC  
> Url: https://github.com/boostorg/mpl/issues/31  

See:  
  
http://www.boost.org/development/tests/develop/output/trippels-powerpc64le-gcc-8-0-date_time-gcc-8-0-0-warnings.html  
  
```  
Warnings: trippels-powerpc64le-gcc-8.0 - date_time / gcc-8.0.0  
Rev c1a20921c54ad6354c3c93e532e76c62b5c598a9 / Sat, 06 Jan 2018 13:35:45 +0000  
  
...  
  
testc_local_adjustor  
../boost/mpl/assert.hpp:188:21: warning: unnecessary parentheses in declaration of ???assert_arg??? [-Wparentheses]  
../boost/mpl/assert.hpp:193:21: warning: unnecessary parentheses in declaration of ???assert_not_arg??? [-Wparentheses]  
                  
testclock  
../boost/mpl/assert.hpp:188:21: warning: unnecessary parentheses in declaration of ???assert_arg??? [-Wparentheses]  
../boost/mpl/assert.hpp:193:21: warning: unnecessary parentheses in declaration of ???assert_not_arg??? [-Wparentheses]  
                  
... (many more)  
```

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-24 16:13:40 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-360186074  

AMDG  
  
  This warning is ridiculous.  What harm do unnecessary  
parentheses do?  It doesn't make the code less readable  
and it's not going to make the code mean something that  
might not be intended either.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: eldiener  
> Created at: 2018-01-24 18:20:45 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-360226309  

I agree with Steven. Attempts to satisfy syntactical compler warnings by changing completely normal C++ code are a huge waste of time, and there is no end to it. This is why I opposed the vc++ shadow warnings fix earlier in date_time and this is yet another example of syntactical warnings that I personally have no intention of wanting to change. if others want to address them, go ahead, but I will not be doing so in any way.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-01-24 21:29:37 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-360279586  

It only shows up in the gcc-8 matrix test.  Don't shoot the messenger.  Close it if you want.

---

## Comment 4

> Username: eldiener  
> Created at: 2018-01-25 14:16:04 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-360478554  

I did not mean to sound harsh. Sorry about that. I will let any others chime in on this before I close it.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-01-26 00:29:42 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-360646926  

It's okay, I'll close it. :)

---

## Comment 6

> Username: Romain-Geissler-1A  
> Created at: 2018-02-27 16:06:24 UTC  
> Updated at: 2018-02-27 16:09:20 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-368930118  

Hi,  
  
I created PR #33 before seeing this issue. So obviously you will not accept it. I have no strong opinion on this warning, however I expect that a Boost user can decide to build with -Wall -Wextra without having warnings from Boost. So would you accept a pull request that explicitly ignore these kind of warnings in assert.hpp using the usual Boost mechanism to ignore expected warnings ?  
  
Cheers,  
Romain

---

## Comment 7

> Username: mclow  
> Created at: 2018-02-27 19:34:00 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-368998776  

Romain --  
My attitude towards warnings has changed over time; I used to believe as you do, that we should deal with all the warnings. However, compilers have added more and more warnings over time, and while most of them are useful, some of them are not. [ Examples: MSVC's 'truncation' warning, Clang's (now neutered) 'tautological limits' warning, and GCC's 'extra parens' warning. ]  Titus Winters said it well in his talk about Abseil at https://www.youtube.com/watch?v=xu7q8dGvuwk, where he says that "some warnings can DIAF"  
  
Couple that with more and more people building with `-Wall -Wextra -Werror` it means that Boost has to be 'ahead of the curve', and find new warnings in unreleased compilers and either (a) "fix" the boost code, (b) suppress the warning, or (c) convince the compiler writers that their warnings are wrong (frequently more than one of the above)  
  
I expect that "surpassing the warning" is the best solution here.

---

## Comment 8

> Username: Romain-Geissler-1A  
> Created at: 2018-02-27 20:16:29 UTC  
> Updated at: 2018-02-27 20:17:10 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-369011713  

Just for me to fully understand, by "surpassing the warning" you mean (b) or (c) (or both ?).  
  
About warning that do not always make sense, well look at the different gcc bug reports I have created for new ones around strncpy. These depends on how good gcc is at inlining things and how you write your code to make the compiler understand you actually want your code like this. Example: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=84468 I also agree that sometimes warning should be challenged, however in most of the cases I have seen so far, they did uncover real bugs (and despite I have had many troubles with the new gcc 8 strncpy warnings, I discovered few but still some real bugs in my code).  
  
About having boost being ahead the curve with unreleased compiler, well you have your own CI on your side, + all the people like me who are actually paid by companies to migrate to newer compiler, newer boost, newer everything, and who try to fix these issues when they see them. Boost is normally big enough to have people contributing to either fix the warnings, or silence them.

---

## Comment 9

> Username: Romain-Geissler-1A  
> Created at: 2018-03-01 09:09:25 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-369525646  

Hi,  
  
I have submitted #34 which does the same thing as #33  but this time by ignoring the warning.  
  
Cheers,  
Romain

---

## Comment 10

> Username: ningfc  
> Created at: 2019-11-18 13:07:39 UTC  
> Updated at: 2019-11-19 14:55:53 UTC  
> Url: https://github.com/boostorg/mpl/issues/31#issuecomment-555007206  

I have build boost with gcc 7.3.0, that is OK
