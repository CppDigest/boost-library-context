# #137 Fix compiler errors [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-16 19:24:40 UTC  
> Updated at: 2018-12-18 18:05:57 UTC  
> Merged at: 2018-12-15 15:50:01 UTC  
> Closed at: 2018-12-15 15:50:01 UTC  
> Url: https://github.com/boostorg/graph/pull/137  

<boost/graph/named_function_params.hpp>  
* Use typename add_const<remove_reference<typename 'T'::value_type>::type>::type vice const typename T::value_type within boost::parameter::aux::tagged_argument type definition.  
* Use boost::mpl::has_key vice boost::detail::parameter_exists.  
* Add missing boost::graph::[function_name] overload definition that takes in ArgumentPack as the last parameter.  
  
"test/Jamfile.v2"  
* Mark msvc-14.0 failures as expected.  
  
".travis.yml"  
* Remove "sudo: false" because Travis Cl deprecated it.  See <https://blog.travis-ci.com/2018-10-04-combining-linux-infrastructures>.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-11-19 02:48:41 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-439756070  

@jzmaddock This is over my pay grade.

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-08 11:06:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/137#pullrequestreview-182940865  

📁 include/boost/graph/named_function_params.hpp

```diff
 373 |+               typename boost::remove_reference<typename T::value_type>::type
 374 |+           >::type
 375 |+       > tagged_arg_type;
```

> Username: jeremy-murphy  
> Created_at: 2018-12-08 11:06:56 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240001509  

Why do we add const and remove references from `value_type` now?

> Username: CromwellEnage  
> Created_at: 2018-12-08 11:30:45 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240002066  

I had compiler errors before without those type transformations.  I'm testing my local fork now to see if those errors still exist without them and, if so, to report what they are.

> Username: CromwellEnage  
> Created_at: 2018-12-08 13:07:58 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240004479  

It looks like I can get by without these type transformations.  I'm going to test my local fork with msvc 14.0 so I can document the regressions.  Then I'll revert these changes.

> Username: jeremy-murphy  
> Created_at: 2018-12-09 00:59:49 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240020928  

OK, keep us posted.

> Username: CromwellEnage  
> Created_at: 2018-12-11 19:21:23 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240755973  

These and other unnecessary changes have now been reverted.


---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-08 11:19:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/137#pullrequestreview-182941232  

📁 test/Jamfile.v2

```diff
 134 |+         <toolset-msvc:version>14.0
 135 |+         <address-model>64
 136 |+     ;
```

> Username: jeremy-murphy  
> Created_at: 2018-12-08 11:19:43 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240001851  

I'm not that familiar with this alias business; does this alias include only msvc, except version 14.0? Or does it include all toolchains except msvc 14.0?

> Username: CromwellEnage  
> Created_at: 2018-12-08 12:40:28 UTC  
> Updated_at: 2018-12-14 17:20:17 UTC  
> Url: https://github.com/boostorg/graph/pull/137#discussion_r240003805  

It includes all toolchains except msvc 14.0.


---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2018-12-08 11:43:16 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445453096  

@jzmaddock John, I think we want to merge this asap.  
There are a few details that I have questions about it but in general it looks good.

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2018-12-08 12:00:56 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445454061  

On Sat, Dec 8, 2018 at 06:19 Jeremy W. Murphy <notifications@github.com>  
wrote:  
  
> *@jeremy-murphy* commented on this pull request.  
> ------------------------------  
>  
> In test/Jamfile.v2  
> <https://github.com/boostorg/graph/pull/137#discussion_r240001851>:  
>  
> > @@ -144,6 +129,35 @@ test-suite graph_test :  
>      [ run delete_edge.cpp ]  
>      ;  
>  
> +alias graph_test_no_msvc14 : :  
> +        <toolset>msvc  
> +        <toolset-msvc:version>14.0  
> +        <address-model>64  
> +    ;  
>  
> I'm not that familiar with this alias business; does this alias include  
> only msvc, except version 14.0? Or does it include all toolchains except  
> msvc 14.0?  
>  
The effect of the alias is to include all toolchains except msvc 14.0 for  
those tests.  
  
  
—  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#pullrequestreview-182941232>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsP5eYgqmDgqHQHK8Hn6Sh_HCTB8Sks5u26BQgaJpZM4Ym1ID>  
> .  
>

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2018-12-08 12:08:25 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445454451  

> The effect of the alias is to include all toolchains except msvc 14.0 for those tests. —  
  
Thanks for clarifying.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-12-08 12:11:51 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445454595  

Doesn't this represent a regression for msvc-14?  For example astar_search_test.cpp develop builds with msvc-14.0, but not after this fix?

---

## Comment 8

> Username: CromwellEnage  
> Created_at: 2018-12-08 22:18:43 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445494116  

Without the alias statement that excludes msvc 14.0, the astar_search_test and random_spanning_tree_test programs outputs exit status -1073741819, while the other affected tests encounter memory access violations at location 0xffffffff.  I'll look into these regressions later tonight.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2018-12-08 23:17:34 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445497214  

John, yes, fair point, but some existing tests (isomorphism) don't even compile at the moment due to some change in the named parameter code.  
  
OK, so maybe a better question is, when did the named parameter code break?  
  
Thanks, Cromwell, I don't have a Windows platform to help with MSVC stuff.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2018-12-08 23:20:13 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445497350  

@CromwellEnage can you give us the background on why/when this code is broken?

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2018-12-08 23:36:28 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445498168  

And, is there a possible alternative to just fix the isomorphism test and leave named parameters alone?

---

## Comment 12

> Username: CromwellEnage  
> Created_at: 2018-12-08 23:51:51 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445498859  

On Sat, Dec 8, 2018 at 18:20 Jeremy W. Murphy <notifications@github.com>  
wrote:  
  
> @CromwellEnage <https://github.com/CromwellEnage> can you give us the  
> background on why/when this code is broken?  
>  
> —  
> You are receiving this because you were mentioned.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#issuecomment-445497350>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsEJK8FmH5c9Q386OrkBJ9UYvRIwpks5u3EkugaJpZM4Ym1ID>  
> .  
>  
In order to enable Boost.Parameter to support perfect forwarding, I had to  
make breaking changes to boost::parameter::aux::tagged_argument and other  
components that were not part of the public interface.  I made the effort  
to ensure that all existing code that used *only* the public interface  
still worked as before, so my changes were approved.  
  
Unfortunately, this library and others relies on non-public portions of  
Boost.Parameter, so now I’m focusing on submitting pull requests to either  
switch to the public interface (preferred) or ensure correct usage of  
current components (as is the case here).

---

## Comment 13

> Username: CromwellEnage  
> Created_at: 2018-12-08 23:59:45 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445499216  

On Sat, Dec 8, 2018 at 18:36 Jeremy W. Murphy <notifications@github.com>  
wrote:  
  
> And, is there a possible alternative to just fix the isomorphism test and  
> leave named parameters alone?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#issuecomment-445498168>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsC9dmQrGs4JvVPbGGtT7PPE6YU0Vks5u3Ez9gaJpZM4Ym1ID>  
> .  
>  
The isomorphism test wasn’t the only failure when I ran a fresh snapshot,  
so alas I can’t leave named parameters alone.

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2018-12-09 00:52:34 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445501477  

Great, thanks for explaining the background.

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2018-12-10 10:28:50 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445766201  

@CromwellEnage how is it going?

---

## Comment 16

> Username: CromwellEnage  
> Created_at: 2018-12-10 15:04:56 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-445847017  

Zero progress on the regression-fixing front.  Not negative, not positive, zero.  I'm not receiving any clues as to why I'm going in the wrong direction, either.  It's frustrating, but I'll keep at it.

---

## Comment 17

> Username: CromwellEnage  
> Created_at: 2018-12-11 19:20:01 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446327291  

I'll have to admit defeat on the msvc-14.0 regression front.  
  
At any rate, the goal of this PR is to fix compiler errors that were occurring across the board.  I'd happily take up the regression issue again in another PR if I had more direct access to msvc-14.0 myself.  
  
However, speaking of compiler errors, I saw some new ones popping up for Xcode, which I noted in the Jamfile.

---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2018-12-11 21:54:10 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446376756  

On Wed., 12 Dec. 2018, 6:20 am Cromwell D. Enage <notifications@github.com  
wrote:  
  
> I'll have to admit defeat on the msvc-14.0 regression front.  
>  
  
Thanks for persisting this far. If I had MSVC I would help, but I'm using  
Linux.  
  
> At any rate, the goal of this PR is to fix compiler errors that were  
> occurring across the board. I'd happily take up the regression issue again  
> in another PR if I had more direct access to msvc-14.0 myself.  
>  
Yeah, let's stay focused on fixing the compilation errors and let the side  
effects be what they are.  
In my opinion it's better to have a single platform failing some tests than  
a single test failing to even compile.  
Presumably other libraries using named parameters might also be having  
problems with msvc-14?  
But wait, how have you been working on this if you don't have access to the  
compiler yourself?  
  
> However, speaking of compiler errors, I saw some new ones popping up for  
> Xcode, which I noted in the Jamfile.  
>  
Huh, interesting. Seem to be some funky compilation issues coming to the  
fore at the moment.

---

## Comment 19

> Username: CromwellEnage  
> Created_at: 2018-12-11 22:24:16 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446385736  

On Tue, Dec 11, 2018 at 16:54 Jeremy W. Murphy <notifications@github.com>  
wrote:  
  
> On Wed., 12 Dec. 2018, 6:20 am Cromwell D. Enage <notifications@github.com  
> wrote:  
>  
> > I'll have to admit defeat on the msvc-14.0 regression front.  
> >  
>  
> Thanks for persisting this far. If I had MSVC I would help, but I'm using  
> Linux.  
>  
> > At any rate, the goal of this PR is to fix compiler errors that were  
> > occurring across the board. I'd happily take up the regression issue  
> again  
> > in another PR if I had more direct access to msvc-14.0 myself.  
> >  
> Yeah, let's stay focused on fixing the compilation errors and let the side  
> effects be what they are.  
> In my opinion it's better to have a single platform failing some tests than  
> a single test failing to even compile.  
> Presumably other libraries using named parameters might also be having  
> problems with msvc-14?  
> But wait, how have you been working on this if you don't have access to the  
> compiler yourself?  
  
  
I’ve been doing the main work on the develop branch of my own Github fork  
and testing that fork using AppVeyor.  Each run is rather time-consuming.  
When I’m ready to publish, I copy a snapshot to the PR branch.  
  
  
> > However, speaking of compiler errors, I saw some new ones popping up for  
> > Xcode, which I noted in the Jamfile.  
> >  
> Huh, interesting. Seem to be some funky compilation issues coming to the  
> fore at the moment.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#issuecomment-446376756>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsE3mcmWORAXWah-NHUcQedHYlULVks5u4CmEgaJpZM4Ym1ID>  
> .  
>

---

## Comment 20

> Username: jeremy-murphy  
> Created_at: 2018-12-12 12:21:43 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446569033  

Btw, I actually had to look up "vice" to realize what it means (and how it is pronounced) in that context.  :)  I don't think that particular usage is common in Australia.

---

## Comment 21

> Username: CromwellEnage  
> Created_at: 2018-12-12 15:03:35 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446618653  

On Wed, Dec 12, 2018 at 07:21 Jeremy W. Murphy <notifications@github.com>  
wrote:  
  
> Btw, I actually had to look up "vice" to realize what it means (and how it  
> is pronounced) in that context. :) I don't think that particular usage is  
> common in Australia.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#issuecomment-446569033>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsDeNRZG_oPnlcw0zD5TERby2RWwXks5u4PTZgaJpZM4Ym1ID>  
> .  
>  
My usage of “vice” wasn’t common for me either until I served in the U. S.  
Navy.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2018-12-12 18:20:54 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446690181  

Is the code now stable and ready to go?  
  
If so I can look at the msvc failure locally.

---

## Comment 23

> Username: CromwellEnage  
> Created_at: 2018-12-12 19:01:20 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446703817  

On Wed, Dec 12, 2018 at 13:20 jzmaddock <notifications@github.com> wrote:  
  
> Is the code now stable and ready to go?  
>  
> If so I can look at the msvc failure locally.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/137#issuecomment-446690181>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsIUiKbXusK8cf-IcOvTCkhnUiJB0ks5u4UkHgaJpZM4Ym1ID>  
> .  
>  
The code is good to go on my end.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2018-12-12 19:47:14 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446719512  

Well... I don't understand the code it seems :(  
  
Here's what I have found:  
  
In boyer_myrvold_planar_test.hpp: 78 the access violation occurs on:  
  
```  
        boyer_myrvold_impl  
          <graph_t,   
           vertex_index_map_t,  
           graph::detail::no_old_handles,  
           graph::detail::no_embedding  
          >  
          planarity_tester(args[graph],   
                           args[vertex_index_map |   
                                get(vertex_index, args[graph])  
                                ]  
                           );  
```  
The subscript calls to args[] both succeed, but then the violation occurs loading the parameters to planarity_tester on the stack.  
  
Changing the above to:  
```  
        auto a1 = args[graph];  
        auto a2 = args[vertex_index_map |  
           get(vertex_index, args[graph])  
        ];  
  
        boyer_myrvold_impl  
          <graph_t,   
           vertex_index_map_t,  
           graph::detail::no_old_handles,  
           graph::detail::no_embedding  
          >  
          planarity_tester(a1,  
                           a2  
                           );  
  
        return planarity_tester.is_planar() ? true : false;  
      }  
```  
  
Fixes the first few calls, but then we get a new access violation on  
  
```  
auto a2 = args[vertex_index_map |  
           get(vertex_index, args[graph])  
        ];  
```  
The "obvious" thing to try then is to replace `args[graph]` with `a1`, but this leads to an impenetrable compiler error I don't understand.  
In any case it looks as if one or more results from these calls is a dangling reference - can that be possible?  What happens if you pass the tests through clang's asan/ubsan?

---

## Comment 25

> Username: CromwellEnage  
> Created_at: 2018-12-12 23:06:30 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446778291  

Progress!  
  
I've replaced the first 'auto' with a const reference to the graph type, which can be extracted from the argument pack via parameter::value_type.  I've replaced the second 'auto' with the vertex index map type of the graph, *not* reference qualified.  Same deal for the edge index map.  
  
If the get() call returns by value, then the bracket operator of args will treat it as an rvalue reference.  Therefore, msvc-14.0 may be evaluating the second 'auto' such that the object holds a dangling reference.

---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2018-12-13 06:02:18 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446852422  

That's great.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2018-12-13 13:16:00 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-446964412  

Closer, but no cigar just yet.  
That now fails on:  
  
```  
            vertex_index_map_t i_map = args[vertex_index_map | get(vertex_index, g)];  
```  
  
But only when the get() call returns something with internal state (ie not just a "default" tag).  
It's fixed by using:  
```  
            graph_t const& g = args[graph];  
            auto a = get(vertex_index, g);  
            vertex_index_map_t i_map = args[vertex_index_map | a];  
```  
The difference is that operator | no longer calls the rvalue-ref overload, but I don't see why that's an issue at present :(  
Hopefully you can see what actual type should replace the auto above?

---

## Comment 28

> Username: CromwellEnage  
> Created_at: 2018-12-13 20:28:42 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447109790  

By replacing "auto" with "typename property_map<graph_t,  vertex_index_t>::const_type", I got it to work.  
  
I've also applied our methods to maximum_adjacency_search and stoer_wagner_min_cut.  astar_search and random_spanning_tree will need more complex fixes.

---

## Comment 29

> Username: CromwellEnage  
> Created_at: 2018-12-14 17:24:43 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447393599  

All right, now I've applied our methods to astar_search and random_spanning_tree.  They work in my fork.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2018-12-14 18:06:03 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447405526  

>All right, now I've applied our methods to astar_search and random_spanning_tree. They work in my fork.  
  
Great!  Just waiting for CI to cycle, there's quite a bit backed up, so it may take a while :(

---

## Comment 31

> Username: jzmaddock  
> Created_at: 2018-12-15 11:24:34 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447561397  

OK all green :)  Are we good to go now?

---

## Comment 32

> Username: CromwellEnage  
> Created_at: 2018-12-15 15:01:04 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447574533  

I'm happy on my end.

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2018-12-15 15:49:56 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447577715  

OK, merging.

---

## Comment 34

> Username: jeremy-murphy  
> Created_at: 2018-12-16 02:42:30 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-447613221  

Great stuff, thanks for the coordinated effort and particularly to Cromwell for the obscure code changes that would have taken the rest of us I don't know how long to figure out.

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2018-12-18 13:29:40 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-448221136  

Since this change, I'm seeing errors in test case isomorphism.cpp:  
  
```  
isomorphism.cpp:136:76: error: no matching function for call to 'isomorphism(graph1&, graph2&, boost::lazy_enable_if_c<true, boost::parameter::aux::tag<boost::graph::keywords::tag::isomorphism_map, boost::associative_property_map<std::map<long long unsigned int, void*> > > >::type)'  
                (g1, g2, _isomorphism_map = make_assoc_property_map(mapping)));  
                                                                            ^  
..\..\../boost/test/minimal.hpp:35:6: note: in definition of macro 'BOOST_CHECK'  
   ( (exp)                      \  
      ^~~  
In file included from ..\..\../boost/graph/depth_first_search.hpp:21,  
                 from ..\..\../boost/graph/isomorphism.hpp:16,  
                 from isomorphism.cpp:26:  
..\..\../boost/graph/isomorphism.hpp:538:42: note: candidate: 'template<class Param0, class Param1> typename boost::result_of<boost::graph::detail::isomorphism_impl<Param0, Param1>(Param0, Param1, const boost::parameter::aux::empty_arg_list&)>::type boost::graph::isomorphism(const Param0&, const Param1&)'  
     BOOST_GRAPH_MAKE_FORWARDING_FUNCTION(isomorphism, 2, 6)  
                                          ^~~~~~~~~~~  
..\..\../boost/graph/named_function_params.hpp:520:3: note: in definition of macro 'BOOST_GRAPH_MAKE_FORWARDING_FUNCTION_ONEX'  
   name(BOOST_PP_ENUM_BINARY_PARAMS(nfixed, const Param, & param) \  
   ^~~~  
..\..\../boost/preprocessor/seq/elem.hpp:43:38: note: in expansion of macro 'BOOST_PP_SEQ_ELEM_III'  
 #    define BOOST_PP_SEQ_ELEM_II(im) BOOST_PP_SEQ_ELEM_III(im)  
                                      ^~~~~~~~~~~~~~~~~~~~~  
..\..\../boost/preprocessor/seq/elem.hpp:41:45: note: in expansion of macro 'BOOST_PP_SEQ_ELEM_II'  
 #        define BOOST_PP_SEQ_ELEM_I(i, seq) BOOST_PP_SEQ_ELEM_II(BOOST_PP_SEQ_ELEM_ ## i seq)  
```  
  
strangely this is not showing up in the CI test results, but is in the test matrix, see for example:  
  
https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-graph-test-isomorphism-test-clang-linux-6-0~gnu++17-debug-visibility-hidden.html  
or  
https://www.boost.org/development/tests/develop/developer/output/teeks99-09-n-win2012R2-64on64-boost-bin-v2-libs-graph-test-isomorphism-test-msvc-14-1-dbg-adrs-mdl-64-thrd-mlt.html  
Any ideas anyone?

---

## Comment 36

> Username: CromwellEnage  
> Created_at: 2018-12-18 15:18:10 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-448255690  

When a boost::parameter::keyword object such as _isomorphism_map is assigned an rvalue reference such as the object returned by make_assoc_property_map(mapping), it returns a tagged_argument_rref rather than a tagged_argument.  I'll open up another PR that adds clang++-6 to the test matrix and fixes this issue.

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2018-12-18 17:49:40 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-448309262  

>I'll open up another PR that adds clang++-6 to the test matrix and fixes this issue.  
  
I only happened to give clang as an example - the test matrix has that test failing with nearly all compilers, including msvc and gcc.  As I said, I'm lost as to why CI doesn't show the failure.

---

## Comment 38

> Username: CromwellEnage  
> Created_at: 2018-12-18 18:05:56 UTC  
> Url: https://github.com/boostorg/graph/pull/137#issuecomment-448314470  

It looks like my fix only worked for Cl because the compilers there were able to find the new overload that I added to named_function_params.hpp, while the compilers used in the Boost develop test matrix did not...which I also find strange.  Regardless, I'm going to work on a more robust fix.

---
