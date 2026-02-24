# #26 [distance] Add variant support [Merged]

> Username: sdebionne  
> Created at: 2014-05-12 15:28:43 UTC  
> Updated at: 2014-07-06 17:45:12 UTC  
> Merged at: 2014-05-23 15:13:59 UTC  
> Closed at: 2014-05-23 15:13:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/26  

Add variant dispatch layer to the distance algorithm.  
I did not see any test cases for variant in the other algorithms so I'm not sure how to implement them... The current tests compile and pass with MSVC 2012.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-05-12 19:40:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42878163  

Thanks!  
There are many unit tests for variants, for example in area.cpp:  
  
```  
void test_variant()  
```  
  
There is more work in progress (but not variant-work), I just sent a mail to the geometry mailing list to discuss this.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2014-05-13 07:13:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42924109  

OK. I was inspired by length.cpp that lack the variant tests. I'll update the PR with tests ASAP.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-05-13 07:31:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42925427  

Bonjour Samuel.  
  
You might also want to take a look at what I have coded for distances:  
https://github.com/mkaravel/geometry/tree/feature/distance-for-merge  
Hopefully this feature branch will be merged with develop soon.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2014-05-13 09:31:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42934768  

@mkaravel, thank you for the link. Hopefully we are not working on the same features so there won't to much conflicts.  
  
@barendgehrels, I have implemented some tests for the variant resolving part but they do not pass... yet. I have overlooked some issues with (default) strategies and result types. I'm wondering if strategies should expose a rebind metafunction so that they could be re-instantiated with the actual types at the variant resolving stage...

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-05-13 09:38:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42935307  

I am not working on variant support, so in principle there are no conflicts. However, the file structure has changed a lot, and this might mean that we might need to do some copy-paste to synchronize our code.  
  
Regarding the second issue: can you be more specific about a case that fails? Could you briefly explain the problem, and how rebinding would solve it? Thanks.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-05-13 11:47:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-42944981  

@sdebionne: Yes, the strategies may be a problem here. It's not clear to me how Variant-aware algorithms should work for non-default strategies. The strategies in general are another big subject...  
  
I don't want to write a very long comment here about all of the details. I think that currently the resolve_strategy namespace can be used to manually dispatch the strategies for some input Geometry pairs (see within.hpp). Not sure if this was an intention and certainly this isn't convenient. But I'd say that you could just go this way and add only tests for the default strategies.  
  
We could support more flexible way of passing custom strategies, e.g. as a compile-/run-time map mapping e.g. the IDs or tags of Variant geometry-types to strategies (maybe Boost.Fusion would help here). So build something on top of the existing interface. But I don't know if we shouldn't first think about the changes in the lower parts - strategies and compile-time library error handling. But this is a different subject.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-05-13 20:17:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43006939  

The default strategy should be determined on a lower level. So if it is clear what is the type of the variant. Then there are no ambiguities. Indeed as done in within, whatever the conveniency is for it.  
  
I agree with Adam: there has to be more discussed about strategies, and this is important input.

---

## Comment 8

> Username: brunolalande  
> Created_at: 2014-05-15 09:42:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43189108  

Samuel: All variant-aware algorithms have a test for variants. For length, it's in test_length.hpp and I would prefer you to stick to that than to the one of area. Area was the first I did and I changed testing strategy later, the one for length is way easier to write.  
  
Adam: I think your point is that users passing variants to distance() should also be allowed to pass "variant" strategies, or anything that the algorithm can use once it knows the concrete geometry type to apply what the user wants. This is indeed not yet handled, and it's not what resolve_strategy tries to solve - it's only concerned with post-variant resolution of the default strategy. For the other algorithms, that's sufficient. There's a reason I didn't handle variants for distance yet: it's by far the very most complicated case. Probably this particular issue will have to be solved at a later point.

---

## Comment 9

> Username: sdebionne  
> Created_at: 2014-05-16 13:03:44 UTC  
> Updated_at: 2014-05-16 13:08:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43328334  

I have completely reworked my PR to take into account your comments. I have added the resolve_strategy stage. A new "pattern" is introduced : a result_of namespace has been added into both resolve namespaces that house a meta function (named by the algorithm) that help getting the result type from the resolved geometries / strategies.  
I have added some tests that compute distances from variants (point and segment) that pass on my setup.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-05-16 13:32:56 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12737762  

The space added here should go away.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2014-05-16 13:39:17 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12738062  

Seems like this is a new file. I would argue that the only copyright that should stay is that of the author (Samuel).

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-05-16 13:39:41 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12738085  

Same comment as above about copyrights.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-05-16 13:42:31 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12738214  

The point-point strategy and the point-segment strategy return double for all fundamental number types.  
I would find it interesting to have an example where the return type is not double. Such an example would be a point and a box with integer coordinates.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-05-16 13:44:10 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12738291  

Why not use  
  
```  
bg::default_distance_result<point_type, point_type>::type  
```  
  
instead of double?

---

## Comment 15

> Username: mkaravel  
> Created_at: 2014-05-16 13:45:50 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12738356  

Missing new line at end of file.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2014-05-16 13:49:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43333013  

Thanks a lot Samuel. It seems that you have not merged today's changes.  
Have you tried to do so? Were there any conflicts if you tried?

---

## Comment 17

> Username: mkaravel  
> Created_at: 2014-05-16 13:55:42 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12739108  

Space at end of line introduced should go away.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2014-05-16 14:01:27 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12739343  

I do not particularly like the idea of having two static apply methods in the same class here.  
I would expect to see here to see the distance struct templated by Geometry1, Geometry2, Strategy and then specialized for default_strategy.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2014-05-16 14:03:32 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12739444  

Same comment as above here: I would expect the strategy to be one of the template parameters of struct distance.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2014-05-16 14:09:52 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12739736  

I mentioned this in one of emails: I do not like the idea of resolving variants for the version of distance that takes a strategy.  
The variant(s) may contain geometry types whose natural strategies are different. What this implies is that the user may specify a strategy that will be disregarded (actually it seems that the strategy passed is always disregarded).  
I find that this behavior gives the wrong impression to the user that his/her strategy is actually used, which is wrong.

---

## Comment 21

> Username: sdebionne  
> Created_at: 2014-05-16 14:17:04 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12740178  

bg::default_distance_result is not implemented for variant, right ?

---

## Comment 22

> Username: mkaravel  
> Created_at: 2014-05-16 14:38:35 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12741278  

No, it is not.

---

## Comment 23

> Username: sdebionne  
> Created_at: 2014-05-16 15:50:40 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12745162  

That would mean merging resolve_variant and resolve_strategy, no ?

---

## Comment 24

> Username: sdebionne  
> Created_at: 2014-05-16 15:51:28 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12745230  

Are the strategies and the algorithms two orthogonal concepts ? I mean, for all the implemented distance specializations, are every strategies usable or some strategies are limited to a few point types ?

---

## Comment 25

> Username: sdebionne  
> Created_at: 2014-05-16 15:57:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43348107  

Menelaos, thank you for the review. I have successfully merged from upstream (hence the "outdated diff"  above). I went through your notes and fixed the trivial issues.  
I added some tests with user defined strategies that rised new issues that I'm trying to fix.  
I also reworked is_implemented to make it more generic (dispatch::distance was hardcoded).

---

## Comment 26

> Username: brunolalande  
> Created_at: 2014-05-16 15:59:13 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12745740  

[responding directly here as I can't see your comment on the github UI]  
  
> I do not particularly like the idea of having two static apply methods in  
> the same class here.  
> I would expect to see here to see the distance struct templated by  
> Geometry1, Geometry2, Strategy and then specialized for default_strategy.  
>   
> Samuel follows what I've done in all other algorithms, and I prefer to  
> stick to that for consistency. That's crucial to later refactor the whole  
> thing.  
  
Also, experience in BG has proven that template parameters are always  
better at method level than class level - whenever you have the choice, you  
should always choose the former. In this particular case I really don't see  
the problem of having 2 or more apply methods, and I certainly don't want  
to move anything at class level if it's not needed.  
  
Regards  
Bruno

---

## Comment 27

> Username: mkaravel  
> Created_at: 2014-05-16 17:48:41 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12750827  

Missing new line character at end of line.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2014-05-16 17:58:24 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12751323  

Hi Bruno, hi Samuel. I am continuing in email.  
  
I saw the updated version of the PR. Many thanks Samuel for making the   
changes.  
  
There are a couple of issues that I would like to see and also hear   
opinions of others if any:  
- What about the issue of not implementing variant support for   
  distance(g1, g2, strategy)? As I said I do not like the idea of   
  supporting passing a strategy that is completed disregarded.  
- In the unit test, I would like to see the variant defined as   
  variant<point, box, segment> and then try computing the distance for two   
  of those for the various choices of the geometries, like you do in the   
  current test. I like the idea of passing the coordinate type as   
  parameter and testing for both double and int.  
  
See also below.  
  
On 16/05/2014 06:59 ??, brunolalande wrote:  
  
> In include/boost/geometry/algorithms/distance.hpp:  
>   
> > -        return dispatch::distance<Geometry1, Geometry2, Strategy>::apply(geometry1, geometry2, strategy);  
> > -    }  
> >   +  
> > -    template <typename Geometry1, typename Geometry2>  
> > -    static inline typename result_of::distance<Geometry1, Geometry2, default_strategy>::type  
> > -    apply(Geometry1 const& geometry1, Geometry2 const& geometry2, default_strategy)  
> > -    {  
> > -        typedef typename detail::distance::default_strategy  
> > -            <  
> > -                Geometry1, Geometry2  
> > -            >::type strategy_type;  
> >   +  
> > -        return dispatch::distance<Geometry1, Geometry2>::apply(geometry1, geometry2, strategy_type());  
> > -    }  
> >   +};  
> >   +  
> >   [responding directly here as I can't see your comment on the github UI]  
> >   I do not particularly like the idea of having two static apply methods   
> >   in the same class here. I would expect to see here to see the distance   
> >   struct templated by Geometry1, Geometry2, Strategy and then   
> >   specialized for default_strategy.  
> >   Samuel follows what I've done in all other algorithms, and I prefer to   
> >   stick to that for consistency. That's crucial to later refactor the   
> >   whole thing. Also, experience in BG has proven that template   
> >   parameters are always better at method level than class level -   
> >   whenever you have the choice, you should always choose the former. In   
> >   this particular case I really don't see the problem of having 2 or   
> >   more apply methods, and I certainly don't want to move anything at   
> >   class level if it's not needed. Regards Bruno  
  
Okay Bruno. That makes sense. I have to admit I did not look at how you   
implemented variant support for other algorithms.  
So it is perfectly okay for me to follow Bruno's design.  
  
Best,  
- m.  
  
> ---  
>   
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26/files#r12745740.

---

## Comment 29

> Username: barendgehrels  
> Created_at: 2014-05-16 18:06:11 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12751683  

So yes, this review becomes a bit messy, I see that this is already outdated so processed?  
  
Anyway, please keep template <typename Strategy> at the method-level and _not_ at the function level. Sorry if you already changed this. We prefer to have it at the method level (for Menelaos: for your information).  
  
As also remarked by Bruno and now agreed by Menelaos.

---

## Comment 30

> Username: barendgehrels  
> Created_at: 2014-05-16 18:11:34 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12752085  

Formally, a new file is not enough to keep one author. If the file is copied and adapted all copyright holders should stay. I don't know what is the case here.  
  
The Geodan clause can be omitted anyway, that is only valid for old sources.

---

## Comment 31

> Username: barendgehrels  
> Created_at: 2014-05-16 18:13:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43362322  

Samuel, thanks a lot for your merging and rework. I hope all is clear now.  
Menelaos, thanks for all your comments.  
What is the state now?

---

## Comment 32

> Username: mkaravel  
> Created_at: 2014-05-16 18:14:47 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12752285  

Hi Barend.  
  
On 16/05/2014 09:06 μμ, Barend Gehrels wrote:  
  
> In include/boost/geometry/algorithms/distance.hpp:  
>   
> > +  
> > +  
> > +template <typename Geometry1, typename Geometry2>  
> > +struct distance  
> > +{  
> > -    template <typename Strategy>  
> > -    static inline typename result_of::distance<Geometry1, Geometry2, Strategy>::type  
> > -    apply(  
> > -        const Geometry1& geometry1,  
> > -        const Geometry2& geometry2,  
> > -        Strategy const& strategy)  
> > -    {  
> > -        return resolve_strategy::distance::apply(geometry1, geometry2, strategy);  
> > -    }  
> >   +};  
> >   +  
>   
> So yes, this review becomes a bit messy, I see that this is already   
> outdated so processed?  
>   
> Anyway, please keep template at the method-level and /not/ at the   
> function level. Sorry if you already changed this. We prefer to have   
> it at the method level (for Menelaos: for your information).  
>   
> FYI, Samuel has kept it as it was.  
  
@Barend: I will keep that in mind. In fact having templates at the   
method level made my life easy/easier when I started working with   
distances. So no worries, I back this choice, and will do henceforth.  
  
-m.  
  
> As also remarked by Bruno and now agreed by Menelaos.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26/files#r12751683.

---

## Comment 33

> Username: mkaravel  
> Created_at: 2014-05-16 18:18:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43362849  

Hi Barend.  
  
On 16/05/2014 09:13 μμ, Barend Gehrels wrote:  
  
> Samuel, thanks a lot for your merging and rework. I hope all is clear now.  
> Menelaos, thanks for all your comments.  
> What is the state now?  
>   
> I am still worried about supporting variants for the version of distance   
> that takes a strategy. In this case the user passes a strategy that may   
> be disregarded completely, and I do like that. I would like to hear   
> other opinions on this.  
  
The rest are less important and can be worked out after the merge if needed.  
- m.  
  
> —  
>   
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26#issuecomment-43362322.

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2014-05-16 18:33:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43364531  

Intuitively (without having yet seen the code), I would state that it is not a variant problem.  
If the user passes a not-fitting strategy, (s)he should get a compiler error.  
If that happens to be a variant, the compiler error should be the same.  
  
However, we might run into problems then if we have a variant supporting all geometries. Then basically we cannot pass any strategy... To perfectly support this we maybe should then have a variant of strategies too... But I don't want to suggest that, it is probably way to complex.  
  
I'm OK with disregarding the strategy too. Actually I'm OK with anything in this stage, it is not clear and we should work this out later, as you suggest. Maybe Bruno has more concrete opinions about this.  
  
More in general, let me repeat that this is a perfect contribution, and right in time for the closing of the branch in a few weeks.  
  
As soon as Samuel indicates it is ready for merge, I'm OK with merging.

---

## Comment 35

> Username: mkaravel  
> Created_at: 2014-05-16 18:40:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43365303  

Hi Barend.  
  
On 16/05/2014 09:33 μμ, Barend Gehrels wrote:  
  
> Intuitively (without having yet seen the code), I would state that it   
> is not a variant problem.  
> If the user passes a not-fitting strategy, (s)he should get a compiler   
> error.  
> If that happens to be a variant, the compiler error should be the same.  
>   
> However, we might run into problems then if we have a variant   
> supporting all geometries. Then basically we cannot pass any   
> strategy... To perfectly support this we maybe should then have a   
> variant of strategies too... But I don't want to suggest that, it is   
> probably way to complex.  
  
This is what I had in mind (almost): a variant with enough geometries,   
so that no single strategy fits the purpose.  
  
> I'm OK with disregarding the strategy too. Actually I'm OK with   
> anything in this stage, it is not clear and we should work this out   
> later, as you suggest. Maybe Bruno has more concrete opinions about this.  
>   
> More in general, let me repeat that this is a perfect contribution,   
> and right in time for the closing of the branch in a few weeks.  
>   
> As soon as Samuel indicates it is ready for merge, I'm OK with merging.  
  
Same here.  
- m.  
  
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26#issuecomment-43364531.

---

## Comment 36

> Username: mkaravel  
> Created_at: 2014-05-16 19:19:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43369349  

Samuel, do not forget to add your copyright notice in algorithms/detail/distance/interface.hpp  
I think it is missing.

---

## Comment 37

> Username: mkaravel  
> Created_at: 2014-05-16 19:22:18 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12755292  

My understanding was that this was a brand new file, hence the comment.  
In general Barend is right.

---

## Comment 38

> Username: brunolalande  
> Created_at: 2014-05-17 21:27:31 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12770779  

You can replace the use of make_variant_over by transform_variant (which you included but finally didn't use). Basically just replace your "possible_result_types" with:  
  
typedef typename transform_variant<  
    possible_input_types,  
    resolve_strategy::result_of blahblah (the same as you did)  
  
> ::type variant_over_possible_result_types;  
  
and then  
  
typedef typename compress_variant<  
    variant_over_possible_result_types  
  
> ::type type;  
  
I know it's not much different, but a tiny bit shorter and it reuses helper metafunctions.

---

## Comment 39

> Username: brunolalande  
> Created_at: 2014-05-17 21:32:42 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12770803  

I prefer to avoid such obvious comments :-)

---

## Comment 40

> Username: brunolalande  
> Created_at: 2014-05-17 21:36:12 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12770819  

I'm surprised that this works without causing compile errors. I thought the only way to use not_implemented_tag for introspection was to define a macro that prevents not_implemented from having its normal effect (i.e. generating a compile time error). But I might misremember... can you confirm you are not locally defining any macro to make this work?

---

## Comment 41

> Username: brunolalande  
> Created_at: 2014-05-17 21:43:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43424950  

Hi Barend,  
  
> I'm OK with disregarding the strategy too. Actually I'm OK with anything  
> in this stage, it is not clear and we should work this out later, as you  
> suggest. Maybe Bruno has more concrete opinions about this.  
>   
> I have the same opinion - it's a more general problem and we can live with  
> it for now. Also for now I'm really making cross-algo consistency my first  
> priority, and other algos were handled like this - so we should handle this  
> one the same way. Then when a nicer solution is found we can apply it  
> everywhere.  
  
Regards  
Bruno

---

## Comment 42

> Username: awulkiew  
> Created_at: 2014-05-18 13:17:53 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12773626  

Yes I think you're right Bruno, currently it should fail because the above line must be compiled and during the compilation MPL_ASSERT should fail. Of course as long as BOOST_GEOMETRY_IMPLEMENTATION_STATUS_BUILD is not defined as true.  
  
Furthermore, the tool for checking if specifically distance() is implemented for some Geometries probably shouldn't be placed in util/is_implemented.hpp and have such general name as is_implemented<>. Or am I missing something?

---

## Comment 43

> Username: brunolalande  
> Created_at: 2014-05-18 21:05:39 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12776270  

OK, thanks for confirming. Where to put is_implemented is less important at this point, I'm more concerned about the whole concept.  
  
Samuel, unless I misunderstand the code I think you're trying to implement what we were discussing in the thread about run-time specified geometries, am I correct? As discussed in that thread, the library is not (yet) ready for that and right now we need to stick to the current behavior regarding variants which is to have them fail if any of the geometry combinations is incompatible. We have to be consistent across algorithms, and this is potentially a very important behavior for some users. If we later manage to support what you need, I expect it to be offered as a compile-time option.  
  
In short, I prefer to implement variants as they currently are in all other algorithms, and work separately on a global solution for run-time specified geometries / flexible variant support.

---

## Comment 44

> Username: sdebionne  
> Created_at: 2014-05-19 07:40:40 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12782233  

My bad, yes, I do have BOOST_GEOMETRY_IMPLEMENTATION_STATUS_BUILD defined.  
I think that this MPL_ASSERT should be removed and replaced by  
  
```  
MPL_ASSERT((is_implemtented<bla, bla>))  
```  
  
right at the begining of the algo free function.  
  
That would make possible the implementing of a "runtime" (vs compile time) version of the free function. But that I would like to discuss "Runtime specified geometries" thread of the mailing list.  
  
IMHO this is_implemented<...> metafunction is so important that it needs to be available outside the doc status tool. I'll try to push a better implementation later today.

---

## Comment 45

> Username: sdebionne  
> Created_at: 2014-05-19 07:59:54 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12782898  

Sorry I should have commented here (see the note above).  
  
First, Adam is right, the current synced version is "distance specific" and that is plainly wrong (and not what I intended to propose). As I said in a previous note, I'm working on a generic one.  
  
Bruno, it's a IMHO mandatory step in the direction of run-time specified geometries. But I intended to stick with the compile time checking with my current code (I'm working on runtime algo in an other branch whose state is far from becoming a PR !)  
  
I think that we could offer both options (and so two interfaces) and that without requiring a compile macro. But that means removing the MPL_ASSERT from not_implemented.  
  
I can postpone the integration of a 'global' is_implemented<> to a later stage (move it as an implementation detail of variant for distance). It sounds like it needs more thinking, no ?

---

## Comment 46

> Username: sdebionne  
> Created_at: 2014-05-19 08:15:00 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12783336  

> In the unit test, I would like to see the variant defined as variant<point, box, segment>  
  
Just to be sure, is distance implemented for all the combinations of  these geometries and with the default and say the haversine strategies ? If not, what combinations are supposed to fail to compile ?

---

## Comment 47

> Username: sdebionne  
> Created_at: 2014-05-19 09:46:06 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12786557  

Done. I had to overload transform_variant slightly to take an MPL sequence input (rather than a variant) and an optional inserter parameter.  
The reason is combine_if uses an MPL set (to manage uniqueness). Hence mpl::transform requires an inserter since the default does not work with sets.  
As a side note, all the combinations should be limited "at the source" rather than generated and filtered afterward since that would require the user to deal with the MPL_LIMIT_XXX macros to expend the sequence size even for simple variants.

---

## Comment 48

> Username: brunolalande  
> Created_at: 2014-05-19 09:48:15 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12786629  

Hi Samuel,  
  
> Bruno, it's a IMHO mandatory step in the direction of run-time specified  
> geometries. But I intended to stick with the compile time checking with my  
> current code (I'm working on runtime algo in an other branch whose state is  
> far from becoming a PR !)  
>   
> I think that we could offer both options (and so two interfaces) and that  
> without requiring a compile macro. But that means removing the MPL_ASSERT  
> from not_implemented.  
>   
> I agree it's important, and I agree the solution you're proposing sounds  
> promising. But there my be other approached, or not, I need to dig that.  
> This is why for now I prefer to have a consistent implementation and add  
> runtime support at a later point.  
>   
> I can postpone the integration of a 'global' is_implemented<> to a later  
> stage (move it as an implementation detail of variant for distance). It  
> sounds like it needs more thinking, no ?  
>   
> Yep, I think so.  
  
Regards  
Bruno

---

## Comment 49

> Username: sdebionne  
> Created_at: 2014-05-19 12:15:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43494437  

> What is the state now?  
  
I have removed the dependency to the 'global' is_implemented. Consequently that file may not be merged if you prefer. Actually I think it could eventually be merged with the code in algorithm/not_implemented.hpp.  
  
To be honest, I did not understand the discussions about user (non default) strategies being discarded with variant parameters. To me, it is not discarded... I have added a test with the haversine strategy that fails to compile with a NOT_YET_IMPLEMENTED assertion (it's commented out in the latest commit). But AFAIU, this is the intended behaviour : haversine strategy is available only for some combinations of type and not all combinations of point, segment and box (the variant types).

---

## Comment 50

> Username: mkaravel  
> Created_at: 2014-05-19 13:13:08 UTC  
> Updated_at: 2014-05-23 15:00:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#discussion_r12792648  

Hi Samuel.  
  
On 19/05/2014 11:15 πμ, sdebionne wrote:  
  
> In include/boost/geometry/algorithms/distance.hpp:  
>   
> > -        return dispatch::distance<Geometry1, Geometry2, Strategy>::apply(geometry1, geometry2, strategy);  
> > -    }  
> >   +  
> > -    template <typename Geometry1, typename Geometry2>  
> > -    static inline typename result_of::distance<Geometry1, Geometry2, default_strategy>::type  
> > -    apply(Geometry1 const& geometry1, Geometry2 const& geometry2, default_strategy)  
> > -    {  
> > -        typedef typename detail::distance::default_strategy  
> > -            <  
> > -                Geometry1, Geometry2  
> > -            >::type strategy_type;  
> >   +  
> > -        return dispatch::distance<Geometry1, Geometry2>::apply(geometry1, geometry2, strategy_type());  
> > -    }  
> >   +};  
> >   +  
>   
> ```  
> In the unit test, I would like to see the variant defined as variant  
> ```  
>   
> Just to be sure, is distance implemented for all the combinations of   
> these geometries and with the default and say the haversine strategies   
> ? If not, what combinations are supposed to fail to compile ?  
  
Unless I have made a mistake, Cartesian distances work for all geometry   
combinations.  
I have not worked yet with spherical geometries, so I can only speculate   
what works and what does not in the current setting. Since unit tests   
are passing everything that was tested in spherical coordinates in boost   
1.55 still works for the develop branch.  
- m.  
  
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26/files#r12783336.

---

## Comment 51

> Username: mkaravel  
> Created_at: 2014-05-19 13:15:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43500659  

Hi Samuel.  
  
On 19/05/2014 03:15 μμ, sdebionne wrote:  
  
> ```  
> What is the state now?  
> ```  
>   
> I have removed the dependency to the 'global' is_implemented.   
> Consequently that file may not be merged if you prefer. Actually I   
> think it could eventually be merged with the code in   
> algorithm/not_implemented.hpp.  
>   
> To be honest, I did not understand the discussions about user (non   
> default) strategies being discarded with variant parameters. To me, it   
> is not discarded... I have added a test with the haversine strategy   
> that fails to compile with a NOT_YET_IMPLEMENTED assertion (it's   
> commented out in the latest commit). But AFAIU, this is the intended   
> behaviour : haversine strategy is available only for some combinations   
> of type and not all combinations of point, segment and box (the   
> variant types).  
  
Since I initiated this issue, I will prepare an example where I will   
illustrate the issue. I will try to do it ASAP, but realistically   
speaking it will probably be ready tomorrow.  
- m.  
  
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26#issuecomment-43494437.

---

## Comment 52

> Username: sdebionne  
> Created_at: 2014-05-19 14:27:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43510762  

I think I (finally) got Menelaos point ! The current behavior with variant geometries and a user defined strategy is a compile time error if the strategy does not support all the combinations of types of the variant (which unfortunately is unlikely). There is no "hidden use" of the default strategy to make the compiler happy for these combinations of geometries.

---

## Comment 53

> Username: mkaravel  
> Created_at: 2014-05-19 14:30:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43511093  

On 19/05/2014 05:27 μμ, sdebionne wrote:  
  
> I think I (finally) got Menelaos point ! The current behavior with   
> variant geometries and a user defined strategy is a compile time error   
> if the strategy does not support all the combinations of types of the   
> variant (which unfortunately is unlikely). There is no "hidden use" of   
> the default strategy to make the compiler happy for these combinations   
> of geometries.  
  
I had not spotted that we get a compilation error in such cases.  
So I am totally fine with the current approach, since the strategy type   
passed is the one used after all (because in case of strategy type   
conflicts the code will not compile....)  
  
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/geometry/pull/26#issuecomment-43510762.

---

## Comment 54

> Username: barendgehrels  
> Created_at: 2014-05-19 20:18:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43551795  

Samuel, about this:   
  
"I have added a test with the haversine strategy that fails to compile with a NOT_YET_IMPLEMENTED assertion (it's commented out in the latest commit). But AFAIU, this is the intended behaviour : haversine strategy is available only for some combinations of type and not all combinations of point, segment and box (the variant types)."  
  
This is completely fine to me.

---

## Comment 55

> Username: awulkiew  
> Created_at: 2014-05-23 11:26:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-43997422  

I'm guessing that everybody are ok with the merge? If yes, and there are no additional comments, I'll merge it today.

---

## Comment 56

> Username: brunolalande  
> Created_at: 2014-05-23 12:48:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-44004572  

Probably I'm not used enough to github - where's the latest version of  
this? Right now I can still see the use of is_base_of<not_implemented>,  
which will fail to compile.  
  
Bruno

---

## Comment 57

> Username: sdebionne  
> Created_at: 2014-05-23 15:03:12 UTC  
> Updated_at: 2014-05-23 15:03:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-44021746  

@brunolalande I have just removed the not_implement.hpp file (not used anymore) to avoid confusion. is_implemented is replaced by mpl::always<> in the result_of metafunction until I have a "filter" for unimplemented combination. See [this comment](#issuecomment-43494437).

---

## Comment 58

> Username: brunolalande  
> Created_at: 2014-05-23 15:14:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/26#issuecomment-44023728  

Thanks for that, this looks good, I've merged the change.  
  
Bruno

---
