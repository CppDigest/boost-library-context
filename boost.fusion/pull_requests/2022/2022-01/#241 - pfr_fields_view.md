# #241 pfr_fields_view [Closed]

> Username: denzor200  
> Created at: 2022-01-16 17:40:54 UTC  
> Updated at: 2022-12-30 07:10:54 UTC  
> Closed at: 2022-12-30 07:10:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/241  

This PR is a part of the issue https://github.com/boostorg/fusion/issues/231.

---

## Comment 1

> Username: denzor200  
> Created_at: 2022-01-16 17:42:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1013920183  

Here is just docs  for "pfr_fields_view"..  
Implementation in progress..

---

## Comment 2

> Username: denzor200  
> Created_at: 2022-01-16 17:45:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1013920762  

Docs for "pfr_view" and for "BOOST_FUSION_ADAPT_PFR" extension will be in another PRs.

---

## Comment 3

> Username: djowel  
> Created_at: 2022-01-17 00:24:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1013989931  

This will introduce a dependency. I am not sure... We should think about this more. Hopefully, it is decoupled in a separate include?

---

## Comment 4

> Username: denzor200  
> Created_at: 2022-01-17 09:33:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1014317666  

> This will introduce a dependency.  
  
You mean dependency of Fusion from Boost.PFR Library, right?  
  
> Hopefully, it is decoupled in a separate include?  
  
Yes, all pfr_view's must be in a separate include to preserve backward compatibility, cause of Boost.PFR an C++14 library.  
  
This fact is documented everywhere like this:  
```  
[heading Header]  
  
    #include <boost/fusion/view/pfr_fields_view.hpp>  
    #include <boost/fusion/include/pfr_fields_view.hpp>  
  
[note This view won't defined by inclusion of 'boost/fusion/view.hpp'. ]  
```

---

## Comment 5

> Username: denzor200  
> Created_at: 2022-01-19 20:51:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1016857635  

@djowel, fixed

---

## Comment 6

> Username: daminetreg  
> Created_at: 2022-01-28 06:37:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1023929389  

I just wanted to add that I'm watching this from far (not much time sadly at the moment).  
  
And wanted to say this is really awesome and I love it (as last big contributor on BOOST_FUSION_ADAPT_STRUCT). I did a pfr integration in this direction for my talk at CppCon last year, happy @denzor200 you are doing that cleanly ready for prime time.  
  
It makes boost fusion work out of the box for many types and many automations based on Fusion work seamlessly with types that aren't "ADAPTED".  
  
The future is in this PR. 👍

---

## Comment 7

> Username: djowel  
> Created_at: 2022-01-28 06:39:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1023930131  

Are we ready for review and merge?

---

## Comment 8

> Username: djowel  
> Created_at: 2022-01-28 06:40:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1023930635  

> I just wanted to add that I'm watching this from far (not much time sadly at the moment).  
>   
> And wanted to say this is really awesome and I love it (as last big contributor on BOOST_FUSION_ADAPT_STRUCT). I did a pfr integration in this direction for my talk at CppCon last year, happy @denzor200 you are doing that cleanly ready for prime time.  
>   
> It makes boost fusion work out of the box for many types and many automations based on Fusion work seamlessly with types that aren't "ADAPTED".  
>   
> The future is in this PR. 👍  
  
How about doing a review @daminetreg ?

---

## Comment 9

> Username: denzor200  
> Created_at: 2022-01-28 18:29:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1024497514  

> Are we ready for review and merge?  
  
PR is ready for review and merge. I have no planned changes

---

## Comment 10

> Username: daminetreg  
> Created_at: 2022-02-09 13:12:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1033746697  

I would like to apologize for the delay. I'll happily make a first review.

---

## Comment 11

> Username: denzor200  
> Created_at: 2022-03-26 10:53:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1079661289  

Hello everyone, how are you? Do you have an interest/opportunity to continue moving within this PR?

---

## Comment 12

> Username: daminetreg  
> Created_at: 2022-03-26 11:05:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1079663160  

hello yes I’ll review this weekend very sorry for the big delay

---

## Comment 13

> Username: daminetreg  
> Created_at: 2022-03-29 09:38:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1081650881  

I started reviewing but will need a bit more time, before being able to push the first comments, I started using the pfr_view in test programs. Looks cool. :-) I'll come back to you here by the end of the week.

---

## Comment 14

> Username: denzor200  
> Created_at: 2022-03-29 16:13:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1082079704  

@daminetreg Great!

---

## Review 15 [Commented]

> Username: daminetreg  
> Created_at: 2022-07-05 12:16:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/241#pullrequestreview-980876302  

Hi @denzor200,  
  
These are my initial thoughts and comments, really great job. Generally the changes are super clean.  
  
I like it a lot and I think it helps avoiding bugs were one forget to list a member or alike.  
  
I'm unsure with some points though :  
  
## Do we really need the macro ?  
Still I have the feeling this could get one step further, wouldn't it be possible to select via SFINAE non adapted types and check if its autoadapted via Boost.Pfr instead of requiring users to call `BOOST_FUSION_ADAPT_PFR` ?  
  
Providing for example a default template for all the base metafunction: at_impl, begin_impl... an implementation that would use PFR if it's non adapted manually, without requiring the use of a separate macro call to register the type.  
  
## Why not include it by default on C++14 ?  
I wrote an inline comment but I feel like PFR works on all C++14 if not at least C++17, so in this context why not just include it by default and provide the feature by default to everyone.  
  
Keep up the great work, and please accept my apologies for my delay. I'm wired now to be reactive on that one and get it merged ASAP.  
  
## non-constexpr compatible types test case  
It looks like to me that a test case for types that are not constexpr compatible could be made with a test type especially made not to be constexpr. It would be sad to end up with a regression where only constexpr types can benefit from the pfr view.

📁 doc/algorithm.qbk

```diff
 189 |+     struct some_person {       // SimpleAggregate
 190 |+         std::string name;
 191 |+         unsigned birth_year;
```

> Username: daminetreg  
> Created_at: 2022-05-21 20:40:22 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r878758755  

I would prefer to avoid very likely uninitialized memory in documentation examples.

---

📁 doc/algorithm.qbk

```diff
 336 |+     #include <boost/fusion/include/pfr_fields.hpp>
 337 |+ 
 338 |+ [note This algorithm won't defined by inclusion of 'boost/fusion/algorithm/auxiliary.hpp' or 'boost/fusion/algorithm.hpp'. ]
```

> Username: daminetreg  
> Created_at: 2022-05-21 20:45:35 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r878759141  

won't **be** defined  
  
And as a note : Why wouldn't we want it ? I mean it's what the headers are for, includes all algorithms.


📁 doc/view.qbk

```diff
 747 |+ [table
 748 |+     [[Parameter]            [Description]                                                    [Default]]
 749 |+     [[`SimpleAggregate`]    [An user defined structure, satisfied 'SimpleAggregate' concept] []]
```

> Username: daminetreg  
> Created_at: 2022-05-21 20:46:31 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r878759203  

satisfying / fullfilling the 'SimpleAggregate' concept

---

📁 doc/view.qbk

```diff
 795 |+ 
 796 |+     aggregate val{"Edgar Allan Poe", 1809, {}};
 797 |+     __pfr_fields_view__<aggregate> view(val); // non-portable behavior
```

> Username: daminetreg  
> Created_at: 2022-05-21 20:47:23 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r878759311  

Can you elaborate on where it compiles or not or link to the PFR doc that says that ?

> Username: denzor200  
> Created_at: 2022-07-05 12:40:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r913750388  

https://www.boost.org/doc/libs/master/doc/html/boost_pfr/limitations_and_configuration.html  
"The library may work with aggregates that don't satisfy the requirements of SimpleAggregate, but the behavior tends to be non-portable."

> Username: daminetreg  
> Created_at: 2022-07-05 12:48:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r913758255  

It perhaps would be good to state `// not a SimpleAggregate because of inheritance` this way people will understand they can use complex types as member of the SimpleAggregate. Linking to this link of the pfr doc wouldn't be bad as well I think.

> Username: denzor200  
> Created_at: 2022-07-05 13:15:37 UTC  
> Updated_at: 2022-07-05 13:15:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r913784844  

> Linking to this link of the pfr doc wouldn't be bad as well I think.  
  
Link already exists, see `[important Also, please read __boost_pfr_lims__ before using this view. Pay special attention to the SimpleAggregate concept.]` above.  
  
Perhaps this turned out to be a bit not readable. I'll try to rethink my way of presenting information here:)

> Username: denzor200  
> Created_at: 2022-07-08 11:26:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r916727561  

Docs updated


📁 include/boost/fusion/algorithm/auxiliary.hpp

```diff
  15 | 
  16 |+ // Unfortunately, there is no way to determine the compatibility of the pfr library with the current compiler.
  17 |+ // The "boost/fusion/algorithm/auxiliary/pfr_fields.hpp" include has been commented out to ensure backward compatibility
```

> Username: daminetreg  
> Created_at: 2022-05-21 20:48:25 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r878759373  

I think you can safely consider CPP 14 and it should work on all compiler that do support >= 14 or you have example where it's not the case ?

> Username: denzor200  
> Created_at: 2022-07-05 19:33:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914141356  

Pfr does not work on all C++14 compilers, unfortunately. I'll find an example

> Username: denzor200  
> Created_at: 2022-07-05 20:34:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914181857  

Some old versions of Visual Studio support C++14, but pfr there is not work. We cannot break builds of projects that use an older version of Visual Studio, thus we cannot include pfr by default on C++14

> Username: daminetreg  
> Created_at: 2022-07-05 20:37:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914183483  

What if we restrict to a minimum msvc or C++14 ? Would that work of your think there is more surprises. Once we merge in develop we will get the regressions tests also for the many exotic platforms.

> Username: denzor200  
> Created_at: 2022-07-05 21:04:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914202916  

> What if we restrict to a minimum msvc or C++14 ?   
  
I don't know. There can be a lot of exotic platforms. I would very much like to find a compromise, and not to make a regression. @djowel already asked above that the PFR be in a separate include.

> Username: denzor200  
> Created_at: 2022-07-06 08:53:33 UTC  
> Updated_at: 2022-07-06 08:53:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914588912  

> What if we restrict to a minimum msvc or C++14 ?  
  
If so, then we should to give Fusion users the opportunity to disable PFR manually, for example, through the declaration `-DBOOST_FUSION_NO_PFR=1`. This is for case, when some project after switching to a newer Fusion will fail building.  
  
@djowel, what do you think about this?

> Username: daminetreg  
> Created_at: 2022-07-06 10:35:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914686202  

Ah ok I didn't see the request from @djowel, then we can keep it like this.

> Username: denzor200  
> Created_at: 2022-07-08 11:26:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r916727313  

decided not to fix


📁 include/boost/fusion/view.hpp

```diff
  26 |+ // #if !defined(BOOST_FUSION_NO_PFR)
  27 |+ // #include <boost/fusion/view/pfr_fields_view.hpp>
  28 |+ // #endif
```

> Username: daminetreg  
> Created_at: 2022-07-05 11:56:00 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r913711594  

I'm puzzled about not including this feature by default. Wouldn't it be possible to simply check for C++14 support, I mean it's not perfect and might work in compiler supporting less than C++14 but at least for all those that have C++14 at least the experience will be awesome.

> Username: denzor200  
> Created_at: 2022-07-08 11:26:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r916727267  

decided not to fix


📁 test/sequence/pfr_fields_view.cpp

```diff
  46 |+         std::string nickname;
  47 |+     };
  48 |+ #endif // BOOST_PFR_USE_CPP17
```

> Username: daminetreg  
> Created_at: 2022-07-05 12:14:56 UTC  
> Updated_at: 2022-07-05 12:16:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r913727700  

Does it mean we loose the non-constexpr compatible types testcase on C++17 compilation ?

> Username: denzor200  
> Created_at: 2022-07-05 19:28:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914138348  

In C++17 we can't create constexpr `std::string`. Did you mean to say in C++20?  
If I understood you correctly, this structure should be brought to like this form:  
```  
struct employee {  
    guaranteed_nonconstexpr_string name;  
    guaranteed_nonconstexpr_string nickname;  
};  
```

> Username: daminetreg  
> Created_at: 2022-07-05 20:40:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914185476  

Oh yes I mixed it with C++20 that has constexpr allocator and std::string.  
  
So I'm unsure why you disable it on C++17 and if this is the purpose of the test to test non constexpr cases (which makes sense) I would definitely add what you propose with guaranteed_nonconstexpr_string.

> Username: denzor200  
> Created_at: 2022-07-05 20:56:26 UTC  
> Updated_at: 2022-07-05 20:57:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914197380  

> So I'm unsure why you disable it on C++17   
  
Real? I suppose I disable it on C++14 and enable on C++17.  
  
> I would definitely add what you propose with guaranteed_nonconstexpr_string.  
  
Ok, I will add `guaranteed_nonconstexpr_string`

> Username: denzor200  
> Created_at: 2022-07-05 20:57:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#discussion_r914198446  

PS typo fixed


---

## Comment 16

> Username: denzor200  
> Created_at: 2022-07-05 20:03:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1175450751  

> Do we really need the macro ?  
  
Perhaps we do not need a macro, I do not deny it. The macro is made to look like the original pfr. There were 3 ways to output the structure:  
- `pfr::io`  
- `pfr::io_fields`  
- macro `BOOST_PFR_FUNCTIONS_FOR`.  
  
Accordingly, I did something similar in my adapter, 3 ways to serialize the structure:  
- `fusion::pfr`  
- `fusion::pfr_fields`  
- macro `BOOST_FUSION_ADAPT_PFR`  
  
However, we are not obliged to adhere to the same principles as in the PFR, we have our own way. If these principles do not suit us, we will gladly change course.

---

## Comment 17

> Username: denzor200  
> Created_at: 2022-07-05 20:17:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1175460630  

> Still I have the feeling this could get one step further, wouldn't it be possible to select via SFINAE non adapted types and check if its autoadapted via Boost.Pfr instead of requiring users to call BOOST_FUSION_ADAPT_PFR ?  
  
That would be nice, but I haven't found a way to do this with clean changes yet. I really would not like to make changes in the core of Boost Fusion. But, if your request is a serious request, I can continue the search how to do it in the near future.  
  
Also, don't forget that your 'step further' is more likely to regress than the implementation I've shown here. Even if it will be a super clean changes.

---

## Comment 18

> Username: denzor200  
> Created_at: 2022-07-06 16:25:35 UTC  
> Updated_at: 2022-07-06 16:27:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1176427868  

> Providing for example a default template for all the base metafunction: at_impl, begin_impl... an implementation that would use PFR if it's non adapted manually, without requiring the use of a separate macro call to register the type.  
  
If I understand you correctly, instead of a macro, you propose to implement specializations of the `at_impl`, `begin_impl` and so other methods for the `non_fusion_tag` tag.  
  
This will most likely work and the custom struct will be serialized by Fusion without `BOOST_FUSION_ADAPT_PFR`. But there is a nuance - such a structure will not be a legal Fusion Sequence(*). At the very least, this can cause problems using this structure in generic code.  
  
I propose to do the automatic serialization you suggested, but leave my version as a backup for a difficult situation. In total, there will be three ways to serialize a structure:  
- Automatic  
- `fusion::pfr`  
- `fusion::pfr_fields`  
  
We'll remove the `BOOST_FUSION_ADAPT_PFR` macro as unnecessary.  
  
@daminetreg What do you think about this?  
  
(* for this you need to know that this structure can be serialized via PFR, otherwise I do not have the right to specialize `is_sequence` for all `non_fusion_tag`. This issue was discussed here https://github.com/boostorg/pfr/issues/56, but the movement on it No, and I'm not sure that it will be possible to implement it in an acceptable form.)

---

## Comment 19

> Username: denzor200  
> Created_at: 2022-07-08 10:14:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1178809345  

> Providing for example a default template for all the base metafunction: at_impl, begin_impl... an implementation that would use PFR if it's non adapted manually, without requiring the use of a separate macro call to register the type.  
  
Unfortunately, it didn't work out. As it turns out, many Fusion methods require the argument to match the Sequence concept, `fusion::begin` is one of them.  
https://godbolt.org/z/jzWrxcorc  
  
I do not deny that this could be solved through the `fusion::begin` function overload, but it already looks like a crutch and I doubt that it will pass the review.  
I suggest to pass BOOST_FUSION_ADAPT_PFR macro and don't remove it yet. The remaining comments on the review will be fixed in the near future.

---

## Comment 20

> Username: denzor200  
> Created_at: 2022-07-08 18:15:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1179249657  

This PR ready for review. Another PRs(pfr view, pfr extension) will be actualized in the near future.

---

## Comment 21

> Username: Kojoley  
> Created_at: 2022-07-13 20:36:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1183653223  

The problem of this PR (omitting additional dependencies it brings) is addition of `pfr_fields` function, which should not exist, because every place where `pfr_fields` is called it indicates a lack of implementation for existing Fusion functions.

---

## Comment 22

> Username: denzor200  
> Created_at: 2022-07-14 05:36:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1184010103  

> The problem of this PR (omitting additional dependencies it brings) is addition of pfr_fields function, which should not exist, because every place where pfr_fields is called it indicates a lack of implementation for existing Fusion functions.  
  
I understand correctly that you also as @daminetreg  propose to upgrade Fusion to automatically use PFR on non adapted types?  
  
Like this:  
```  
AnUserDefinedStructure attr;  
*fusion::begin(attr) = 0;  
```  
  
Instead of this:  
```  
AnUserDefinedStructure attr;  
*fusion::begin(fusion::pfr_fields(attr)) = 0;  
```  
  
Correct me if I misunderstood you.

---

## Comment 23

> Username: Kojoley  
> Created_at: 2022-07-14 16:49:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1184668406  

> > The problem of this PR (omitting additional dependencies it brings) is addition of pfr_fields function, which should not exist, because every place where pfr_fields is called it indicates a lack of implementation for existing Fusion functions.  
>   
> I understand correctly that you also as @daminetreg propose to upgrade Fusion to automatically use PFR on non adapted types?  
>   
> Like this:  
>   
> ```  
> AnUserDefinedStructure attr;  
> *fusion::begin(attr) = 0;  
> ```  
>   
> Instead of this:  
>   
> ```  
> AnUserDefinedStructure attr;  
> *fusion::begin(fusion::pfr_fields(attr)) = 0;  
> ```  
>   
> Correct me if I misunderstood you.  
  
Pretty much yes, though I still think the support should be opt-in (by including some enabling header first).

---

## Comment 24

> Username: denzor200  
> Created_at: 2022-07-15 04:49:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1185170862  

> Pretty much yes, though I still think the support should be opt-in (by including some enabling header first).  
  
Why not, but then an attribute will be passed to Spirit that's not a Sequence, but at the same time has overloaded methods (`fusion::begin`, `fusion::at`, ...). Is Spirit able to work with such an attribute?

---

## Comment 25

> Username: denzor200  
> Created_at: 2022-07-15 05:03:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1185176763  

```  
template<typename Attrubute>  
void verify_sequence(const Attrubute&) {  
   static_assert(fusion::traits::is_sequence< Attrubute >::value, "Not a legal Fusion Sequence");  
}  
  
AnUserDefinedStructure attr;  
*fusion::begin(fusion::pfr_fields(attr)) = 0;  
verify_sequence( fusion::pfr_fields(attr) );  //< GOOD  
  
AnUserDefinedStructure attr;  
*fusion::begin(attr) = 0;  
// verify_sequence( attr );  //< anyway will failure  
```  
  
We use `fusion::pfr_fields` to tell that our attribute is a legal Sequence, we have no way to determine it automatic, PFR can not this. This issue was discussed here boostorg/pfr#56, but the movement on it No, and I'm not sure that it will be possible to implement it in an acceptable form.

---

## Comment 26

> Username: Kojoley  
> Created_at: 2022-07-16 02:28:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1186070632  

Looking at https://github.com/boostorg/pfr/blob/develop/include/boost/pfr/detail/fields_count.hpp I feel like it is very much possible (and it also seems possible to make PFR work with empty bases classes).

---

## Comment 27

> Username: denzor200  
> Created_at: 2022-07-18 05:11:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1186774810  

> I feel like it is very much possible  
  
You suggest to resolve https://github.com/boostorg/pfr/issues/56 on the PFR side?  
  
> and it also seems possible to make PFR work with empty bases classes  
  
I doubt that it will be possible to do this in c++14 https://godbolt.org/z/nvvzrTr9G (off-top)

---

## Comment 28

> Username: denzor200  
> Created_at: 2022-08-11 03:12:36 UTC  
> Updated_at: 2022-08-11 03:13:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1211510195  

I created pfr ticket: https://github.com/boostorg/pfr/issues/100 and i works with PR for this ticket.  
This pfr functional will let us to implement auto detection serializable types(if we really need it).  
Also, this will let us to remake this PR more shorter, because there will be more ability in the pfr side.

---

## Comment 29

> Username: denzor200  
> Created_at: 2022-09-09 20:04:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1242409890  

>   
  
  
  
> I created pfr ticket: [boostorg/pfr#100](https://github.com/boostorg/pfr/issues/100) and i works with PR for this ticket. This pfr functional will let us to implement auto detection serializable types(if we really need it). Also, this will let us to remake this PR more shorter, because there will be more ability in the pfr side.  
  
I doned docs and posted it here: https://github.com/boostorg/pfr/pull/107  
Everyone who wish can participate in review. Other things - implementation and tests are in progress and will be available soon.

---

## Comment 30

> Username: xR3b0rn  
> Created_at: 2022-12-09 13:09:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1344291683  

Is there any progress on this?

---

## Comment 31

> Username: djowel  
> Created_at: 2022-12-10 00:29:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1344943988  

> Is there any progress on this?  
  
Indeed, I'd love to get this merged soon. Are there any showstoppers that need to be addressed, that are preventing merge?

---

## Comment 32

> Username: denzor200  
> Created_at: 2022-12-10 20:07:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1345374578  

> Is there any progress on this?  
  
Of course, I'm working on it now on the PFR side, then I will return to the Fusion and this PR will be actualized(or recreated).  
I'd like to complete this work, and to fulfill all the previous wishes from reviewers.  
  
> Are there any showstoppers that need to be addressed, that are preventing merge?  
  
We have to wait when https://github.com/boostorg/pfr/pull/111 and https://github.com/boostorg/pfr/pull/86 will be accepted and merged into PFR. And till we waiting, we can do something with broken CI, I suggested a fix here: https://github.com/boostorg/fusion/pull/262

---

## Comment 33

> Username: denzor200  
> Created_at: 2022-12-19 05:09:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1357105408  

Hello everyone, I have a good news, both PRs(https://github.com/boostorg/pfr/pull/111 and https://github.com/boostorg/pfr/pull/86) successfully merged into PFR, I'm ready for switch to working on Fusion. This PR will be reacreated in a very very nearest future.

---

## Comment 34

> Username: denzor200  
> Created_at: 2022-12-19 05:22:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1357113823  

@djowel can we merge https://github.com/boostorg/fusion/pull/262 to apply fix for CI?

---

## Comment 35

> Username: djowel  
> Created_at: 2022-12-21 05:49:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1360885485  

> @djowel can we merge #262 to apply fix for CI?  
  
Done.

---

## Comment 36

> Username: denzor200  
> Created_at: 2022-12-30 07:10:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/241#issuecomment-1367762007  

closed because not actual.  
Actual PR here: https://github.com/boostorg/fusion/pull/266

---
