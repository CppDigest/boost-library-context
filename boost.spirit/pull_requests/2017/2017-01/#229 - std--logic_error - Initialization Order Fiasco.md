# #229 std::logic_error - Initialization Order Fiasco [Merged]

> Username: sehe  
> Created at: 2017-01-22 00:01:11 UTC  
> Updated at: 2020-02-26 23:27:24 UTC  
> Merged at: 2017-01-22 02:29:58 UTC  
> Closed at: 2017-01-22 02:29:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/229  

(reported by Exagon http://stackoverflow.com/a/41785268/85371)  
  
Due to [Static Initialization Order Fiasco](https://isocpp.org/wiki/faq/ctors#static-init-order) sometimes X3 parsing aborts with a `std::logic_error`. The cause is unitialized `rule::name` fields.  
  
It is clear that the design of Spirit X3 has accounted for static initialization order in that the `parse_rule` specializations contain function-local instances that defeat the issue.  
  
However, the rule name initialization lives inside the namespace-scope static variable and its initialization is not guarded. This is probably an oversight.  
  
Unfortunately I don't think there is a clean/elegant solution that will not introduce more function-local statics (with their syntax and runtime overhead).  
  
This PR adds diagnostics to detect when a rule was copied before its initialization. The commit comments and code detail the approach.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-01-22 02:13:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-274302547  

Hmmm... Tricky. Let me analyze this some more.

---

## Comment 2

> Username: djowel  
> Created_at: 2017-01-22 02:29:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-274303221  

OK, there should be a better way to solve this, but for now, I think this is a good intermediate step.

---

## Comment 3

> Username: wanghan02  
> Created_at: 2020-02-25 15:27:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-590923591  

I'm wondering if we should add this assert_initialized_rule in the second commit [16320a5](https://github.com/boostorg/spirit/pull/229/commits/16320a5566ccd3e6243edcf60ec16c57c75cb21b). If the coding style is bad and with 50% chance it will trigger the Static Initialization Order Fiasco (SIOF) in debug build. But even if it does not trigger SIOF in debug build (also with 50% chance), it could still trigger SIOF in release build and we know nothing about that.  
  
Most x3 parsers could have constexpr constructor (I'm trying to do this now). But this assert_initialized_rule cannot be constexpr and it makes all derived classes of unary_parser/binary_parser cannot have constexpr constructor.   
  
Can we reconsider the solution here?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-02-25 18:23:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-590996072  

@wanghan02 I am sorry I do not understand your issue. Hopefully, rule placeholders SIOF will no cause a real problem unless you are enabling `BOOST_SPIRIT_DEBUG`.  
  
> Most x3 parsers could have constexpr constructor (I'm trying to do this now). But this assert_initialized_rule cannot be constexpr and it makes all derived classes of unary_parser/binary_parser cannot have constexpr constructor.  
  
AFAIK you can use `BOOST_ASSERT` in constexpr function, at least https://godbolt.org/z/sVV_ku compiles fine on every major compiler, and if you are talking about assert firing at compile time after making constructor constexpr -- it is an issue in the user code.

---

## Comment 5

> Username: wanghan02  
> Created_at: 2020-02-25 20:37:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591056526  

Thanks for the information. I still have some questions.  
  
1.    
  
> Hopefully, rule placeholders SIOF will no cause a real problem unless you are enabling BOOST_SPIRIT_DEBUG.  
  
Then why don't we wrap it with BOOST_SPIRIT_DEBUG instead of NDEBUG?   
  
2. From my understanding, this assert over SIOF will only fire at run time. It cannot be a static assert at compile time in a constexpr funtion. Is it correct?  
  
3. I found constructing a big parser takes time. For example,  
`x3::parse(str.begin(), str.end(), some_big_parser_construct_in_place)`  
in this parse function, the construction of the parser can take about 40% of total run time in release build and 90% in debug build (on windows). So if most of the parsers could have a constexpr constructor, most of the parsers we write could be a constexpr variable and save us valuable running time. Therefore if we could at least move that assert_initialized_rule(cannot be in constexpr function) into some better macro instead of NDEBUG, we could make the constructors conditional constexpr (controlled by a macro).

---

## Comment 6

> Username: djowel  
> Created_at: 2020-02-25 22:53:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591115880  

I am not sure if I have time to look into this now, but as I said above, I'm hoping to have a better solution. One thing I really want to do is remove the need for the `rule::name` use of a `std::string`. Most of the time, you use literals with no lifetime issues. It will be a breaking change, but the risk is low, and the payoff is great. Rules should be able to be constexpr too.

---

## Comment 7

> Username: djowel  
> Created_at: 2020-02-25 22:56:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591117583  

> I am not sure if I have time to look into this now, but as I said above, I'm hoping to have a better solution. One thing I really want to do is remove the need for the `rule::name` use of a `std::string`. Most of the time, you use literals with no lifetime issues. It will be a breaking change, but the risk is low, and the payoff is great. Rules should be able to be constexpr too.  
  
OK, I'll have another look today.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-02-26 15:50:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591496452  

> 1. Then why don't we wrap it with BOOST_SPIRIT_DEBUG instead of NDEBUG?  
  
Because `get_info`/`what` could be used directly,  
  
> 2. From my understanding, this assert over SIOF will only fire at run time. It cannot be a static assert at compile time in a constexpr funtion. Is it correct?  
  
Short answer is: the check is needed to cover cases that cannot be resolved at compile time.  
  
> 3. I found constructing a big parser takes time. For example, `x3::parse(str.begin(), str.end(), some_big_parser_construct_in_place)`  
  
Why you are initializing a 'big parser' in a function? If it has to be constructed dynamically (because of something is known at run-time) constexpr constructors will not solve your problem.  
  
> One thing I really want to do is remove the need for the `rule::name` use of a `std::string`. Most of the time, you use literals with no lifetime issues. It will be a breaking change, but the risk is low, and the payoff is great. Rules should be able to be constexpr too.  
  
I would vote for it back then, but since C++20 will have constexpr `std::string` I am not so keen now.

---

## Comment 9

> Username: wanghan02  
> Created_at: 2020-02-26 16:54:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591530126  

I agree that constexpr cannot solve all the problems. But in my opinion, it does bring many benefits. If most of the parser/rules can be constexpr, then why not?   
  
What I propose is, we use another macro other than NDEBUG for this run time check. Then it's the user's decision to choose between this run time check and constexpr.

---

## Comment 10

> Username: Kojoley  
> Created_at: 2020-02-26 17:42:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591552564  

The check, as is, should not interfere with your constexpr things. Please open an issue with a code sample that show the actual problem.

---

## Comment 11

> Username: wanghan02  
> Created_at: 2020-02-26 17:51:04 UTC  
> Updated_at: 2020-02-26 17:52:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591556181  

> The check, as is, should not interfere with your constexpr things. Please open an issue with a code sample that show the actual problem.  
  
@Kojoley Have you read the code? The check calls `what(p)` under `#ifndef NDEBUG` trying to build a std::string which cannot be in a constexpr function.   
  
```  
        template <typename Parser>  
        static void assert_initialized_rule(Parser const& p) {  
            boost::ignore_unused(p);  
  
            // Assert that we are not copying an unitialized static rule. If  
            // the static is in another TU, it may be initialized after we copy  
            // it. If so, its name member will be nullptr.  
            //  
            // Rather than hardcoding behaviour for rule-type subject parsers,  
            // we simply allow get_info<> to do the check in debug builds.  
#ifndef NDEBUG  
            what(p); // note: allows get_info<> to diagnose the issue  
#endif  
        }  
```

---

## Comment 12

> Username: djowel  
> Created_at: 2020-02-26 23:27:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/229#issuecomment-591697072  

> > One thing I really want to do is remove the need for the `rule::name` use of a `std::string`. Most of the time, you use literals with no lifetime issues. It will be a breaking change, but the risk is low, and the payoff is great. Rules should be able to be constexpr too.  
>   
> I would vote for it back then, but since C++20 will have constexpr `std::string` I am not so keen now.  
  
Sure, but we'll still have to deal with c++17 for a long time.

---
