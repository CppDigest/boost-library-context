# #44 - UBSAN errors identified [Open]

> Username: jeking3  
> Created at: 2018-03-07 12:09:10 UTC  
> Updated at: 2018-03-17 19:44:19 UTC  
> Url: https://github.com/boostorg/program_options/issues/44  

I ran a build of develop through UBSAN and a number of tests failed; this needs to be investigated and resolved.  
  
https://travis-ci.org/jeking3/program_options/jobs/350280182

---

## Comment 1

> Username: vprus  
> Created at: 2018-03-16 09:38:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373656282  

One error, about validation_error::m_kind, was correct. I've checked in a possible fix.  
  
The other error is this:  
`../../boost/program_options/detail/config_file.hpp:70:38: runtime error: load of value 144, which is not a valid value for type 'bool'`  
  
I think it's false positive - it's result of copying a default-initialized past-the-end iterator, and such iterator will never use the uninitialized member. How can I suppress this warning?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-03-16 11:52:39 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373691184  

Usually these errors are accurate and point to real problems, I would not recommend disabling them.  The ubsan checkers are fairly mature these days.  There is probably a way to annotate your code to ignore this case, but I've never done that (I always fix the real issue in code).

---

## Comment 3

> Username: vprus  
> Created at: 2018-03-16 12:35:19 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373700180  

Well, in this case I am fairly sure it's not real issue.  
  
On Fri, 16 Mar 2018, 14:52 James E. King, III, <notifications@github.com>  
wrote:  
  
> Usually these errors are accurate and point to real problems, I would not  
> recommend disabling them. The ubsan checkers are fairly mature these days.  
> There is probably a way to annotate your code to ignore this case, but I've  
> never done that (I always fix the real issue in code).  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/44#issuecomment-373691184>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANyeyg42JziNvDgDF69g9wF5txll8Kiks5te6eKgaJpZM4SgVnQ>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 4

> Username: jeking3  
> Created at: 2018-03-16 12:43:24 UTC  
> Updated at: 2018-03-16 12:47:27 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373701939  

I would refer to:  
  
https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html  
  
> Issue Suppression  
> UndefinedBehaviorSanitizer is not expected to produce false positives. If you see one, look again; **most likely it is a true positive!**  
>   
> Disabling Instrumentation with __attribute__((no_sanitize("undefined")))  
> You disable UBSan checks for particular functions with __attribute__((no_sanitize("undefined"))). You can use all values of -fsanitize= flag in this attribute, e.g. if your function deliberately contains possible signed integer overflow, you can use __attribute__((no_sanitize("signed-integer-overflow"))).  
>   
> This attribute may not be supported by other compilers, so consider using it together with #if defined(__clang__).  
  
I believe the same attributes work with gcc, but you would want to check on that.  I would highly advise against adding a suppression until you have reported this to the ubsan team and they verify it is truly a false positive.  The overwhelming majority of cases suggest there is a real code bug.  
  
It might be worth getting a second opinion in any case, from @pdimov perhaps?

---

## Comment 5

> Username: pdimov  
> Created at: 2018-03-16 13:27:38 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373712810  

If you want to be able to copy an uninitialized variable, it needs to be of narrow character type, otherwise it's indeed undefined. May not matter in this case, but the sanitizer is technically correct.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-03-16 13:28:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373712979  

Of course just initializing the `bool` in the default constructor might be even easier. -)

---

## Comment 7

> Username: vprus  
> Created at: 2018-03-17 19:44:19 UTC  
> Url: https://github.com/boostorg/program_options/issues/44#issuecomment-373947424  

Yes, it's easy to initialize in constructor, and I have local patch to that  
effect, but it feels awkward to add extra code to silence a rather  
pedantic warning. Note that valgrind does not appear to warn, precisely  
because that 'uninitialized' variable is never used in any condition.  
  
I'll think about it further over the weekend.  
  
On Fri, 16 Mar 2018, 16:28 Peter Dimov, <notifications@github.com> wrote:  
  
> Of course just initializing the bool in the default constructor might be  
> even easier. -)  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/44#issuecomment-373712979>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye1AqlLpu2p6jjlUpbV80_AMHybTkks5te73zgaJpZM4SgVnQ>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com
