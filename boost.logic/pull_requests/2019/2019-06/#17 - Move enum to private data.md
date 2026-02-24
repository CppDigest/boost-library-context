# #17 Move enum to private data [Open]

> Username: fekir  
> Created at: 2019-06-29 04:20:07 UTC  
> Updated at: 2020-10-24 06:19:32 UTC  
> Url: https://github.com/boostorg/logic/pull/17  

As proposed in https://github.com/boostorg/logic/issues/16.  
  
It is an implementation detail, as following code compiles, but does not behave as expected:  
  
```  
boost::tribool v1 = boost::tribool::false_value;  
boost::tribool v2 = boost::tribool::true_value;  
boost::tribool v3 = boost::tribool::indeterminate_value _value;  
  
v1 == boost::tribool::false_value;  
v2 == boost::tribool::true_value;  
v3 == boost::tribool::indeterminate_value _value;  
```

---

## Comment 1

> Username: fekir  
> Created_at: 2019-06-29 11:42:16 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-506950932  

Could someone enlighten me on the failing test?  
  
In the output I can only see "failed-as-expected" or "passed"

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2019-07-01 10:31:18 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/logic/pull/17#pullrequestreview-256255420  

This library has been in use since Boost 1.32.0.  This is a breaking change made to cope with a sub-standard auto-complete in an editor.  I don't see the benefit to breaking everyone's usage out there.  
  
You could, possibly:  
  
1. Introduce the new method(s) and deprecate the direct use of the enum.  
2. Wait at least one release.  
3. Move the enum.  
  
However I'm still not convinced the change is worth the aggravation it will cause.

---

## Comment 3

> Username: fekir  
> Created_at: 2019-07-01 12:20:19 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507241255  

> This library has been in use since Boost 1.32.0. This is a breaking change made to cope with a sub-standard auto-complete in an editor. I don't see the benefit to breaking everyone's usage out there.  
  
I admit I still do not understand the failing test (I can see only "failed-as-expected" and "passed"), but is it really a breaking change?  
The enum is not described anywhere (at least I could not see it in the documentation), so for me it always seemed like an implementation detail.  
Wouldn't it be like changing something like inside `namespace detail`?  
  
It is not necessarily an editor issue.  
Someone wants to create a tribol with value indeterminate, one of the first obvious attempt would be to write something like `boost::tribool::indeterminate`.  
Auto-completion helps to find something appropriate (or just grepping in the header), the constructor of tribool accepts it...  
  
And does `boost::tribool tb = boost::tribool::indeterminate_value` or `void foo(boost::tribool); fun(boost::tribool::indeterminate_value)` really looks that wrong?  
  
Of course for someone that knows the library, and knows how all the implicit conversions will spot the error or even understand it, but for those using it occasionally or the first time it is not necessarily that easy.  
  
> You could, possibly:  
>   
>     1. Introduce the new method(s) and deprecate the direct use of the enum.  
  
Which new methods?  
In my PR I just changed the visibility.  
There is AFAIK no scenario where the direct usage of the enum is necessary.  
  
Deprecating is a good idea, I assume you are suggesting something like `enum [[deprecated("Use true, false and boost::indeterminate")]] value_t  {...};` as a first step.  
  
>     2. Wait at least one release.  
  
I have no problem with that.  
  
>     3. Move the enum.  
>  
> However I'm still not convinced the change is worth the aggravation it will cause.  
  
Could you enlighten me what the aggravation would be?  
  
Other approaches could be  
  
*  use `=delete` in C++ >=11 to delete the overload with the enum, but it is much more complicated than by avoiding the issue entirely  
* add an overloaded constructor that takes the enum, but I do not see a clear advantage instead of using `true`, `false` and `boost::indeterminate`.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-07-01 15:54:41 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507323602  

As the enumeration is currently public, folks may be using it.  Whether they should be using it or not is a different question.  I see your points, and I see no other references to indeterminate_value in boost other than the implementation and that the implementation detail is not documented.  
  
A change like this should probably wait until after the 1.71.0 release cycle which has already started.  
  
The codecov build failure is likely a function of not having updated to the latest boost-ci, which I will do now in a separate PR.

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-07-01 18:01:05 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507365522  

If you rebase on develop it will solve that one failing build.

---

## Comment 6

> Username: fekir  
> Created_at: 2019-07-02 18:07:50 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507786392  

Thank you.  
Should I do something else for having this change in the next release cycle?

---

## Comment 7

> Username: jeking3  
> Created_at: 2019-07-02 18:27:57 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507793532  

No, I'll just add a 2nd reviewer to verify things are sane.

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-07-02 18:51:05 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507801307  

This looks like something that needs to be fixed, although all direct uses of `.value` will break.

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-07-02 19:34:28 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-507815406  

Thanks for the review.  I guess we can document it as a breaking change for v1.72.0 and the resolution is to have consumers use the provided methods instead of accessing it directly.  This would be backwards compatible so they would be able to make this change and then if they had to be compatible with older boost releases, they still would be.

---

## Comment 10

> Username: fekir  
> Created_at: 2019-12-12 08:57:34 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-564912364  

Hello @jeking3 , boost 1.72.0 has been released (https://www.boost.org/users/history/version_1_72_0.html), but this change has not been merged.

---

## Comment 11

> Username: fekir  
> Created_at: 2020-02-18 19:56:52 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-587747903  

@jeking3 , @pdimov : any news?

---

## Comment 12

> Username: pdimov  
> Created_at: 2020-02-18 22:55:51 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-587945867  

> The enum is not described anywhere (at least I could not see it in the documentation), so for me it always seemed like an implementation detail.  
  
Actually, it is described.  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost/logic/tribool.html has  
  
```  
class tribool {  
public:  
  enum value_t;  
  // construct/copy/destruct  
  tribool() noexcept;  
  tribool(bool) noexcept;  
  tribool(indeterminate_keyword_t) noexcept;  
  
  // public member functions  
  explicit BOOST_CONSTEXPR operator bool() const noexcept;  
  
  // public data members  
  enum boost::logic::tribool::value_t value;  
};  
```  
  
and https://www.boost.org/doc/libs/1_72_0/doc/html/boost/logic/tribool/value_t.html has  
  
```  
enum value_t { false_value, true_value, indeterminate_value };  
```  
  
So this is not an implementation detail.

---

## Comment 13

> Username: fekir  
> Created_at: 2020-02-19 19:31:21 UTC  
> Updated_at: 2020-02-19 19:44:56 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-588405517  

@pdimov : you are right. I believe I meant that the documentation does not show anyhow how it interacts with the `boost::tribool` class/how it should get used, but I can't remember anymore.  
  
As far as I've understood @jeking3, the plan was to change the visibility for 1.72 to avoid the issues I've reported (I found this error at least in two projects).  
  
Since the change did not get merged, and I could also find no mention in the release notes, I wanted to know how we want to proceed.  
  
Alternative solutions (which IMHO are not as good as making the `enum` private), is avoiding the implicit conversions (in c++>=11) between the `enum` and the class.  
  
  
Edit: Some other data about the usage of the enum:  
  
I could not find any usage of `tribool::indeterminate_value` in the debian source code (except for the tribool class itself, source: https://codesearch.isocpp.org/cgi-bin/cgi_ppsearch?q=tribool%3A%3Aindeterminate_value&search=Search).  
  
I still believe (but I acknowledge that this is only my view), that the probability of any valid code to be broken is near zero.  
But also for valid broken code, there is a transition path, as the tribool interface is rich enough for providing all the necessary operations.

---

## Comment 14

> Username: pdimov  
> Created_at: 2020-02-19 19:56:44 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-588418578  

I think that a better way to fix that is to add a private converting constructor from value_t (and leave it undefined).  
  
(Or perhaps to add a converting constructor that does the right thing.)

---

## Comment 15

> Username: fekir  
> Created_at: 2020-02-19 20:01:51 UTC  
> Updated_at: 2020-02-19 20:04:21 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-588420971  

Yes, this would be a possible approach (was thinking `= delete` in c++ >= 11 instead of private)  
  
I guess I'm still questioning what are valid use-cases for the `enum` in the user-code, and find thus a better design decision to make it private.  
It also reduces the complexity of the class (less code to write/mantain/document/...) with no drawbacks, on the contrary it does prevent bugs.  
  
Edit: making the constructor private or `=delete` seems wrong, because the two entities has some common concept (represent same type of information, and share similar names), but there is no way to make the conversion in both directions, only from the class through `.value` to the `enum`, but not the other way.

---

## Comment 16

> Username: fekir  
> Created_at: 2020-10-24 06:19:32 UTC  
> Url: https://github.com/boostorg/logic/pull/17#issuecomment-715774537  

Up

---
