# #58 C++11: Fixed bug with initializing phoenix lazy from array types [Merged]

> Username: Kojoley  
> Created at: 2017-12-24 16:45:43 UTC  
> Updated at: 2018-01-02 14:28:45 UTC  
> Merged at: 2017-12-29 19:28:11 UTC  
> Closed at: 2017-12-29 19:28:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58  

Type `const T [N]` decays to `T *const` when you place it into a function signature.  
  
Closes https://svn.boost.org/trac10/ticket/12733.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-12-24 16:50:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-353793916  

Also, please, enable bug tracker in repository settings.

---

## Comment 2

> Username: Flast  
> Created_at: 2017-12-25 04:13:30 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-353827572  

You rock! I'll check when I get home (no CI configured yet...).  
  
> Also, please, enable bug tracker in repository settings.  
  
OK, I ask admin to enable issue and CI. @djowel Can I also get admin role on this repo like fusion?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-25 19:12:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-353886219  

Actually I have 'reinvented' `expr_impl` from scratch, it was one specialization shorter, but it looks a bit more cryptic:  
```cpp  
    template <template <typename> class Actor, typename Tag, typename... A>  
    struct expr_ext;  
  
    template <typename Tag, typename... A>  
    struct expr_impl;  
  
    template <typename... A>  
    struct expr_arg_types {};  
  
    template <typename Tag, typename... A, typename... T>  
    struct expr_impl<Tag, expr_arg_types<A...>, void, T...> : expr_ext<actor, Tag, A...> {};  
  
    template <typename Tag, typename... A, typename H, typename... T>  
    struct expr_impl<Tag, expr_arg_types<A...>, H, T...> : expr_impl<Tag, expr_arg_types<A..., H>, T...> {};  
  
    template <typename Tag, typename... A>  
    struct expr : expr_impl<Tag, expr_arg_types<>, A..., void> {};  
```

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-25 23:32:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-353895680  

@Flast Absolutely. Now you have admin privs.

---

## Comment 5

> Username: Flast  
> Created_at: 2017-12-27 10:33:43 UTC  
> Updated_at: 2017-12-27 10:34:42 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354093149  

Sorry, both...  
I had a cold and can't test this PR... wait a moment or @djowel can you test this?

---

## Comment 6

> Username: djowel  
> Created_at: 2017-12-28 00:13:37 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354199764  

I'll see what I can do, @Flast. It would be best if we could have CI configured for Fusion.

---

## Comment 7

> Username: djowel  
> Created_at: 2017-12-29 10:09:30 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354426097  

OK, I get one error running the tests locally.  
  
Clang OSX  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin17.2.0  
  
====================== start:  
  
darwin.compile.c++ ../../../bin.v2/libs/phoenix/test/from_array.test/darwin-darwin-4.2.1/debug/threadapi-pthread/from_array.o  
regression/from_array.cpp:19:61: warning: use of right-shift operator ('>>') in template argument will require parentheses in C++11 [-Wc++11-compat]  
    boost::phoenix::construct<boost::array<int, sizeof(ints)>>(ints);  
                                                            ^  
                                                (                   )  
regression/from_array.cpp:19:61: error: invalid operands to binary expression ('unsigned long' and 'int *')  
    boost::phoenix::construct<boost::array<int, sizeof(ints)>>(ints);  
                                                ~~~~~~~~~~~~^ ~~~~~~  
regression/from_array.cpp:19:69: error: expected a type  
    boost::phoenix::construct<boost::array<int, sizeof(ints)>>(ints);  
                                                                    ^  
regression/from_array.cpp:19:69: warning: declaration does not declare anything [-Wmissing-declarations]  
2 warnings and 2 errors generated.  
  
    "g++"   -O0 -fno-inline -Wall -g -dynamic -gdwarf-2 -fexceptions -fPIC -m64  -DBOOST_ALL_NO_LIB=1  -I"../../.." -c -o "../../../bin.v2/libs/phoenix/test/from_array.test/darwin-darwin-4.2.1/debug/threadapi-pthread/from_array.o" "regression/from_array.cpp"  
  
...failed darwin.compile.c++ ../../../bin.v2/libs/phoenix/test/from_array.test/darwin-darwin-4.2.1/debug/threadapi-pthread/from_array.o...

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-12-29 10:47:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354430081  

Should be fixed.

---

## Comment 9

> Username: djowel  
> Created_at: 2017-12-29 19:25:13 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354489757  

OK. All tests pass. I'm going to give this a go.

---

## Comment 10

> Username: fletchjp  
> Created_at: 2018-01-01 23:38:15 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354685010  

Hi  
  
I have been attempting to construct use cases for these examples of construct.  
  
This one works fine:  
  
    let(_a = construct<std::string>("str") )  
    [  
        std::cout << _a  << val('\n')  
     ]();  
  
This one fails because it cannot do the assignment to _a.  
  
    // This fails with C++03 and C++11.  
    //let(_a = construct<boost::array<int, sizeof(ints)> >(ints) )  
    //[  
    //  nothing //std::cout << _a  << val('\n')  
    //]();  
  
The error messages come from code in phoenix/object/construct.hpp  
  
This tests for BOOST_PHOENIX_NO_VARIADIC_OBJECT which is always defined in phoenix/core/limits.hpp  
  
The alternative variadic code is marked TODO.  
  
I have not been able to find a way to use the boost::array case.  
  
Best wishes  
  
John Fletcher  
  
  
  
________________________________  
From: Joel de Guzman [notifications@github.com]  
Sent: 29 December 2017 19:28  
To: boostorg/phoenix  
Cc: Subscribed  
Subject: Re: [boostorg/phoenix] C++11: Fixed bug with initializing phoenix lazy from array types (#58)  
  
  
Merged #58<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fpull%2F58&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7Ce1961f69cd0e4b84965708d54ef24e1f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636501724959456260&sdata=jZo6PulvGFPaY%2FpZ2dYX4N6GDMcvmSV%2FLHvpU%2FnaMD4%3D&reserved=0>.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fpull%2F58%23event-1404805380&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7Ce1961f69cd0e4b84965708d54ef24e1f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636501724959456260&sdata=8j8SH3uesYy7kT0%2FvxRjPNFvigUoJvrS%2FJ7JBBNHbn4%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG234Dp27v8rXSO53sFUYtUnSGOm4Z-ks5tFT1MgaJpZM4RL-CE&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7Ce1961f69cd0e4b84965708d54ef24e1f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636501724959456260&sdata=AgUFUsSAA7Thr5Uf5ZsnZtieEzVfKKhgs%2BsXy43%2BTGY%3D&reserved=0>.

---

## Comment 11

> Username: Kojoley  
> Created_at: 2018-01-02 11:00:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354743651  

It looks like it is a known problem http://www.boost.org/doc/libs/develop/libs/phoenix/doc/html/phoenix/what_s_new/phoenix___warning_on__lambda_and_let_.html  
  
> This fails with C++03 and C++11.  
  
The previous bug was c++11 only and that's why I have managed to fix it (and because it should fix some of Spirit examples). I am afraid I am not familiar enough with phoenix internals and also bad at proto things to deal with the bug.  
  
> The error messages come from code in phoenix/object/construct.hpp  
  
It does not matter. I am getting this error `boost/phoenix/object/detail/cpp03/preprocessed/construct_eval_10.hpp(32): error C2440: '<function-style-cast>': cannot convert from 'const int *const ' to 'boost::array<int,3>'` and it feels like another type decay bug. The problem must be in `boost/phoenix/object/detail/target.hpp`.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-01-02 11:03:35 UTC  
> Updated_at: 2018-01-02 11:50:31 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354744332  

Ahh, no it in the other place, type is already decayed here...  
  
```  
std::cout << typeid(decltype(construct<boost::array<int, sizeof(ints) / sizeof(ints[0])> >(ints))).name() << std::endl;  
```

---

## Comment 13

> Username: Kojoley  
> Created_at: 2018-01-02 11:18:23 UTC  
> Updated_at: 2018-01-02 11:51:00 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354746510  

Whoa, it is intentional thing for `val(...)` https://github.com/boostorg/phoenix/blob/77d16af8c8fba511885643e0ffff27f7ea105fb8/include/boost/phoenix/core/value.hpp#L117-L136

---

## Comment 14

> Username: Kojoley  
> Created_at: 2018-01-02 12:00:54 UTC  
> Updated_at: 2018-01-02 14:28:45 UTC  
> Url: https://github.com/boostorg/phoenix/pull/58#issuecomment-354752679  

Ok, I have found the cause why it is initially decays. It is because of `call_traits` usage.  
  
http://www.boost.org/doc/libs/develop/libs/utility/call_traits.htm  
`If T is an array type, then call_traits defines value_type as a "constant pointer to type" rather than an "array of type" (requires partial specialization). Note that if you are using value_type as a stored value then this will result in storing a "constant pointer to an array" rather than the array itself. This may or may not be a good thing depending upon what you actually need (in other words take care!).`

---
