# #7 Removed usage of "safe_bool" idiom from tribool. [Closed]

> Username: robertramey  
> Created at: 2018-05-18 22:16:16 UTC  
> Updated at: 2018-10-14 14:15:28 UTC  
> Closed at: 2018-10-13 13:30:26 UTC  
> Url: https://github.com/boostorg/logic/pull/7  

This was initially motivated by the fact that usage of this idiom with all current and past gcc compilers with constexpr does not work.  First it reports reference to the address of a local member function as not being constexpr.  This may or may not be correct according to the standard.  FWIW clang is OK with it.  Making the member function constexpr fixes this error.  But still there is a problem.  in GCC, the operator to implicitly covert the instance to a void * does not seem to work.  Invocation of this operator in the context of an argument to an if(... or other conditional doesn't result in behavior similar to bool.  Not even a static cast to a bool seems to work.  Again CLANG is OK with all this.  This has been reported as an issue to the GCC team.  
  
One idea which has been tried is to use C++11 "explicit" as a substitute from the safe_bool idiom.  Unfortunately this breaks legacy code.  
  
bool f(x) {  
	return tribool(true); // fails  
}  
  
So this is not a great solution either.  
  
Attempts to resolve this were inconclusive.  This motivated me to think about why the safe_bool idiom was being used here at all.  According to articles on safe_bool articles this main reason is to support the common practice of using a not null value in a conditional:  
  
Testable t;  
  
if(t) ...  
  
without accidentally permitting things like  
  
if(t >> 1)...  
  
The above would occur if Testable included an operator bool () const member function.  
  
Basically safe_bool is meant to support the common C/C++ practice of implicity converting something to bool so we write something like if(t) ... to mean t "is_valid" or "is_not_null" or some such.  
  
a) I don't think this idiom is a good idea and even more so in the context of modern C++.  
  
Conversion of a Testable instance to a bool instance should result in something that looks/acts like a C bool - not something else like a void * which C then maps to a bool.  If we want to use a bool for something like "is_valid" we'd be much better off just implementing is_valid directly.  There would be then no confusion, side effects or hand a waving.  
  
b) it's an especially a bad idea for tribool.  
  
The motivating concept behind tribool is that of some sort of "extended" bool. The naming suggests that it acts like a bool.  But since we've used he safe_bool idiom, it doesn't any more.  That is we can't use a tribool anywhere a bool is used. So if we use operator bool we'll get a tribool which acts like a bool - even when the original usage of bool was a bad idea according to a) above.  But at least we have the same behavior for tribool and bool which is a lot more intuitive and less confusing.  
  
Also, changing to operator bool () will address the current problem with GCC not supporting a constexpr version of tribool.  
  
Accordingly, I've submitted this PR to change the implementation of tribool to avoid the safe_bool idiom.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-05-18 22:45:05 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-390351474  

Does this change the behavior of `tribool` in existing code?

---

## Comment 2

> Username: robertramey  
> Created_at: 2018-05-19 05:08:40 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-390379862  

It might.  
  
tribool t{true}  
tribool f{false}  
  
if(f < t) ...  
if(t >> 1) ...  
  
fail to compile currently but will compile with this change  
  
if(f == t) ...  
if(f != t) ...  
  
will continue to compile  
  
bool fx() {  
    return tribool{t};  
}  
  
will continue to compile and run as inspected.  
  
note that using idiom recommended for C++11:  
boost::tribool::operator explicit bool () const ...  
  
will fail to compile in this case whereas the current tribool will compile and run as expected.  
  
Robert Ramey

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-05-22 14:58:53 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-391022936  

From the discussion in the mailing list:  
  
Using (non-explicit) `operator bool` allows `t+f` which is not allowed for the safe-bool-idiom. Hence there is an additional source of potential bugs.  
  
This is not solved (still allowed) in C++17 (against claims in the mailing list):  https://godbolt.org/g/yYDBb4

---

## Comment 4

> Username: robertramey  
> Created_at: 2018-05-23 23:08:02 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-391528464  

I'm leaving this to the maintainer of this library - which I believe is the CMT to decide how to handle this. With the discussion on the mailing list, I believe that they have the information required to make a good decision.

---

## Comment 5

> Username: robertramey  
> Created_at: 2018-05-31 16:09:05 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-393584728  

The main objection to using this solution was the complaint that implicitly converting tribool to bool constitutes a "narrowing" conversion analogous to the conversion of an int64 to an int32.  When this was stated, I found it convincing.  But now that I think about it, I wonder.  The int example clearly would  silently inject an error in some cases.  But conversion of a tribool to a bool is actually legitimate.  It's mapping a tribool to the best match in the set of bool values. Information may be lost - but that is the purpose of the mapping.  So I don't think the "narrowing" conversion complaint is applicable here.  
  
I can go along with whatever the library maintainer decides.  Its not a holy war for me.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2018-05-31 20:22:30 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-393667384  

I challenge the assertion `But conversion of a tribool to a bool is actually legitimate.`  
Currently (and with `explicit bool()`) it is only possible to use tribool in a boolean context, which is a much narrower use case as converting to bool. But I agree that it does loose information in that case.  
  
I'd rather advertise usages like `if(indeterminate(tribool)) boom(); else if(tribool) ... ; else ...;`.     
`explicit bool` would allow this. The safe bool allows it too  but with a few drawbacks as you mentioned.  
  
In short: It should be avoided to loose information as much as possible. `implicit bool` does the opposite hence I'd decide against it.

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-07-05 01:31:05 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-402581632  

Please rebase on develop so a full CI suite can run on the change.  Thanks.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-10-13 04:11:01 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-429509366  

I added a rather simple test at the beginning of tribool_test.cpp which I expected to fail in all cases, based on my understanding of safe_bool (which is intended to force the developer to explicitly cast from tribool to bool to avoid potential loss of data):  
```  
  {  
    bool bx = x;  
    bool by = y;  
    bool bz = z;  
    BOOST_CHECK(bx == false);  
    BOOST_CHECK(by == true);  
    BOOST_CHECK(bz == false);  
  }  
```  
Without the changes in the PR, building in C++03 is successful (I did not expect this), and building in C++11 fails as expected:  
```  
tribool_test.cpp:20:10: error: no viable conversion from 'boost::logic::tribool' to 'bool'  
    bool bx = x;  
         ^    ~  
tribool_test.cpp:21:10: error: no viable conversion from 'boost::logic::tribool' to 'bool'  
    bool by = y;  
         ^    ~  
tribool_test.cpp:22:10: error: no viable conversion from 'boost::logic::tribool' to 'bool'  
    bool bz = z;  
```  
When I applied the changes in the pull request, the results were the same.  This leads me to believe that safe_bool is no longer necessary, perhaps does not do what it was intended to do; or perhaps I do not understand the original intention.  Given the overall sentiment on the mailing list regarding C++03 support, I'm inclined to merge this if it passes tests.  
  
It will also resolve the second part of https://svn.boost.org/trac10/ticket/5510, allowing me to close it, as I see nothing wrong with the truth tables.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2018-10-13 06:36:34 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-429515960  

@jeking3 Have you seen my comment above yours? What is your position regarding that?  
  
IMO tribool->bool is a loss of data and must not be implicit.

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-10-13 13:01:14 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-429540097  

@Flamefire if you look at a pull request running in my fork:  
  
https://github.com/jeking3/logic/pull/2  
  
I added a test (on top of the current develop, without this PR's contents) that initially I had expected to fail in all cases.  It fails (as expected) on all combinations of `cxxstd=11,14,17 cxxstd-dialect=iso,gnu toolset=gcc,clang` due to the recent addition of the `explicit` keyword on the conversion operator, thus prevent implicit conversions.  However with all variations of `cxxstd=03` it compiles successfully which means implicit conversion is happening with the definition of safe_bool as-is.  I further locally removed safe_bool all together and all conversions failed (even non-assignment conversions).  C++11 `explicit` keyword fixes this behavior properly.  That was added in March.  
  
Applying Robert's patch does nothing to change this behavior, therefore I agree with Robert that safe_bool is unnecessary and can be removed.  Given the explicit keyword is now the only difference, I would further reduce the code that's there slightly to:  
```  
  BOOST_CONSTEXPR  
#if !defined( BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS )  
  explicit  
#endif  
  operator bool () const BOOST_NOEXCEPT  
  {  
    return value == true_value;  
  }  
```

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-10-13 13:31:10 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-429542103  

Ugh, a force push to resolve the online merge closed this automatically.  I will open a new one.

---

## Comment 12

> Username: jeking3  
> Created_at: 2018-10-14 14:15:28 UTC  
> Url: https://github.com/boostorg/logic/pull/7#issuecomment-429629956  

So the end result of the discussion is that safe_bool is still useful for environment without explicit conversion operators, so we will keep safe_bool for C++03 environments.  Once someone moves up to C++11 it is no longer needed and is compiled out, including the "dummy" parts that were causing some trouble.  
  
Also it looks like I had inadvertantly removed your change in this PR with a push, the contents of which reappeared in #11 however, sorry about that.

---
