# #3 Version of ADAPT_STRUCT that does not require specifying the types of the fields [Closed]

> Username: daminetreg  
> Created at: 2014-03-05 00:22:58 UTC  
> Updated at: 2014-06-17 19:41:53 UTC  
> Closed at: 2014-06-14 10:40:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/3  

Hi @djowel,  
  
I finally got a first version of the **BOOST_FUSION_AUTO_ADAPT***  macros which deduce the types of the attributes fields. The idea I have is avoiding where this makes sense to repeat the type, as it is an important source for mistakes when changing the type of one of the fields, this is especially problematic with types that can be auto converted between each other as it lead  to loss of precision and other kinds of issue.  
  
It's also nice to type less. :)  
  
This pull-request follow the discussion on the boost devel mailing list : [DRYing BOOST_FUSION_ADAPT_STRUCT with decltype/Boost.TypeOf](http://lists.boost.org/Archives/boost/2013/12/209912.php) and the related ticket [#9516](https://svn.boost.org/trac/boost/ticket/9516).  
  
To sum it up, this patch allows one to adapt a given struct to a random access sequence, by only providing the list of members to adapt :   
  
``` cpp  
namespace demo  
{  
    struct employee  
    {  
        std::string name;  
        int age;  
    };  
}  
  
// demo::employee is now a Fusion sequence  
BOOST_FUSION_AUTO_ADAPT_STRUCT(  
    demo::employee,  
    (name)  
    (age))  
```  
  
The changes are relatively small and are only macros, I added new headers bringing the auto versions of the macros :  
- `include/boost/fusion/adapted/struct/auto_adapt_struct.hpp`  
- `include/boost/fusion/adapted/struct/auto_adapt_struct_named.hpp`  
- `include/boost/fusion/adapted/struct/auto_adapt_assoc_struct.hpp`  
- `include/boost/fusion/adapted/struct/auto_adapt_assoc_struct_named.hpp`  
  
I duplicated the related units-test of the non auto version and adapted them, as we now have :  
- `test/sequence/auto_adapt_struct.cpp`  
- `test/sequence/auto_adapt_struct_named.cpp`  
- `test/sequence/auto_adapt_assoc_struct.cpp`  
- `test/sequence/auto_adapt_assoc_struct_named.cpp`  
  
I also added the related documentation in `doc/adapted.qbk`, a generated version can be found [here](http://lecbna.org/sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/) for reference. The additions are in the _Adapted_ section under :   
- BOOST_FUSION_ [AUTO_] ADAPT_STRUCT  
- BOOST_FUSION_ [AUTO_] ADAPT_STRUCT_NAMED  
- BOOST_FUSION_ [AUTO_] ADAPT_ASSOC_STRUCT  
- BOOST_FUSION_ [AUTO_] ADAPT_ASSOC_STRUCT_NAMED  
  
As this is a first version, I'm the meaning that this isn't good enough to integrate it in fusion. That's why I opened this pull-request, so that we can discuss about the signatures and the implementation of the AUTO macros. Indeed different questions appeared to me, while writing these new type deducing ADAPT macros.  
  
I would be really thankful to you if you could give me some pointer or hints to clear them up, these questions are mainly design choices, because I'm afraid that with these AUTO macros I could have broken the coherence of the Boost.Fusion API.  
## Why is there two places for the Boost.Fusion headers ?  
  
For example BOOST_FUSION_ADAPT_STRUCT is available from both :  
- `boost/fusion/adapted/struct/adapt_struct.hpp`  
- `boost/fusion/include/adapt_struct.hpp`  
  
Is that because of backward compatibility ? Or are the boost/fusion/adapted... ones private and others public ?  
  
In this patch only the boost/fusion/adapted/struct/ are provided as I wasn't sure.   
## What about mixing together the AUTO version and normal ADAPT macros ?  
  
Currently I choosed to make always another version of the macro for the type deducing one, with an AUTO prefix in front of it, to provide the meaning of the auto keyword as in C++11.   
  
I'm not sure that this is a good idea it would perhaps be better if the existing BOOST_FUSION_ADAPT_STRUCT macros and others would be usable as well with pairs of _(member_typeN, member_nameN)_ and only _(member_nameN)_, or is it pointless to be able to specify the type, and it would be even better to provide a breaking change for the ADAPT api ?   
  
This would allow something like optional type parameters :  
  
``` cpp  
namespace demo  
{  
    struct employee  
    {  
        std::string name;  
        int age;  
        std::string postal_address;  
        int salary;  
    };  
}  
  
// demo::employee is now a Fusion sequence  
BOOST_FUSION_ADAPT_STRUCT(  
    demo::employee,  
    (std::string, name)  
    (age)  
    (postal_address)  
    (int, salary))  
```  
  
Would this be a good idea ? And what are the coherence issue with the __TPL_ version of the macro ?  
### Sub questions if we don't mix AUTO and normal ADAPT macros  
#### Is it better to put the auto macro in the same headers files than the non-auto one ?  
  
Currently I made new header files for the auto version, but I'm not so sure if it makes sense, should I put them together ?  
#### Should we have the units-test for both be in one units test file and share at most code ?  
  
In this patch I simply duplicated and adapted the units tests for the auto version. This is naturally not fun to manage changes to the units test.  
## Doing the same for the _TPL version ?  
  
I'm not sure if this would be possible, but I would be happy to also be able to deduce template parameter name or read them from the template decl to avoid repeating them there too. Do you see some practicable way to achieve this ?  
## Type deducing doesn't makes sense for BOOST_FUSION_ADAPT_ADT and BOOST_FUSION_ADAPT_ASSOC_ADT  
  
If I understood well, it's even the point of BOOST_FUSION_ADAPT_ADT and  BOOST_FUSION_ADAPT_ASSOC_ADT to be able to specify the type of the fields, and that there is no point in deducing them there ? Perhaps mistaken on this point.  
  
Thank you for taking the time to review this pull-request :)  
Looking forward to read from you.  
  
Cheers,

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-03-05 00:31:11 UTC  
> Updated_at: 2014-03-05 00:31:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-36697075  

A few comments:  
- Since `BOOST_FUSION_AUTO_ADAPT_STRUCT` takes only one element, it seems that the parens are redundant. Instead, it could simply be:  
    
    BOOST_FUSION_AUTO_ADAPT_STRUCT(  
        demo::employee,  
        (name, age, ...))  
- I like the idea of having a single `BOOST_FUSION_ADAPT_STRUCT` macro that works with mixed member specifications. I think it should be possible to implement this without introducing any breaking change.  
- The forwarding headers in `include` are a convenience for the user.  
- The point of `BOOST_FUSION_ADAPT_ADT` is specifying get/set expressions to access the members. The first argument specifies the return type of the get expression, and can be automatically deduced. I'm not entirely sure that the second argument is needed at all.

---

## Comment 2

> Username: daminetreg  
> Created_at: 2014-03-05 00:43:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-36697859  

First thank you @K-ballo for this fast reply, I didn't even had the time  
to get the pull-request page reloaded after my click on "send  
pull-request". :p  
  
On 03/05/2014 01:31 AM, Agustín Bergé wrote:  
  
> A few comments:  
> - Since `BOOST_FUSION_AUTO_ADAPT_STRUCT` takes only one element, it seems that the parens are redundant. Instead, it could simply be:  
>   BOOST_FUSION_AUTO_ADAPT_STRUCT(  
>       demo::employee,  
>       (name, age, ...))  
  
Yes that would be nicer, I would better remove them. I put this on my  
TODO list. :)  
  
> - I like the idea of having a single `BOOST_FUSION_ADAPT_STRUCT` macro that works with mixed member specifications. I think it should be possible to implement this without introducing any breaking change.  
>   
> So you think that it would be nice to allow the use case of being able  
> to specify the type for some fields. For example to get them  
> auto-converted ?  
> - The forwarding headers in `include` are a convenience for the user.  
>   
> Thanks for the info, I will make these headers then too.  
> - The point of `BOOST_FUSION_ADAPT_ADT` is specifying get/set expressions to access the members. The first argument specifies the return type of the get expression, and can be automatically deduced. I'm not entirely sure that the second argument is needed at all.  
>   
> Ok, I thought there were some use case when the attributes type could be  
> different but compatible to the getter one.  
  
But this makes sense that it's not so. So the get_expression return type  
should be enough for this. I put it on my TODO list too. :)  
  
Thank you for these first comments, they are really constructive and  
helpful.  
  
> ---  
>   
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/fusion/pull/3#issuecomment-36697075

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-03-05 00:56:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-36698681  

> - I like the idea of having a single `BOOST_FUSION_ADAPT_STRUCT` macro that works with mixed member specifications. I think it should be possible to implement this without introducing any breaking change.  
>   So you think that it would be nice to allow the use case of being able to specify the type for some fields. For example to get them auto-converted ?  
  
I'm not saying this would be good or bad, I'm just saying that AFAIK it should be possible with variadic macros and if that's the case then a single macro would be better than two separate ones that do less.  
  
> - The point of `BOOST_FUSION_ADAPT_ADT` is specifying get/set expressions to access the members. The first argument specifies the return type of the get expression, and can be automatically deduced. I'm not entirely sure that the second argument is needed at all.  
>   Ok, I thought there were some use case when the attributes type could be different but compatible to the getter one. But this makes sense that it's not so. So the get_expression return type should be enough for this. I put it on my TODO list too. :) Thank you for these first comments, they are really constructive and helpful.  
  
Nod, the return type of the get expression would be enough. I think the second argument is needed in C++03 since we don't have perfect forwarding, but should not be needed in C++11.

---

## Comment 4

> Username: daminetreg  
> Created_at: 2014-03-07 05:01:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-36968325  

On 03/05/2014 01:56 AM, Agustín Bergé wrote:  
  
> > - I like the idea of having a single `BOOST_FUSION_ADAPT_STRUCT` macro that works with mixed member specifications. I think it should be possible to implement this without introducing any breaking change.  
> >   So you think that it would be nice to allow the use case of being able to specify the type for some fields. For example to get them auto-converted ?  
>   
> I'm not saying this would be good or bad, I'm just saying that AFAIK it should be possible with variadic macros and if that's the case then a single macro would be better than two separate ones that do less.  
  
I'll give it a try, because I'm also the meaning that a single macro  
could be really nice. This will allow me to clean up a bit the  
implementation of the detail/adapt_base.hpp that I didn't touched until  
now. So I'll change the API to the following :  
  
``` cpp  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    member_name0,  
    member_name1,  
    (member_type2, member_name2)  
    (member_type3, member_name3),  
    member_name4  
    ...  
    )  
```  
  
This would allow to be backward compatible and have in the same time the  
old, the new API and both to disposal.  
  
@djowel are you also the meaning that it would be better than fully  
separate macros as I proposed first ?  
  
> > - The point of `BOOST_FUSION_ADAPT_ADT` is specifying get/set expressions to access the members. The first argument specifies the return type of the get expression, and can be automatically deduced. I'm not entirely sure that the second argument is needed at all.  
> >   Ok, I thought there were some use case when the attributes type could be different but compatible to the getter one. But this makes sense that it's not so. So the get_expression return type should be enough for this. I put it on my TODO list too. :) Thank you for these first comments, they are really constructive and helpful.  
>   
> Nod, the return type of the get expression would be enough. I think the second argument is needed in C++03 since we don't have perfect forwarding, but should not be needed in C++11.  
>   
> Ok. That's a good hint about perfect forwarding. I'll make a version of  
> BOOST_FUSION_ADAPT_ADT which uses deduce types from the get_expr.  
  
I have to look deeper in BOOST_FUSION_ADAPT_ADT to see if it's possible  
to get rid of attribute_const_type.  
  
Thanks again for your comments. :)  
  
> ---  
>   
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/fusion/pull/3#issuecomment-36698681

---

## Comment 5

> Username: djowel  
> Created_at: 2014-04-09 04:43:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-39928179  

I'd love to get this merged ASAP. Is this the latest? Have you been able to address all the concerns in the comments above?

---

## Comment 6

> Username: djowel  
> Created_at: 2014-04-09 04:45:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-39928261  

Damien, since you know your way around the adapt macros, would you be interested to be its maintainer? I believe it needs some loving care and attention. Right now, it's a mess, IMO. It would be good if someone takes care of it.

---

## Comment 7

> Username: daminetreg  
> Created_at: 2014-04-10 15:03:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40096369  

Hi @djowel thank you for your replies.   
  
I didn't got so much time last weeks to improve the macros from the comments, however I wanted to give it a run next week and this weekend.  
  
I started on a separate branch the improvements related to the comment, that I'll push on this pull-request as soon as it fullfill what was discussed in these comments.  
  
It would be an honor to help maintaining this. Is there a dead-line for the AUTO features for these macros ? Because if you want to have this soon, I can block more time to work on the comments.   
  
Right now I wasn't able to continue because of other projects (actually one which is using this feature), but I can put this as priority 1 if you tell me that you want the "AUTO" version soon.

---

## Comment 8

> Username: djowel  
> Created_at: 2014-04-10 21:28:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40145170  

Well, I am working on Spirit X3 right now and it would be nice to use your new adapt struct macros instead of the older ones in the examples I am porting from Spirit V2. But please, take your time. No deadlines. This is open source anyway and I am thankful enough that you welcome my suggestion. I've authored 3 boost libraries and it's getting tough to maintain all of them. This part of fusion is something that I've always wanted to clean up but lacked time. It would be superb if someone can take care of it.

---

## Comment 9

> Username: daminetreg  
> Created_at: 2014-04-11 04:52:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40170714  

I'm a fanboy of Boost.Spirit, I like it so much. I have also some binary parsers I want to contribute some day, but I've to extract them out of an older closed-source application and clean them up before.  
  
Spirit, Fusion and Phoenix are really big, so I imagine the workload which comes with maintaining them. I'll help for the small adapt part. I hope we can get someday Rich Pointers as proposed in N3340 to be able to automagically adapt structs.  
  
I arranged one week full time for the ADAPT cleanup from the 5th to 9th May. I'll work before on it but this will be really active at this moment. I hope that what I'll produce will be of an acceptable quality for Boost (We will see :p ) I'm excited about your review then.

---

## Comment 10

> Username: daminetreg  
> Created_at: 2014-04-15 22:11:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40540899  

@djowel Is there a reason why the private macros like BOOST_FUSION_ADAPT_STRUCT_BASE have no doxygen doc ? I've started documenting them, so that the reason and the effect  for each parameters get explained.   
  
However as there isn't any doc in the detail/ implementation, I'm just wondering if there is any good reason why ? Or it's just because it's private and that you didn't get the time to document it ?

---

## Comment 11

> Username: djowel  
> Created_at: 2014-04-15 23:04:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40545206  

Damien, those are implementation details. Not documenting them gives us some flexibility to change the implementation when needed. Once we document them, people will start relying on the interface. For implementation docs, we simply put them in the header files in the form of comments.  
  
As for doxygen, I don't think we have any doxygen doc at all, do we? To be honest, I am not a fan of doxygen. I believe a good library is not just a bunch of function calls and classes. There are such things as concepts and expression semantics that cannot be captured with doxygen. Doxygen is so poorly equipped to deal with modern C++.

---

## Comment 12

> Username: daminetreg  
> Created_at: 2014-04-16 05:17:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-40563877  

On 04/16/2014 01:04 AM, Joel de Guzman wrote:  
  
> Damien, those are implementation details. Not documenting them gives us some flexibility to change the implementation when needed. Once we document them, people will start relying on the interface. For implementation docs, we simply put them in the header files in the form of comments.  
>   
> Sorry Joel I've wrongly formulated my question. I didn't want to make a  
> public documentation in the form of a Boost Book for the implementation  
> details. I just was wondering if there was a reason why there isn't so  
> much comments to describe the interface and behaviour of reusable  
> implementation details macros.  
  
But now I've got the reply as well.  
  
I asked because I did not found so much comments in the implementation  
details across boost fusion code base, and I was wondering if there was  
any other reason, like avoiding that people begin using things from the  
details/ headers directly and so on.  
  
> As for doxygen, I don't think we have any doxygen doc at all, do we? To be honest, I am not a fan of doxygen. I believe a good library is not just a bunch of function calls and classes. There are such things as concepts and expression semantics that cannot be captured with doxygen. Doxygen is so poorly equipped to deal with modern C++.  
>   
> I didn't saw doxygen anywhere. It was just to describe the kind of  
> comments I was using for the internal macros API. I agree with you on  
> doxygen and I wouldn't use it for a doc of a library like Fusion. It's  
> just the comments format which are useful to comment the API of  
> macros/functions. But I'll factor out the macros to avoid comments. :)  
  
And if comments are still needed I'll avoid the doxygen style as it's  
not used anywhere in fusion.  
  
Thank you and sorry for the misunderstanding.  
  
> ---  
>   
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/fusion/pull/3#issuecomment-40545206

---

## Comment 13

> Username: daminetreg  
> Created_at: 2014-05-13 22:18:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-43020550  

Hi @djowel, I've been working last week on it, not so much as I wanted but now I'm allocating time for it and it's going forward.  
  
So far I'm working on a separate branch than the one of this pull-request (i.e. [here](https://github.com/daminetreg/fusion/commits/feature/improving-type-deduction-for-ADAPT_STRUCT-macros) ), because the changes are not ready for review.   
  
However I currently need variadic macros support, to be able to support an API which can handle both providing type and/or type deduction, as :   
  
``` cpp  
 BOOST_FUSION_ADAPT_STRUCT(  
     struct_name,  
     member_name0,  
     member_name1,  
     (member_type2, member_name2)  
     (member_type3, member_name3)  
     (member_name4),  
     member_name5  
     ...  
     )  
```  
  
This is what we discussed above, so that the new macro get compatible with the old one and also provides more flexibility.   
  
I was wondering if this was a problem, because it seems not all compilers that Boost supports do have VARIADICS. Should I avoid them in this case or it is no matter for Boost.Fusion ?  
  
Cheers :)

---

## Comment 14

> Username: daminetreg  
> Created_at: 2014-06-11 01:09:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-45691343  

Hi @djowel and @K-ballo,  
  
I'm writing you today because I think I got a reviewable ( i.e. hopefully good ) state for the `BOOST_FUSION_ADAPT_STRUCT` and `BOOST_FUSION_ADAPT_STRUCT_TPL` deducing the type without separate macros and providing a backward compatible signature to the existing macro.  
  
This was for me the important basis since our discussion about my first easy/naïve implementation of the feature, now this is quite more flexible and better maintainable, because it includes less repetition of the same kind of macros.  
  
# What is available now in this patch ?  
  
Simply `BOOST_FUSION_ADAPT_STRUCT` and `BOOST_FUSION_ADAPT_STRUCT_TPL` capable of deducing the types of declared fields, but still allowing the old syntax. The unit test to this and the documentation for these two macros.  
  
``` cpp  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    member_name0,  
    member_name1,  
    member_name2,  
    ...  
    )  
  
// When BOOST_PP_VARIADICS is missing or you wish to specify type self :  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    (member_type0, member_name0)  
    (member_type1, member_name1)  
    (BOOST_FUSION_ADAPT_AUTO, member_name2)  
    ...  
    )  
  
// It can even be mixed, but I wouldn't document it :   
  
BOOST_FUSION_ADAPT_STRUCT(  
    struct_name,  
    member_name0,  
    member_name1,  
    (member_type2, member_name2)  
    (member_type3, member_name3)  
    (BOOST_FUSION_ADAPT_AUTO, member_name4),  
    member_name5,  
    ...  
    )  
  
  
// Note that the same applies for the _TPL versions of ADAPT_STRUCT.  
```  
  
Example of the generated doc :   
- [BOOST_FUSION_ADAPT_STRUCT](http://www.lecbna.org/~sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/fusion/adapted/adapt_struct.html)  
- [BOOST_FUSION_ADAPT_STRUCT_TPL](http://www.lecbna.org/~sys_lecbna/boost_fusion_type_deduction_for_adapt_struct_doc/fusion/adapted/adapt_tpl_struct.html)  
  
## Rationale about the new ADAPT_STRUCT signature.  
  
It's possible to use the macro without BOOST_PP_VARIADICS support as it was possible before, which is an important deal to ensure backward compatibility. That's why the complexity of the parameter parsing costs a bit readability.  
  
The type deduction feature is also usable in the "old" syntax via the placeholder BOOST_FUSION_ADAPT_AUTO, which is simply defined to BOOST_PP_EMPTY, which would also allow to completely omit it `(, member_name3)`, but it's not something advisable, as even if it works on GCC/Clang  the BOOST_PP_IS_EMPTY facility used to detect the placeholder is not thought to detect emptyness but a macro expanding to nothing or 0.  
  
To this a new signature was added when BOOST_PP_VARIADICS was available, which offers a pretty light syntax, as all unneeded parens were removed in comparison to the first version of this patch. This allows to simply declare the field to take in the adapted sequence as variadic arguments.  
  
# What is not in this patch ?  
  
It took a bit longer, as I searched long a solution to provide a clean API, often changing my mind, that's why I'm not delivering you in one batch the full feature.   
  
Now that I have this solid basis, I can go on and add type deduction support to the other macros (listed below), but this is more like a copy-paste-reuse work than anything else, so I hope to provide them really soon.  
- BOOST_FUSION_ADAPT_STRUCT_NAMED  
- BOOST_FUSION_ADAPT_ASSOC_STRUCT  
- BOOST_FUSION_ADAPT_ASSOC_STRUCT_NAMED  
- BOOST_FUSION_ADAPT_ADT  
- BOOST_FUSION_ADAPT_ASSOC_ADT  
- And their TPL counterparts naturally  
- Fix the DEFINE_STRUCT_\* macros to work with the new adapt_base  
  
# Why am I sending this incomplete ?  
  
It's not all macros as said above, but that's the central ones IMHO, and therefore the sooner this part is reviewed, the faster I'll know if it's the way I should follow. Because it's important to me that my proposal please your philosophy and design principles.   
  
So I beg you if you want to, to criticize this state and tell me what you want different. I love critics. :) I even need them.  
  
As all other macros will simply be calls or glue code to this, I think that it makes sense to already propose you these changes for review. :smile:  
  
Thank you for your support, I hope I'll be in time for the Spirit X3 release. :sweat_smile:

---

## Comment 15

> Username: djowel  
> Created_at: 2014-06-12 01:08:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-45819430  

I notice this is targeting the master. It should instead target develop. Oh wait, better yet, why don't we have a new branch for it. I'll create a branch...  
  
Ok created: https://github.com/boostorg/fusion/tree/fusion_adapters  
  
Can we move this over there. That way I can merge and see the code in action. Then we can interact more.

---

## Comment 16

> Username: daminetreg  
> Created_at: 2014-06-14 10:40:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/3#issuecomment-46084491  

> I notice this is targeting the master. It should instead target develop. Oh wait, better yet, why don't we have a new branch for it. I'll create a branch...  
  
Oh sorry, I missed this totally when I started the pull-request. I rebased on fusion_adapters. I have alot of commit which are trying different direction, do you think it's valuable that I squash all the commits together or do you prefer me to let the history as-is ?  
  
I cannot change the target of this pull-request, so I'll close this one and open another targeting the right branch.  
  
> That way I can merge and see the code in action.  
> Nice :) Just note that in the current state the DEFINE_STRUCT_\* aren't working, I didn't fix them with the changes made to adapt_base, but I'll do so next week, because that's just adapting how the arguments are extracted from the expanded BOOST_FUSION_ADAPT_STRUCT_FILLER macros.  
  
[Here](https://github.com/boostorg/fusion/pull/9) is the new pull-request against fusion_adapters.  
  
Thanks for creating this branch, it's perfect to interact.  
Cheers ;)

---
