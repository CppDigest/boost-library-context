# #28 - std::isspace requires unsigned [Closed]

> Username: OlafvdSpek  
> Created at: 2017-03-18 08:58:54 UTC  
> Updated at: 2017-05-15 09:56:36 UTC  
> Closed at: 2017-03-20 20:00:50 UTC  
> Url: https://github.com/boostorg/convert/issues/28  

```  
boost\convert\base.hpp(112): warning C6330: 'const char' passed as _Param_(1) when 'unsigned char' is required in call to 'isspace'.  
  
boost\convert\base.hpp(115): warning C6330: 'const char' passed as _Param_(1) when 'unsigned char' is required in call to 'isspace'.  
```

---

## Comment 1

> Username: yet-another-user  
> Created at: 2017-03-18 09:49:44 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287530821  

Much appreciated. I'll have it addressed ASAP.

---

## Comment 2

> Username: yet-another-user  
> Created at: 2017-03-19 08:06:31 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287601204  

Unm, I presume is MS Visual Studio. What version is it? The standard POSIX signature of the function is  
    int isspace(int c)   
It is defined in   
    #include <ctype.h>  
via the cctype header.  
  
Seems like MS have it messed it up. What am I missing?

---

## Comment 3

> Username: OlafvdSpek  
> Created at: 2017-03-19 08:08:12 UTC  
> Updated at: 2017-03-19 08:12:19 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287601254  

http://en.cppreference.com/w/cpp/string/byte/isspace  
  
```  
The behavior is undefined if the value of ch is not representable as unsigned char and is not equal to EOF.  
```  
  
https://linux.die.net/man/3/isspace  
  
```  
These functions check whether c, which must have the value of an unsigned char or EOF,  
```

---

## Comment 4

> Username: yet-another-user  
> Created at: 2017-03-19 08:33:31 UTC  
> Updated at: 2017-03-20 08:52:36 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287602199  

1. We get a compilation warning "unsigned char is required in call to 'isspace". I believe it is wrong for the compiler to issue such a warning because "int is actually required by isspace". So, technically, 'char' is fine (auto-promoted to int).  
  
2. The statement "behavior is undefined if the value of ch is not representable as unsigned char" describes the internal run-time function behavior. However, it does not expect "unsigned char" as the compiler claims. For example, EOF is not "unsigned char". It means that if the supplied "int" goes out of [-1,256) range, then "behavior is undefined". Consequently, I expect  
  
char ch;  
isspace(ch);  
  
to be a valid piece of code at compile-time because it matches the isspace(int) signature (char is promoted to int).  
  
At run-time it is valid as long as 'ch' is ASCII [0,127].  
  
The problem will arise if, say, ch=-55. However, the compiler cannot pick it up. It'll be an internal run-time "undefined behavior".  
  
Do you agree?

---

## Comment 5

> Username: OlafvdSpek  
> Created at: 2017-03-19 08:35:49 UTC  
> Updated at: 2017-03-19 08:42:31 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287602288  

The error is from a code analyzer, not from the compiler. But yeah, negative values will cause undefined behavior.  
  
The input doesn't have to be unsigned char but it has to be in -1 .. 255 (inclusive)

---

## Comment 6

> Username: yet-another-user  
> Created at: 2017-03-19 08:45:16 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287602705  

Uh, a code analyzer. Fair enough. So, the compiler is innocent then. Good. :-) So, technically, compilation-wise the code is correct and there seem nothing to fix. Then, yes, the code analyzer warns us of the std::isspace() function vulnerability... which is fair enough too. I suspect the necessary validation checks are outside boost::convert scope. Do you agree?

---

## Comment 7

> Username: OlafvdSpek  
> Created at: 2017-03-19 08:47:37 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287602801  

No, undefined behavior here is unacceptable.  
I don't know the right solution, I've used `v & 0xff` myself. A static_cast<unsigned> might work as well, perhaps you should ask on the mailing list.

---

## Comment 8

> Username: yet-another-user  
> Created at: 2017-03-19 08:59:15 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287603247  

Correction: there is no undefined behavior here, i.e. in boost::convert. The undefined behavior is in isspace(). Now you are essentially saying that if the user (on one side) messed it up and isspace() (on the other side) does not like it, then it is the middle-man (boost::convert) responsibility to clean it all up. I am not sure I am prepared to accept that. I'll have to think about it. Thanks for the input all the same.

---

## Comment 9

> Username: OlafvdSpek  
> Created at: 2017-03-19 09:02:44 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287603407  

> Correction: there is no undefined behavior here, i.e. in boost::convert.   
  
How so?  
Does boost::convert have preconditions that forbid input having 'negative' chars?

---

## Comment 10

> Username: yet-another-user  
> Created at: 2017-03-19 09:08:48 UTC  
> Updated at: 2017-03-20 08:54:57 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287603632  

There are 3 participants -- the caller, boost::convert, isspace(). It is the isspace() that introduces the undef. behavior but does not addresses that. Still, you seem you believe it's boost::convert task to address that. I am not sure I am that definitive about that. :-)

---

## Comment 11

> Username: OlafvdSpek  
> Created at: 2017-03-19 10:11:30 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287606335  

No, it is convert that violates preconditions of isspace..

---

## Comment 12

> Username: yet-another-user  
> Created at: 2017-03-19 10:22:07 UTC  
> Updated at: 2017-03-20 08:57:00 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287606851  

isspace does not have preconditions to violate. Consequently, boost::convert does not "have preconditions that forbid input having 'negative' chars" (quoting you). Just like isspace(). If instead, you insist that isspace() does have preconditions, then so boost::convert. In other words, boost::convert has (or does not have) preconditions just as isspace. So, if isspace does have those, then it is the user who violates those. Isn't it? Or no matter what it is always boost::convert's fault? :-)

---

## Comment 13

> Username: OlafvdSpek  
> Created at: 2017-03-19 10:31:32 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287607267  

Yes ;)  
  
` In other words, boost::convert has (or does not have) preconditions just as isspace.`  
  
OK, so where are those preconditions of boost::convert documented?

---

## Comment 14

> Username: yet-another-user  
> Created at: 2017-03-19 22:34:39 UTC  
> Updated at: 2017-03-20 08:58:38 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287653605  

... documented  
  
That's a valid point... but lacking/insufficient documentation is somewhat different from a bug in the actual code. I'll keep it mind though to address.

---

## Comment 15

> Username: yet-another-user  
> Created at: 2017-03-20 20:00:50 UTC  
> Updated at: 2017-03-20 20:13:11 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-287880018  

After Peter Dimov's explanations I understand that it is indeed a problem. Corrected. Thank you for your help. Much appreciated.

---

## Comment 16

> Username: OlafvdSpek  
> Created at: 2017-04-25 14:39:25 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-297051588  

Boost 1.64 does not contain this fix, does it? :(

---

## Comment 17

> Username: yet-another-user  
> Created at: 2017-04-25 21:51:15 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-297175970  

Unfortunately it is correct. In late March 1.64 was already closed for changes (or so I understood). So, I did not merge the changes into the master branch. 1.65 is now open, so I did it now. I am sorry.

---

## Comment 18

> Username: OlafvdSpek  
> Created at: 2017-04-26 06:55:58 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-297257546  

1.64 was open for fixes after March 24th.. this should've been included.

---

## Comment 19

> Username: yet-another-user  
> Created at: 2017-04-26 07:05:57 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-297259564  

By Rene Rivera 09MAR17: "The master branch is now closed for all changes except by permission for the Beta release".   
  
If 1.64 was, in fact, open as you indicated, then I misinterpreted that statement above. My most humble apologies.

---

## Comment 20

> Username: OlafvdSpek  
> Created at: 2017-04-26 07:16:12 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-297261473  

> On 24-03-17 02:00, Rene Rivera via Boost wrote:  
> The master branch is is now open for post-beta merges, but only as  
> described in the Post-Beta Merge Policy. See <  
> https://github.com/boostorg/boost/wiki/Release-Beta-Merge-Policy>

---

## Comment 21

> Username: OlafvdSpek  
> Created at: 2017-05-15 08:41:32 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-301412946  

Just to verify, is it in master yet?

---

## Comment 22

> Username: yet-another-user  
> Created at: 2017-05-15 09:56:36 UTC  
> Url: https://github.com/boostorg/convert/issues/28#issuecomment-301430454  

Yes, the develop branch has been merged into the master some time ago. Unfortunately, it was late for 1.64 as we discussed.
