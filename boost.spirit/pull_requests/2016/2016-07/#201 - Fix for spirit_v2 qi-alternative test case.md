# #201 Fix for spirit_v2 qi/alternative test case [Merged]

> Username: mxc-commons  
> Created at: 2016-07-28 02:46:00 UTC  
> Updated at: 2017-08-31 01:18:10 UTC  
> Merged at: 2016-08-17 22:47:38 UTC  
> Closed at: 2016-08-17 22:47:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/201  

For me it looks like an issue involving the type traits `not_is_variant` and `not_is_optional`. `boost::optionals` which contain a `boost::variant`  (`boost::optional<boost::variant>`) are treated as variants, i.e. `not_is_variant` gives `mpl::false_`.   
  
This leads to `call_optional_or_variant` gets called with `mpl::false_` which seems to be incorrect for this type.   
  
Because `not_is_variant` and `not_is_optional` are not mutually exclusive in that context, I changed that to continue with `mpl::true` if the Attribute is an optional.  
  
With that change the test case compiles.   
  
But I am actually not sure about the intention behind treating optionals as variants by the traits. If this design produces side effects qi relies on, my change might be not sufficient.   
  
The spirit_v2/qi test suite compiles successfully and all tests succeed now. So I assume, there are no side effects which stopped working.  
  
I corrected a typo in a comment, too.  
  
I did not look after the errors karma and lex produce. I am not into those right now.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-07-28 09:57:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-235851954  

Seems that:  
  
```  
template <typename T, typename Domain>  
struct not_is_variant<boost::optional<T>, Domain>  
  : not_is_variant<T, Domain>  
{};  
```  
  
Is indeed suspicious. I don't recall what lead to that, but have you tried removing that instead? Does it lead to other errors?

---

## Comment 2

> Username: mxc-commons  
> Created_at: 2016-07-28 15:37:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-235933523  

Yes, that's where it comes from. But simply removing Holy Code? I did not even consider that yet. ;)  
  
> Am 28.07.2016 um 11:57 schrieb Joel de Guzman notifications@github.com:  
>   
> Seems that:  
>   
> template <typename T, typename Domain>  
> struct not_is_variantboost::optional<T, Domain>  
>   : not_is_variant<T, Domain>  
> {};  
> Is indeed suspicious. I don't recall what lead to that, but have you tried removing that instead? Does it lead to other errors?  
>   
> —

---

## Comment 3

> Username: mxc-commons  
> Created_at: 2016-07-28 20:55:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236022511  

But seriously: `not_is_variant` gets referenced from all over the place. Because `alternative_function` is the only place which causes a problem, I preferred that local fix.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-07-28 21:38:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236033161  

Same here, but I'd like to know the reasons why.

---

## Comment 5

> Username: djowel  
> Created_at: 2016-07-28 21:39:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236033445  

Actually, thinking about it, although a local fix is the easy way out, it might not be the correct solution and we might be propagating something that is fundamentally wrong.

---

## Comment 6

> Username: mxc-commons  
> Created_at: 2016-07-28 22:34:13 UTC  
> Updated_at: 2016-07-28 22:35:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236045397  

I do not agree. The error occurs in `alternative_function` which is used only by alternative.hpp. If test cases for the alternative operator are carefully designed and complete and all pass, we can be somewhat confident that this does not get propagated wrong anywhere.  
  
On the other hand, changing things globally by removing that special `optional<variant>` treatment is likely to break something somewhere. I do not know that, but I guess, the original author did implement that because it was required somewhere else. At that place that behaviour is needed and would be broken.  
  
Btw, I actually do not like _lying names_. Something called `not_is_variant` should detect in particular that and nothing else.  
  
Who originally authored that? We should ask that guy what the purpose was that he did it that way. Without knowing that I'd vote for the local fix. Impact can be much better and easier controlled.

---

## Comment 7

> Username: djowel  
> Created_at: 2016-07-28 22:36:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236045893  

That author is me ;-) and I no longer remember the reason why. That is why it is pertinent to get to the bottom of it.

---

## Comment 8

> Username: djowel  
> Created_at: 2016-07-28 22:37:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236046095  

I also do not like lying names. So touché ;-)

---

## Comment 9

> Username: mxc-commons  
> Created_at: 2016-07-28 22:40:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236046624  

That was you? omg ;-)

---

## Comment 10

> Username: djowel  
> Created_at: 2016-07-28 22:43:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047098  

yeah! LOL. but don't rub it in :-) I'm already green in embarrassment ;P

---

## Comment 11

> Username: djowel  
> Created_at: 2016-07-28 22:44:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047363  

OK, anyway, since I'm the one guilty, It's my responsibility to find out why and try to get a fix.

---

## Comment 12

> Username: mxc-commons  
> Created_at: 2016-07-28 22:44:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047404  

Trying to give some consolation:   
  
I think there were never ever any user complaints about that. That means that nobody ever used an optional alternative.

---

## Comment 13

> Username: mxc-commons  
> Created_at: 2016-07-28 22:46:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047661  

Ok. :) So can I close this?

---

## Comment 14

> Username: djowel  
> Created_at: 2016-07-28 22:46:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047761  

I think the proper fix is to do something like (pseudo) is_variant || (is_optional && optional::enclosed_type is_variant)

---

## Comment 15

> Username: djowel  
> Created_at: 2016-07-28 22:47:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236047853  

No, please don't. It's good to have it here. I'll look into it asap.

---

## Comment 16

> Username: mxc-commons  
> Created_at: 2016-07-28 22:54:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236049150  

Ok. I will keep the fix active in my fork to have at least the spirit_v2/qi testsuite compile.

---

## Comment 17

> Username: mxc-commons  
> Created_at: 2016-07-28 22:59:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236050062  

Your pseudo: It should return mpl::true_ if it is an optional with embedded variant and mpl::false_ if it is a pure variant (at the place I fixed it). Cause we are in the alternative operator we should have a variant anyway, shouldn't we? But it could be an embedded one.

---

## Comment 18

> Username: mxc-commons  
> Created_at: 2016-07-28 23:00:55 UTC  
> Updated_at: 2016-07-28 23:01:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236050313  

I hope, I am not wrong with true and false. These not_ thingies just twist my brain. :)

---

## Comment 19

> Username: djowel  
> Created_at: 2016-07-28 23:02:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236050596  

One approach is to have the traits logic in the alternative implementation as an implementation detail, with a clear comment on the rationale. that way, we can have this fix sooner rather than later.

---

## Comment 20

> Username: djowel  
> Created_at: 2016-07-28 23:03:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-236050725  

that is what i am going too once i see it break when i "fix" the optional-variant thing.

---

## Comment 21

> Username: jpcanepa  
> Created_at: 2017-08-30 17:56:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-326069863  

I'm sorry to comment in this issue that is already closed, but I'm not versed enough in the implementation and I need to know if this is related to [the trac reported issue #12349](https://svn.boost.org/trac10/ticket/12349).  
  
In there I've (recently, thought the issue is around a year old) added a very small grammar that fails to compile with gcc 6.2 on boost 1.63 (since boost 1.61). This conversation seems very related to what I'm seeing (a problem around alternative_function), but as I said, I'm not savvy enough as to make sure if the problems _are_ the same.   
  
I did take this change and applied it over a 1.63 version and the error changes (now failing in   
  
`../../../snps/include/boost/spirit/home/qi/detail/assign_to.hpp:44:16: error: no type named ‘value_type’ in ‘class snps_boost_1_63_0::variant<int, std::__cxx11::basic_string<char, std::char_t  
raits<char>, std::allocator<char> > >’  
         struct is_container_of_ranges`  
  
I'm having serious trouble with an upcoming release due to this, so any help would be appreciated.

---

## Comment 22

> Username: djowel  
> Created_at: 2017-08-30 23:45:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-326149514  

pardon previous comment. I didn't read enough. OK, let's see what we can do. First I need to understand the test code. Let's continue discussion in the Trac ticket.

---

## Comment 23

> Username: djowel  
> Created_at: 2017-08-31 01:18:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/201#issuecomment-326161961  

OK, seems this is is already fixed in the latest devel which should already make it to the latest Boost release. See line 141  
alternative_function.hpp  
  
// fix for alternative.cpp test case, FHE 2016-07-28

---
