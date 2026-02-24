# #51 Implicit string view cast [Closed]

> Username: cmazakas  
> Created at: 2018-09-29 17:01:15 UTC  
> Updated at: 2025-10-24 04:35:52 UTC  
> Closed at: 2020-06-16 19:47:07 UTC  
> Url: https://github.com/boostorg/utility/pull/51  

This patch allows `boost::basic_string_view` to now seamlessly interop with `std::basic_string_view`.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-09-29 17:15:39 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425660930  

As I've said when this was proposed before, I don't really like establishing a dependency between the two.    
  
Also, I'm worried about ambiguity.

---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2018-09-29 17:23:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/utility/pull/51#pullrequestreview-160087526  

📁 include/boost/utility/string_view.hpp

```diff
 115 |+ #ifndef BOOST_NO_CXX17_HDR_STRING_VIEW
 116 |+     BOOST_CONSTEXPR basic_string_view(std::basic_string_view<charT, traits> view)
 117 |+         : ptr_(view.data()), len_(view.max_size()) {}
```

> Username: glenfe  
> Created_at: 2018-09-29 17:19:43 UTC  
> Updated_at: 2018-09-29 18:12:32 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r221434429  

This is wrong. It should be size() not max_size()

> Username: vinniefalco  
> Created_at: 2020-06-16 14:49:44 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r440911353  

THE DIAL GOES TO 11


---

## Comment 3

> Username: cmazakas  
> Created_at: 2018-09-29 17:48:39 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425663397  

> As I've said when this was proposed before, I don't really like establishing a dependency between the two.  
> Also, I'm worried about ambiguity.  
  
Ambiguity concerns are justified.  
  
As far as establishing a dependency between the two goes, it's a necessary evil in my opinion.  
  
By leveraging feature detection macros, we're able to properly give `boost::string_view` conditional support for `std::string_view` which eases the interop between pre-C++17 code and modern C++17 and beyond.  
  
I think this is the best move for users of `boost::string_view` as they can begin writing new libraries with `std::string_view` and still integrate with existing libs. This also enables users of `boost::string_view` to easily interop with any other libs that use `std::string_view`.  
  
In essence, this implicit conversion enables better meshing of different APIs and saves users from having to write and maintain their own conversions. It increases Utility's viability as a Boost library by not shoe-horning users into the Boost-flavor of `string_view`.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-09-29 18:16:11 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425665212  

> As far as establishing a dependency between the two goes, it's a necessary evil in my opinion.  
  
And there we disagree.   
  
> In essence, this implicit conversion enables better meshing of different APIs and saves users from having to write and maintain their own conversions. It increases Utility's viability as a Boost library by not shoe-horning users into the Boost-flavor of `string_view`.  
  
I have no problems with users writing and maintaining their own conversions.  
This allows *them* to decide if such a dependency is desired, rather than boost mandating one.

---

## Comment 5

> Username: glenfe  
> Created_at: 2018-09-30 00:20:34 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425683919  

Ultimately this is Marshall's decision. I marked my Review as Approved after you fixed the max_size() issue that I raised.

---

## Comment 6

> Username: cmazakas  
> Created_at: 2018-09-30 15:26:34 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425728815  

> This allows them to decide if such a dependency is desired, rather than boost mandating one.  
  
Well, it's only a big deal if we get it wrong.  
  
Currently, it seems like most would prefer the mandate:  
https://github.com/boostorg/utility/issues/40  
https://github.com/boostorg/utility/issues/47  
  
Even going by informal conversations I've had with other Boost users (even some of the really good and famous ones), the fact that they must write their own conversion has proven irksome.  
  
> I'd rather not require a dependency on std::string_view in boost::string_view.  
  
You may view this dependency as a hinderance or an undue burden on Boost. But in all reality, this implicit back-and-forth enables people to use Boost and modern `std::string_view`-based APIs seamlessly and easily (which is a massive benefit for Boost users). My best example of this is Beast which uses `boost::string_view` heavily.  
  
If a user _did_ write a conversion function or operator, they'd have to carry it with them to every new project and what's worse is, C++ currently doesn't have a good way of sharing code so every person's implementation is subtly different and/or subtly wrong.  
  
If there are edge cases that are concerning, I vote we translate them into tests and see how the code behaves and then we also have living documentation of those cases as well.

---

## Comment 7

> Username: mclow  
> Created_at: 2018-09-30 20:24:44 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-425749204  

> > I'd rather not require a dependency on std::string_view in boost::string_view.  
>   
> You may view this dependency as a hinderance or an undue burden on Boost.   
  
Sorry; you've gotten this backwards.  
The (proposed) dependency not an burden on Boost; but rather a burden on the people who use boost.   
  
> If a user _did_ write a conversion function or operator, they'd have to carry it with them to every new project and what's worse is, C++ currently doesn't have a good way of sharing code so every person's implementation is subtly different and/or subtly wrong.  
  
This comment is deeply ironic given that it's about a bunch of code (Boost) that many people carry with them to every new project (and shared among lots of users)  
  
It's OK for users to write their own libraries.

---

## Comment 8

> Username: cmazakas  
> Created_at: 2018-10-01 18:19:16 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426010112  

> It's OK for users to write their own libraries.  
  
Yes, it is. For example, Beast is most appropriately used for writing higher-level libs. Yap is useful for creating DSLs. The list could likely go on and on.  
  
However, users are not using Utility in this case to write a library. They're adding a patch to Utility to make it usable "in the real world".  
  
> This comment is deeply ironic given that it's about a bunch of code (Boost) that many people carry with them to every new project (and shared among lots of users)  
  
This is code I'm aiming to obsolete because it's a duplicated effort that we can solve at the source. If 1000 teams added conversions for `boost::string_view` and `std::string_view`, I aim to obsolete the code of 1000 teams.  
  
> The (proposed) dependency not an burden on Boost; but rather a burden on the people who use boost.  
  
This can be quantified in a few different ways.  
  
The most obviously burdened people are the ones who wrote a non-semantic conversion between `boost::string_view` and `std::string_view` otherwise, they'd be doing the logical equivalent of the patch I'm proposing to merge in.  
  
The next are those that _don't_ want the implicit conversion and would prefer to have the type errors at an API level. In this case, we do actively harm them and there's not much I can do to defend that stance.  
  
In my anecdotal and personal experience, I've wanted the opposite of a type error and _want_ the conversion. There are even two issues who mention the same desire. And there is no contestation on these issues claiming that the type mismatch is a feature and not a deficiency.  
  
There is also a precedence set by `boost::system::error_code` which also supports an implicit conversion to `std:error_code`. My patch is well in line with other aspects of what the Boost libraries have done.  
  
Boost and the STL have a special relationship and should live in harmony with each other. Implicit conversions between relevant types make it easy for Boost to interop with modern libs that were coded only with the STL in mind.

---

## Comment 9

> Username: Lastique  
> Created_at: 2018-10-01 20:21:16 UTC  
> Updated_at: 2018-10-01 20:21:36 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426048942  

@mclow Could you expand a little on what dependency poses concern and why?  
  
If it's `#include <string_view>` then I think that dependency likely already exists through `<string>`. If not the conversion to `std::basic_string`, it would be beneficial to just replace `#include <string>` with `#include <string_view>` where possible as the latter is more lightweight and still provides `std::char_traits`.

---

## Comment 10

> Username: mclow  
> Created_at: 2018-10-01 21:01:14 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426061454  

You can start off by solving this:  
  
	#include <boost/config.hpp>  
  
	#ifndef BOOST_NO_CXX17_HDR_STRING_VIEW  
	#include <string_view>  
	#endif  
  
	int main () {  
	#ifndef BOOST_NO_CXX17_HDR_STRING_VIEW  
		std::string_view sv;  
	#endif  
	}  
  
When will this code fail to compile?  
(Longer posting coming soon)

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-10-01 21:22:03 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426067457  

Provided that Boost.Config doesn't lie, it won't fail. But I don't think I understand, why should it fail to compile and how is it related?

---

## Comment 12

> Username: cmazakas  
> Created_at: 2018-10-01 21:40:43 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426072704  

Was going to say, this patch is predicated on the feature detection macro working properly. We only enable the constructors and conversion operator in the case that the feature is detected. Barring the user doing wonky things with macros directly, the code _should_ just work.  
  
But I am interested in fleshing out the test suite at this stage of development and attempting to create something robust.

---

## Comment 13

> Username: mclow  
> Created_at: 2018-10-01 22:00:51 UTC  
> Updated_at: 2018-10-01 22:01:03 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426078696  

> Provided that Boost.Config doesn't lie, it won't fail. But I don't think I understand, why should it fail to compile and how is it related?  
  
Boost.Config doesn't lie. What it says is "there exists a `<string_view>` header."  
Try it yourself.  
  
I used gcc 7.3.0: `gcc/gcc-7.3.0/bin/bin/g++ -I $BOOST boost.cpp`

---

## Comment 14

> Username: cmazakas  
> Created_at: 2018-10-01 22:14:13 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426082070  

Hmm, we may have an issue then, Mr. Clow.  
  
https://travis-ci.org/boostorg/utility/jobs/435051423  
  
Seems to show that gcc-7.3.0 passes. I'm away from an easy install of gcc 7.3 right now but if you claim it fails on your machine but it passes on Circle, there's a disconnect and one of the two are wrong.  
  
_Or_, surprise twist, they're both right and we've discovered a Heisenbug!

---

## Comment 15

> Username: glenfe  
> Created_at: 2018-10-01 22:17:56 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426082897  

Marshall has exposed an issue in Config: `BOOST_NO_CXX17_HDR_STRING_VIEW` should be defined in certain cases, where it is currently not defined.

---

## Comment 16

> Username: Lastique  
> Created_at: 2018-10-01 22:23:26 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426084180  

I must be missing something because on my system with gcc 7.3 it compiles, both in C++03 and C++17. Note that `BOOST_NO_CXX17_HDR_STRING_VIEW` is defined for C++ <17.  
  
In any case, if there is a problem in Boost.Config then let's fix it. I don't see how it is related to the dependency issue that was raised before.

---

## Comment 17

> Username: glenfe  
> Created_at: 2018-10-01 22:27:44 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426085261  

The  intent of `BOOST_NO_CXX17_HDR_STRING_VIEW` is to only be not-defined when all of the following are true:   
* In C++1z or higher mode (C++17 or higher)  
* It is valid to #`include <string_view>`  
* There is a conforming implementation of `std::string_view` in `<string_view>`  
  
It is not a very meaningful macro otherwise. To discover that `BOOST_NO_CXX17_HDR_STRING_VIEW` is not-defined for libstdc++ when in pre-C++17 mode, where the third bullet point in the list above is certainly not true, should be treated as a defect in Config.

---

## Comment 18

> Username: Lastique  
> Created_at: 2018-10-01 22:27:58 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426085300  

> What it says is "there exists a `<string_view>` header."  
  
It says there is a *useful* `<string_view>`. Which means it must be defined when the header exists but cannot be used or too broken.

---

## Comment 19

> Username: Lastique  
> Created_at: 2018-10-01 22:32:19 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426086232  

> To discover that BOOST_NO_CXX17_HDR_STRING_VIEW is not-defined for libstdc++ when in pre-C++17 mode, where the third bullet point in the list above is certainly not true, should be treated as a defect in Config.  
  
But it is defined, so there is no defect.

---

## Comment 20

> Username: glenfe  
> Created_at: 2018-10-01 22:36:43 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-426087169  

> But it is defined, so there is no defect.  
  
Perfect. I have not checked, but assumed that Marshall had, when he raised his concern. If it is defined then the macro works as intended.

---

## Comment 21

> Username: cmazakas  
> Created_at: 2018-10-04 16:04:29 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-427075658  

It's been a few days.   
  
I can look into the failure on Travis and resolve the compiler issue but I was wondering what the status of this PR is.  
  
I'd hate to spend the time fixing an issue that will ultimately not be merged in.

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-12-18 23:00:46 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-448404013  

I don't understand the problem either. What are we concerned about?

---

## Comment 23

> Username: cmazakas  
> Created_at: 2018-12-18 23:24:40 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-448410088  

The problem is admittedly a small one.  
  
Namely, just save the user from having to type:  
```cpp  
auto const my_hella_sick_view  = std::string_view(boost_view.begin(), boost_view.size());  
```  
  
Increasing the inter-op between `boost::string_view` and `std::string_view` makes writing C++14 libraries that are consumed by C++17 users easier. We're in a grey messy area where C++14 is still prominent and many libs were written using what Boost offered at the time.  
  
The overall goal is to make the developer's time using `boost::string_view` as nice as possible.  
  
Right now, libs have come up with solutions like configuration macros that will simply redefine the `string_view` type used. This works in practice but I think it makes more work for library authors.  
  
And when library authors don't take this challenge upon themselves, users are forced to deal with it themselves.  
  
It's a small change that goes a long way towards user experience.

---

## Comment 24

> Username: pdimov  
> Created_at: 2018-12-18 23:34:04 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-448412292  

No, I meant that I don't understand the concern about the optional dependency on `<string_view>` that is blocking this.

---

## Comment 25

> Username: pdimov  
> Created_at: 2018-12-23 16:19:28 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-449647028  

@mclow If this program fails on some configurations, then https://github.com/boostorg/config/blob/develop/test/boost_no_cxx17_hdr_string_view.ipp needs to be modified to declare variables of `string_view` types, instead of just `using` declarations.

---

## Comment 26

> Username: AndWass  
> Created_at: 2020-04-14 14:10:11 UTC  
> Updated_at: 2020-04-14 14:10:37 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-613465925  

This would be a nice quality-of-life increase. For instance Beast defaults to `boost::string_view` while Asio uses `std::string_view` and right now I have to care about these small, and IMO insignificant, details that I really thought "just works"...

---

## Review 27 [Commented]

> Username: Lastique  
> Created_at: 2020-04-14 15:32:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/51#pullrequestreview-393046788  

📁 include/boost/utility/string_view.hpp

```diff
 114 | 
 115 |+ #ifndef BOOST_NO_CXX17_HDR_STRING_VIEW
 116 |+     BOOST_CONSTEXPR basic_string_view(std::basic_string_view<charT, traits> view)
```

> Username: Lastique  
> Created_at: 2020-04-14 15:32:17 UTC  
> Updated_at: 2020-04-14 15:32:18 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r408231632  

Inconsistent indentation.

> Username: Lastique  
> Created_at: 2020-04-14 15:33:41 UTC  
> Updated_at: 2020-04-14 15:33:42 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r408232639  

Also in `test/string_view_test3.cpp`.


---

## Review 28 [Commented]

> Username: pdimov  
> Created_at: 2020-04-15 17:50:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/51#pullrequestreview-394001580  

📁 test/string_view_test3.cpp

```diff
  22 |+ 
  23 |+ // Test support for construction and assignment from std::basic_string_view
  24 |+ void test_string_view_construction()
```

> Username: pdimov  
> Created_at: 2020-04-15 17:50:43 UTC  
> Updated_at: 2020-04-15 17:50:44 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r409025476  

This function isn't called.


---

## Comment 29

> Username: Lastique  
> Created_at: 2020-06-15 15:57:33 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644221336  

@mclow Do you still have objections to a change like this PR?  
  
If not, I could handle the merging with corrections.

---

## Comment 30

> Username: mclow  
> Created_at: 2020-06-15 19:52:35 UTC  
> Updated_at: 2020-06-15 19:52:49 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644343983  

I haven't heard any arguments other than "I really want this", so yes, I continue to believe that this is not something that boost should support.   
  
On a lark, I decided to look look elsewhere in boost for other examples. `boost::any` is not convertible to/from `std::any`. `boost::variant` is not convertible to/from `std::variant`. Same with `boost::variant2`, `boost::optional` and `boost::tuple`.

---

## Comment 31

> Username: cmazakas  
> Created_at: 2020-06-15 20:13:45 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644354808  

> I haven't heard any arguments other than "I really want this", so yes, I continue to believe that this is not something that boost should support.  
  
Tbf, I do really want it.  
  
But what's more important is that libs today, including those being proposed to Boost, will use macros to toggle using `std::string_view` or `boost::string_view`. This can be problematic when exposed at an ABI boundary.  
  
We should aim for stable ABIs that rarely (if ever) shift with C++ standard version. By making the conversion transparent at an API level, we avoid many potential ABI issues.  
  
> On a lark, I decided to look look elsewhere in boost for other examples. boost::any is not convertible to/from std::any. boost::variant is not convertible to/from std::variant. Same with boost::variant2, boost::optional and boost::tuple.  
  
You know what this means, right? It means I need to make more PRs.

---

## Comment 32

> Username: pdimov  
> Created_at: 2020-06-15 20:30:26 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644371975  

The argument is that `string_view` is an interface class, used as a parameter or as a return value. C++17 code using `std::string_view` has trouble using a Boost library using parameters or return values of type `boost::string_view`, and there's no good alternative because due to ABI/ODR issues, the library can't just switch to `std::string_view` when under C++17.

---

## Comment 33

> Username: mclow  
> Created_at: 2020-06-15 20:39:48 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644376833  

> You know what this means, right? It means I need to make more PRs.  
  
Why? Are people clamoring for this? I think not.

---

## Comment 34

> Username: mclow  
> Created_at: 2020-06-15 20:41:06 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644377511  

> The argument is that `string_view` is an interface class, used as a parameter or as a return value.   
  
Same argument applies to `any`, it seems to me - and yet no one is kvetching about _that_.

---

## Comment 35

> Username: pdimov  
> Created_at: 2020-06-15 20:50:10 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644382133  

Yes, nobody's "kvetching" about `any`. People are "kvetching" about `string_view`, because it's a real problem for them, because their libraries take and return `boost::string_view` and their users are complaining about not being able to pass `std::string_view` or store the result in one.

---

## Comment 36

> Username: mclow  
> Created_at: 2020-06-15 20:55:55 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644384677  

Peter, you left out "without writing a one-line conversion function" in your response. In #40, we have someone complaining that he's had to write (ok, copy/paste) this conversion routine _four whole times_

---

## Comment 37

> Username: pdimov  
> Created_at: 2020-06-15 21:16:29 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644394783  

You also have to call that conversion function everywhere you pass one string_view to another. This is a common complaint that is getting more and more frequent as C++17 is getting more and more widespread; it's not going to go away.

---

## Comment 38

> Username: pdimov  
> Created_at: 2020-06-15 21:38:44 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644404303  

That's actually in the spirit of `string_view`, whose whole point was to be able to implicitly convert from any contiguous sequence of characters so as to be usable as a universal parameter type for functions taking string sequences. Since `std::string_view` is a contiguous sequence of characters, `boost::string_view` should be constructible from it; and conversely, since `boost::string_view` is a contiguous sequence of characters, `std::string_view` should be constructible from it. Except it isn't, but that's arguably a defect in the standard owing to the lack of concepts when it was added.

---

## Comment 39

> Username: mclow  
> Created_at: 2020-06-15 22:39:56 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644425883  

> This is a common complaint that is getting more and more frequent as C++17 is getting more and more widespread; it's not going to go away.  
  
I really hope it does go away; as more and more people have access to `std::string_view`, less and less people should be using `boost::string_view`

---

## Comment 40

> Username: Flamefire  
> Created_at: 2020-06-16 07:49:21 UTC  
> Updated_at: 2020-06-16 14:52:35 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644596400  

> less and less people should be using boost::string_view  
  
Great. If only most of Boost libs could require C++17 now less and less people would be using `boost::string_view.` To quote a very recent case: A Boost library wanting to support C++11 to 17/20 needs a member of type `string_view`. Which one should it use? It can't conditionally use either boost or std because that would lead to ODR violations. It can't use boost only as that would be a burden on C++17 users. Having `boost::string_view` convert automatically to `std::string_view` would solve the latter.  
  
> boost::any is not convertible to/from std::any. boost::variant is not convertible to/from std::variant. Same with boost::variant2, boost::optional and boost::tuple.  
  
The main difference: It is trivial to do and likely does not even generate any machine code. For all other types mentioned some real conversion is very likely to happen. For string_view being a pointer and size in all known implementations no real conversion happens and even if it was it would be a trivial copy.  
And the other difference: People write that conversion code often enough to create 2 issues to add that conversion to boost::string_view  
  
> I haven't heard any arguments other than "I really want this"  
  
Why is that not a valid reason? People writing the same code over and over again because they "really want this" behavior is a very valid motivation to put that piece of code in a library. And the most natural one is this one.  
  
Answer by @mclow   
> Not when I have elaborated my objections (several times, in several fora), and the people I am referring to never bother to address them, just respond with "but I really want this!"

---

## Comment 41

> Username: Lastique  
> Created_at: 2020-06-16 08:40:56 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644623467  

> Peter, you left out "without writing a one-line conversion function" in your response.  
  
Speaking on one-liners, I'll note that the one in https://github.com/boostorg/utility/pull/51#issuecomment-448410088 is not correct. You should not use `begin()` to construct a `string_view` as it may not be a pointer. This is saying that one-liners are error prone.

---

## Comment 42

> Username: cmazakas  
> Created_at: 2020-06-16 14:38:43 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644807675  

> Not when I have elaborated my objections (several times, in several fora), and the people I am referring to never bother to address them, just respond with "but I really want this!"  
  
I don't think I've been privy to such objections before. For those of us not in the know, what's the abbreviated summary?

---

## Comment 43

> Username: madmongo1  
> Created_at: 2020-06-16 14:41:43 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644809435  

> > This is a common complaint that is getting more and more frequent as C++17 is getting more and more widespread; it's not going to go away.  
>   
> I really hope it does go away; as more and more people have access to `std::string_view`, less and less people should be using `boost::string_view`  
  
For the timebeing, when using boost (which I probably always will), my programs will always have to contend with the coexistance of boost and std types.   
  
Seamless interop would be greatly appreciated.  
  
This longstanding and avid user of Boost and latest C++ implores you, please accept the PR.

---

## Comment 44

> Username: Flamefire  
> Created_at: 2020-06-16 14:47:04 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644812688  

@mclow Did you edit my comment on purpose or on accident? Would you mind to restore it? I believe it contains valid arguments about having this `std` support here although it isn't in many other boost types. And reading this again I feel they address the arguments brought up here quite well

---

## Comment 45

> Username: mclow  
> Created_at: 2020-06-16 14:48:53 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644813833  

@Flamefire - I did not mean to edit your comment. I thought that I was editing my response.  I apologize.

---

## Comment 46

> Username: Flamefire  
> Created_at: 2020-06-16 14:56:49 UTC  
> Updated_at: 2020-06-16 15:10:17 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644818595  

Ok I changed that back to original and added your answer below.  
  
I'd really like to understand your objection. The only one I could find here is that it introduces a dependency from `boost::string_view` to `std::string_view` but I haven't found an answer on what kind of dependency the problem is and why exactly it is a problem. You posted some code at https://github.com/boostorg/utility/pull/51#issuecomment-426061454 but that is reported as working as intended. I was searching for the "longer posting" announced there but it seems that went missing too, or was that somehwere else? What is the problem with the code?  
  
For reference: https://godbolt.org/z/FzhNfo using Boost 1.69, the version where this macro was introduced. All later boost version work as well as far as I can tell.

---

## Comment 47

> Username: vinniefalco  
> Created_at: 2020-06-16 14:59:11 UTC  
> Updated_at: 2020-06-16 14:59:51 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644820068  

Why can't the constructor use template metaprogramming to construct from any type that has these member functions and nested types:  
```  
using value_type = CharT;  
CharT const* data() const noexcept;  
std::size_t size() const noexcept;  
```  
Then, there is no dependence on `std::basic_string` or `std::basic_string_view`.

---

## Comment 48

> Username: Flamefire  
> Created_at: 2020-06-16 15:09:12 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644826134  

@vinniefalco This only solves one half of the problem: Converting std->boost, but not the other way round

---

## Review 49 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-16 15:14:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/51#pullrequestreview-431615191  

📁 include/boost/utility/string_view.hpp

```diff
 146 | 
 147 |+ #ifndef BOOST_NO_CXX17_HDR_STRING_VIEW
 148 |+         operator std::basic_string_view<charT, traits>() const {
```

> Username: vinniefalco  
> Created_at: 2020-06-16 15:14:57 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r440930848  

should this be `noexcept`?

> Username: cmazakas  
> Created_at: 2020-06-16 15:23:52 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r440939022  

Good catch, it really should be.  
  
This whole PR likely needs a lot of cleanup and fixes to get back into a good state

> Username: Flamefire  
> Created_at: 2020-06-16 15:28:33 UTC  
> Url: https://github.com/boostorg/utility/pull/51#discussion_r440942446  

while we are at it: it can be constexpr too. Also the closing brace is misindented


---

## Comment 50

> Username: pdimov  
> Created_at: 2020-06-16 15:16:09 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644830247  

> Why can't the constructor use template metaprogramming to construct from any type that has these member functions and nested types:  
  
That's how I would have designed it; but in my imaginary design there wouldn't have been a Traits parameter. With the status quo, it's not clear whether basic_string_view<E, Tr1> should convert to basic_string_view<E, Tr2>, as will happen if we go with construction from any StringLike.

---

## Comment 51

> Username: Lastique  
> Created_at: 2020-06-16 15:44:46 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644847331  

Guys, given that Marshall is not convinced, there is no chance getting this merged. We might as well close this PR and the related issue as wontfix.

---

## Comment 52

> Username: madmongo1  
> Created_at: 2020-06-16 16:33:19 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644874470  

> @vinniefalco This only solves one half of the problem: Converting std->boost, but not the other way round  
  
You only need to be able to convert to boost from std. Any >= C++17 program is going to use std::string_view by default. The conversion is only needed for interop with boost libraries which demand boost::string_view (which unless I am mistaken, is only Beast!)

---

## Comment 53

> Username: vinniefalco  
> Created_at: 2020-06-16 16:34:23 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644875467  

> which unless I am mistaken, is only Beast  
  
And Boost.JSON, and Boost.URL, etc

---

## Comment 54

> Username: Flamefire  
> Created_at: 2020-06-16 16:36:03 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644877383  

> You only need to be able to convert to boost from std. Any >= C++17 program is going to use std::string_view by default.  
  
And what about those boost libraries wanting to return a `string_view`?

---

## Comment 55

> Username: madmongo1  
> Created_at: 2020-06-16 17:34:06 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644907197  

> > You only need to be able to convert to boost from std. Any >= C++17 program is going to use std::string_view by default.  
>   
> And what about those boost libraries wanting to return a `string_view`?  
  
A string_view as a return type is problematic. It doesn't automatically convert to anything and it creates a dangling reference by default.  
  
I would discourage it.

---

## Comment 56

> Username: cmazakas  
> Created_at: 2020-06-16 18:06:37 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644923221  

> Guys, given that Marshall is not convinced, there is no chance getting this merged. We might as well close this PR and the related issue as wontfix.  
  
Never say never, imo. So far, we haven't heard a "no", we just need to make a more convincing case. If such things as ABI stability are not sufficient, we may need to just think a little bit harder. If there's one thing I know that users love, it's definitely subtle ABI differences caused by libs being compiled with different standards of C++.  
  
> A string_view as a return type is problematic. It doesn't automatically convert to anything and it creates a dangling reference by default.  
  
Depends what the input is. If you pass in something like a `std::string const&` and get out a `vector<string_view>`, it's very heavily implied that you're being given a split view of the input string and that its lifetime matches that of the intput param.

---

## Comment 57

> Username: glenfe  
> Created_at: 2020-06-16 18:22:27 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644930981  

> Never say never, imo. So far, we haven't heard a "no",   
  
Out of respect for Marshall, we have heard a "no". As @Lastique says, closing this issue as Won't-Fix. makes sense to me.

---

## Comment 58

> Username: vinniefalco  
> Created_at: 2020-06-16 18:33:20 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644940765  

> Never say never  
  
You could make your own library, say Boost.StringVieux, and implement it the way that you want. It might even pass a review.

---

## Comment 59

> Username: cmazakas  
> Created_at: 2020-06-16 19:47:07 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-644976117  

부스트의 눈물은 영원히 무덤에서 들을 것입니다

---

## Comment 60

> Username: vinniefalco  
> Created_at: 2025-10-24 04:31:37 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-3441006380  

In hindsight, the refusal to improve interoperability was a mistake.

---

## Comment 61

> Username: vinniefalco  
> Created_at: 2025-10-24 04:35:52 UTC  
> Url: https://github.com/boostorg/utility/pull/51#issuecomment-3441020031  

> On a lark, I decided to look look elsewhere in boost for other examples. `boost::any` is not convertible to/from `std::any`. `boost::variant` is not convertible to/from `std::variant`. Same with `boost::variant2`, `boost::optional` and `boost::tuple`.  
  
That's not really a good comparison. `string_view` is a profoundly fundamental type. Strings are processed far more than "any" or "variant". Boost is in a unique position. We invent our own types for backward compatibility and supporting older C++ and on the one hand we try to make them identical to the std equivalent. And on the other hand making them identical in some situations makes it _worse_ than the std equivalent. This is one of those cases.

---
