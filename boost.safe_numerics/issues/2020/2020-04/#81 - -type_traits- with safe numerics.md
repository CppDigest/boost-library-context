# #81 - <type_traits> with safe numerics [Closed]

> Username: GregKon  
> Created at: 2020-04-06 11:26:50 UTC  
> Updated at: 2020-10-14 13:05:54 UTC  
> Closed at: 2020-10-14 13:05:54 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81  

Dear Robert   
  
I hope you are well in these difficult times.  
Am I understand correctly that for safe_numeric types, <type_traits> templates has to been overwritten?  
   
for example:  
with gcc 9.2.0 and boost 1.72  
  
this compile:  
static_assert(std::is_unsigned<safe<int>>::value == false);  
static_assert(std::is_signed<safe<int>>::value == false);  
  
/Greg

---

## Comment 1

> Username: robertramey  
> Created at: 2020-04-06 13:17:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-609788445  

I don't see what you're getting here.  I don't see type_traits having anything to do here.  
  
std::is_unsigned<T> is true for unsigned, unsigned long and unsigned char and false for everything else.  This  is specified by the standard.  similarly for is_signed.  And also according to the standard, one can't changed this be specialization.  
  
Sooooo - what did I do.  I depend on numeric_limits.  It is not constrained by the standard but has similar facilities.    
  
so std::numeric_limits<safe<int>>::is_integer == true (or something like that.)  So what we might use (arithmetic) type_traits for, we actually use numeric_limits for.  Its a little bit subtle and many users won't need to go in to this.  Without looking, it should be available in the reference section of the documentation.  Please go in there and check.  If it's not, we'll add it.  If you've come upon a case where it's important for your application and think that a lot people would be helped by explaining this, we can expand the documentation - perhaps include another example/case study.  I've been gently ridiculed by the length of the documentation.  But I don't think that anything should be removed.  
  
Thanks for looking into this - this library benefits by the efforts of many people such as your self who take the time to understand the odd corners and tricky cases.  Hopefully, together we can make it ever better.

---

## Comment 2

> Username: GregKon  
> Created at: 2020-04-06 14:11:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-609819275  

Do you think documentation is too long or to short? :)  
  
Thank you for explanation: In my company we build our own "simply unit test" or rather "examples" for better understand. I have 2-3 issue what may be relevant for my application. I will back to this thread 1-2 weeks and present you what may be add to documentation.  
  
For metaprogramming I have use (std::numeric_limits<T>::min() == 0) to detect unsigned safe numeric.  
  
/Greg

---

## Comment 3

> Username: GregKon  
> Created at: 2020-04-09 11:14:06 UTC  
> Updated at: 2020-04-09 11:29:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-611472586  

Dear Robert  
  
I will continue regarding documentation. but first of all quick about context (some of them may be not relevant now, but I have few topics so just clarify for future use):  
  
==  
1. embedded safety critical  
2. no underlying system (no system call)  
3. no exceptions support (form many, many reasons), so malfunction in arithmetic handle in "assertion callback"  
4. handling arithmetic error depend on need:   
- globally assert  
- encapsulate in module   
- encapsulate in class (just want to know if arithmetic encapsulate in class went correctly or not)  
==  
  
What I notice:  
1. Documentation focused on exception handling  
2. Intention is : https://github.com/boostorg/safe_numerics/issues/82 but I do understand also: "drop replacement for build in integers"  
  
consider code: (sorry but formatting seems not working)  
  
 my_assert {  
  my_assert(const safe_numerics_error e, const char * message){ /* got it !*/} };using u8_t = boost::safe_numerics::safe<::std::uint8_t,  
                                        boost::safe_numerics::native,  
                                        exception_policy<my_assert,  
                                                         my_assert,  
                                                         my_assert,  
                                                         my_assert> >;  
  
int main ()  
{  
  u8_t oveflow {std::numeric_limits<u8_t>::max()};  
  ++oveflow;  
}  
  
overflowing will be catch by assertion (while in your examples by exception)  
  
unsigned overflow is well defined by ISO standard and sometime is used in programming technics. for example in order to simply and fast prescaling. If I use safe integer in third party code what does relay on such construction than I can not use safe numeric because it may be very complex to adopt it. consider also legacy code.  
  
A) base documentation I can not assume if **oveflow is 0** (but it is well defined by standard, in examples we see only exception and simply game is over)  
B) I can not distinguish if this is well defined behaviour or not (positive_overflow_error is also for signed integers)  
  
I do not care to much in B) but A) reason is quite important to define in documentation.  
(in general: does the result of such examples as above in case of: defined behaviour or implementation behaviour is exactly the same as on built in integers)  
What do you think? I may do help you with documentation if you like.

---

## Comment 4

> Username: GregKon  
> Created at: 2020-05-07 07:52:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-625092241  

I think I have found solution on most of my doubts, it just more dig into documentation and source code. we may close issue.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-05-07 14:17:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-625283417  

If you want to suggest changes in either code and/or documentation I would be happy to hear them.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-06-15 18:59:12 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-644318717  

"unsigned overflow is well defined by ISO standard and sometime is used in programming technics. for example in order to simply and fast prescaling. If I use safe integer in third party code what does relay on such construction than I can not use safe numeric because it may be very complex to adopt it. consider also legacy code."  
  
Correct.  If you use techniques supported by the standard for unsigned integers, you  shouldn't use safe<unsigned int, ...>.  The later is designed for those who mean an unsigned integer as opposed to a mod 2^16 (or ??).  If you're using an unsigned integer for something like size, use the safe version to detect errors.  If you using it as a modular integer, the standard defined behavior is what you want.  Choose wisely.

---

## Comment 7

> Username: GregKon  
> Created at: 2020-06-16 07:25:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-644584533  

Indeed, in my first though was: use safe integers everywhere, however what about legacy code or third party code. not such easy and has to be use  "Choose wisely."

---

## Comment 8

> Username: robertramey  
> Created at: 2020-10-14 12:20:21 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-708364104  

may I close this issue?

---

## Comment 9

> Username: GregKon  
> Created at: 2020-10-14 13:05:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/81#issuecomment-708389023  

yes.  
I saw some activity on dev boost mailing list. Is there any activity coming in in safe_numeric?
