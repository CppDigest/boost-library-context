# #726 Umbrella strategies. [Merged]

> Username: awulkiew  
> Created at: 2020-06-16 22:37:44 UTC  
> Updated at: 2020-08-28 14:16:26 UTC  
> Merged at: 2020-08-28 14:16:25 UTC  
> Closed at: 2020-08-28 14:16:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/726  

Followup of issue https://github.com/boostorg/geometry/issues/717.  
  
I prepared a preliminary version of umbrella strategies for `area()` algorithm. The purpose is to use this simple example to discuss whether or not you agree with the approach at the early stage, agree about naming conventions, etc.  
  
The current design allows to  
- call the default version automatically picking the strategy based on the CS of the geometry  
- pass umbrella strategy (for an algorithm) from namespace `bg::strategies::area`, e.g. `bg::strategies::area::cartesian<>`  
- pass umbrella strategy (for all algorithms) from namespace `bg::strategies`, e.g. `bg::strategies::spherical<>`  
- pass simple/legacy strategy (for backward compatibility) from namespace `bg::strategy::area`, e.g. `bg::strategy::area::geographic<>`, which is converted to umbrella strategy and passed to `area()`  
  
```  
polygon po = { { {0, 0}, {0, 5}, {5, 5}, {5, 0}, {0, 0} },  
               { {1, 1}, {4, 1}, {4, 4}, {1, 4}, {1, 1} } };  
bg::area(po);  
bg::area(po, bg::strategies::area::cartesian<>());  
bg::area(po, bg::strategies::spherical<>());  
bg::area(po, bg::strategy::area::geographic<>());  
```  
  
For now I placed the umbrella strategies in the separate directory `strategies2`. Later I think both directories could be merged preserving backward compatibility of includes.  
  
I have also 2 proposals which I'd like to discuss along with this:  
- removal of spherical-polar CS and making `spherical` CS an alias of `spherical_equatorial`  
  - the rationale for this proposal is that the support for this CS is limited anyway, there are no users requesting an improvement in this area, and that if a user had data in this CS he/she could simply implement get/access converting to `spherical_equatorial` on the fly.  
- including strategies for an algorithm automatically, together with the algorithm  
  - this is in order to be conformant with the boost header policy. AFAIU if a user includes an algorithm this algorithm should compile. Our default strategy design prevents that because the user also has to include the default strategy for a coordinate system, he/she has to know which strategy is the default one which is not clear so I think in practice means that either the main `geometry.hpp` or all of the `strategies.hpp` are included anyway. Plus I don't understand what is the reason behind separating algorithms from strategies. AFAIU the compilation time is mostly affected when templates are actually instantiated and adding more files for parsing doesn't change much. This could be tested if needed because this is only my informed guess.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-06-23 14:11:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-435774653  

Thanks I am ok with the design!  
I am not sure that I understand the need of two interfaces, one with algorithm specific strategies and one for all algorithms.  
  
> I have also 2 proposals which I'd like to discuss along with this:  
>   
>     * removal of spherical-polar CS and making `spherical` CS an alias of `spherical_equatorial`  
>         
>       * the rationale for this proposal is that the support for this CS is limited anyway, there are no users requesting an improvement in this area, and that if a user had data in this CS he/she could simply implement get/access converting to `spherical_equatorial` on the fly.  
  
I strongly agree.  
  
>     * including strategies for an algorithm automatically, together with the algorithm  
>         
>       * this is in order to be conformant with the boost header policy. AFAIU if a user includes an algorithm this algorithm should compile. Our default strategy design prevents that because the user also has to include the default strategy for a coordinate system, he/she has to know which strategy is the default one which is not clear so I think in practice means that either the main `geometry.hpp` or all of the `strategies.hpp` are included anyway. Plus I don't understand what is the reason behind separating algorithms from strategies. AFAIU the compilation time is mostly affected when templates are actually instantiated and adding more files for parsing doesn't change much. This could be tested if needed because this is only my informed guess.  
  
 I am not sure that I understand this question clearly, is it ok for all algorithms to just include the headers of the respective default strategies?

📁 include/boost/geometry/strategies/area_result.hpp

```diff
  45 |+     typename Geometry,
  46 |+     typename Strategy,
  47 |+     bool IsImbrella = strategies::detail::is_umbrella_strategy<Strategy>::value
```

> Username: vissarion  
> Created_at: 2020-06-23 13:21:40 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444218199  

typo `IsUmbrella`


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-06-23 14:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648209846  

> I am not sure that I understand the need of two interfaces, one with algorithm specific strategies and one for all algorithms.  
  
It's the same interface. The difference is the one umbrella strategy groups strategies that are used only by one algorithm. And the other umbrella strategy groups strategies that are used by all algorithms. So if you were using only one algorithm you could #include only this one algorithm and umbrella strategy for only this one algorithm. Buf if you used more algorithms at the same time you could #include the whole library and create only one strategy for all of them and e.g. keep it somewhere and reuse it everywhere.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-06-23 14:59:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648219701  

> I am not sure that I understand this question clearly, is it ok for all algorithms to just include the headers of the respective default strategies?  
  
Currently the only thing that is included by an algorithm is non-specialized `bg::algorithm::services::default_strategy` template definition. Actual strategies are not included, also the ones for which `default_strategy` is specialized. The users has to explicitly include them themselves. Not counting rare cases where the strategies are indeed included for backward compatibility because some parts of the code were moved at some point from algorithm to strategy.  
  
So currently this will compile:  
```  
#include <boost/geometry/area.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
int main()  
{  
    using boost::geometry;  
    typedef model::d2::point_xy<double> point_t;  
    typedef model::polygon<point_t> polygon_t;  
    polygon_t poly;  
}  
```  
  
But if you add the following line the code will not compile even though the algorithm is included:  
```  
double a = area(poly);  
```  
even though the algorithm is included and any strategy is not created nor used explicitly by the user.  
  
The user would also have to include even though no strategy is created in the user's code:  
#include <boost/geometry/strategies/cartesian/area.hpp>  
  
So for each algorithm the users has to know which strategy is the default one for the coordinate system they're using and include it.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-06-23 23:33:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648481381  

To give you a better overview of the design of umbrella strategies I decided to implement it for one more algorithm, i.e. `envelope()` which is more complex than `area()`.

---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-24 11:05:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-436543984  

📁 include/boost/geometry/geometry.hpp

```diff
  57 | 
  58 | #include <boost/geometry/strategies/strategies.hpp>
  59 |+ #include <boost/geometry/strategies2/strategies2.hpp>
```

> Username: barendgehrels  
> Created_at: 2020-06-24 11:05:01 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444816260  

This is not in the PR

> Username: awulkiew  
> Created_at: 2020-06-24 11:31:52 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444828788  

What do you mean?

> Username: barendgehrels  
> Created_at: 2020-06-24 11:35:30 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444830402  

Oops, sorry - it is.


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-24 11:05:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-436544392  

📁 include/boost/geometry/algorithms/detail/envelope/range.hpp

```diff
 102 |+         using range_t = typename boost::range_value<MultiRange>::type;
 103 |+         using strategy_t = decltype(strategy.envelope(std::declval<range_t>(), mbr));
 104 |+         using state_t = typename strategy_t::template multi_state<Box>;
```

> Username: barendgehrels  
> Created_at: 2020-06-24 11:05:38 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444816612  

So this new approach uses C++14 - OK for me.

> Username: awulkiew  
> Created_at: 2020-06-24 11:30:09 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444827945  

Yes, though this particular part is C++11.  
  
I think the only thing from C++14 I used is `auto` return type in umbrella strategies.  
  
Another thing I could use is type_traits's template aliases, so e.g. `std::enable_if_t` instead of `std::enable_if`, but for now I'm using C++11 versions.

> Username: barendgehrels  
> Created_at: 2020-07-01 09:20:17 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448232631  

I'm fine with either - but is there a particular reason to use the C++11 version here?


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-24 11:07:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-436545491  

📁 include/boost/geometry/algorithms/detail/envelope/point.hpp

```diff
  43 |+         // strategy.envelope(point, mbr).apply(point, mbr);
  44 |+         using strategy_t = decltype(strategy.envelope(point, mbr));
  45 |+         strategy_t::apply(point, mbr);
```

> Username: barendgehrels  
> Created_at: 2020-06-24 11:07:34 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444817434  

It looks good.  
But this way strategy state is lost. That might of course be solveable, but it might be good to have an example of that.

> Username: awulkiew  
> Created_at: 2020-06-24 11:21:13 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r444823805  

Yes, I simply replaced the already existing line. It was and it is implemented like that because in case of points the only information that is needed is the CS because envelope for points doesn't use radius nor ellipsoid. But yes, if we want to be fully correct then the line that is currently commented out should be used instead.

> Username: barendgehrels  
> Created_at: 2020-07-01 09:19:43 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448232278  

I tested with `area` and indeed, the strategy is not lost, so it works really well


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2020-06-24 11:13:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648757197  

>    removal of spherical-polar CS and making spherical CS an alias of spherical_equatorial  
  
OK for me, we can do that right away I think. It's not really related to this strategy topic.  
  
>    including strategies for an algorithm automatically, together with the algorithm  
  
Thanks for this and the explanation, yes, I think that will be an improvement. So I agree with that.  
  
I would like to play with this new approach next week, can you create a complete version? I already downloaded a patch but it is incomplete now. No problem, because I was busy with my other patch today, but next week I would like to dive a bit more into this.  
  
Thanks for the start, looks promising!

---

## Comment 9

> Username: awulkiew  
> Created_at: 2020-06-24 11:33:38 UTC  
> Updated_at: 2020-06-24 11:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648765837  

@barendgehrels Ok, I'll polish it, make sure that the full library compiles. I'll also add implementation for `expand()` because it is thightly bound with `envelope()`. Do you want me to add it somewhere else at this point so you could see how it works?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2020-06-24 11:37:16 UTC  
> Updated_at: 2020-06-24 11:51:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-648767445  

>   
>   
> @barendgehrels Ok, I'll polish it, make sure that the full library compiles. I'll also add implementation for `expand()` because it is thightly bound with `envelope()`. Do you want me to add it somewhere else at this point so you could see how it works?  
  
Thank you! `expand` is fine too, that will probably do I think.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2020-07-01 02:48:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-652155356  

@barendgehrels Ok, done. The library compiles. Umbrella strategies are backward compatible. There are some temporary parts gluing old and new strategies.

---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-01 02:51:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-440512459  

📁 include/boost/geometry/algorithms/correct.hpp

```diff
 153 |+ 												<
 154 |+ 													Strategy
 155 |+ 												>::get(strategy)),
```

> Username: awulkiew  
> Created_at: 2020-07-01 02:51:10 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448086433  

I don't know why but it seems my IDE put tabs instead of spaces. I'll fix it tomorrow. And btw you can see here how to convert simple strategy to umbrella strategy if needed.

> Username: barendgehrels  
> Created_at: 2020-07-01 09:18:33 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448231572  

OK, no problem - and indeed, I notice the converter, really convenient


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-01 02:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-440512873  

📁 include/boost/geometry/algorithms/detail/calculate_point_order.hpp

```diff
 302 |+ 															<
 303 |+ 																decltype(strategy.get_area_strategy())
 304 |+ 															>::get(strategy.get_area_strategy()));
```

> Username: awulkiew  
> Created_at: 2020-07-01 02:52:48 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448086782  

Here is another place. Note `decltype` instead of getting the type from the strategy type.


---

## Comment 14

> Username: barendgehrels  
> Created_at: 2020-07-01 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-652269537  

>   
>   
> @barendgehrels Ok, done. The library compiles. Umbrella strategies are backward compatible. There are some temporary parts gluing old and new strategies.  
  
@awulkiew great, thanks a lot for preparing this, appreciated!

---

## Comment 15

> Username: mloskot  
> Created_at: 2020-07-01 08:21:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-652270725  

Although I did not really review the code, but I was tracking the discussion and I like the idea. It solves real issues.

---

## Review 16 [Approved]

> Username: barendgehrels  
> Created_at: 2020-07-01 09:27:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/726#pullrequestreview-440693613  

I love it! So I approve right away (though I realize it still need some minor changes such as `TEMP` comments - but those will come).  
  
I played with it, passing the umbrella strategy and passing individual strategies and passing a geographic strategy with another spheroid model, and these all works well - it also takes over the model correctly (I actually didn't expect that already - so: great).  
  
I understand how it works, broadly, and agree with the approach.  
  
And the mainline is: this is a big improvement above the previous approach without the umbrella. Really good and I think we can extend this to all strategies we need in all our code.  
  
Well done! :+1: :+1:

📁 include/boost/geometry/algorithms/detail/expand/box.hpp

```diff
  56 |+ 		// strategy.expand(box_out, box_in).apply(box_out, box_in);
  57 |+ 		using strategy_t = decltype(strategy.expand(box_out, box_in));
  58 |+ 		strategy_t::apply(box_out, box_in);
```

> Username: barendgehrels  
> Created_at: 2020-07-01 09:21:21 UTC  
> Updated_at: 2020-08-11 21:03:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#discussion_r448233223  

In the past I opposed to the `_t` suffix, but I'm OK with it now. It's standard. No objection.


---

## Comment 17

> Username: awulkiew  
> Created_at: 2020-07-01 21:17:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-652651150  

Thanks for the reviews! I'm glad you're ok with the PR. I don't consider it to be ready to be merged yet. I only wanted to get your feedback if you agree with the approach. It needs more polishing.  
  
Regarding your question @barendgehrels:  
> I'm fine with either - but is there a particular reason to use the C++11 version here?  
* `using` could be omitted and replaced with `typedef`. I don't have a strong opinion about it. Since we're moving to C++14 I though I'll use `using`.  
* `decltype`/`std::declval` is needed here because simple/legacy strategy type defines the state type. And it has to be retreived here instead of in the `apply()` taking two iterators because geometry type aka Ring has to be passed to the umbrella strategy to get the simple/legacy strategy. With that said, I'll look into envelope/expand strategies because currently they are interdependent. It's possible that with umbrella strategies it will be possible to make them more independent, maybe to implement them differently so state will not be needed at all.  
  
I also fixed the tabs. This is why I'm answering here to your question.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2020-07-21 17:26:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-661998217  

@barendgehrels @mloskot I'm currently relocating the old and new strategies and I'm wondering about the reason for inconsistency of namespaces and directory structure. Do you remember what is the reason for:  
- different forms of nouns in namespaces vs directories, e.g. `namespace strategy` vs `strategies`?  
  - but not `policies`  
- different names, e.g. `namespace model` vs `geometries`?  
- different structure for strategies `namespace strategy::area::cartesian` vs `strategies/cartesian/area.hpp`?  
  
@barendgehrels @vissarion @mloskot I'm thinking about moving existing strategies to directory `strategy` (singular noun) and adding `namespace strategies` for new umbrella strategies. Here instead of having CS-based directories (`strategies/cartesian/area.hpp`, `strategies/geographic/area.hpp`), have algorithm-based directories reflecting the namespaces (`strategies/area/cartesian.hpp`, `strategies/envelope/geographic.hpp`). Or this could be done the other way around, so keep CS-based directories, but then change the namespaces and define umbrella strategies like: `bg::strategies::cartesian::area<>`, `bg::strategies::geographic::envelope<>`, etc. Basically the naming should be consistent. What is your preference?

---

## Comment 19

> Username: vissarion  
> Created_at: 2020-07-22 08:23:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-662317163  

> @barendgehrels @vissarion @mloskot I'm thinking about moving existing strategies to directory `strategy` (singular noun) and adding `namespace strategies` for new umbrella strategies. Here instead of having CS-based directories (`strategies/cartesian/area.hpp`, `strategies/geographic/area.hpp`), have algorithm-based directories reflecting the namespaces (`strategies/area/cartesian.hpp`, `strategies/envelope/geographic.hpp`). Or this could be done the other way around, so keep CS-based directories, but then change the namespaces and define umbrella strategies like: `bg::strategies::cartesian::area<>`, `bg::strategies::geographic::envelope<>`, etc. Basically the naming should be consistent. What is your preference?  
  
I am ok with both, slightly prefer the latter maybe because I have used to that structure of files.

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2020-07-22 15:05:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-662507425  

> I'm thinking about moving existing strategies to directory strategy (singular noun) and adding namespace strategies for new umbrella strategies  
  
Yes, you can make it consistent - and I understand you need to discern old and new and can use the other (which should then probably be our final choice).  
  
I'm OK either way, and at this moment I don't remember how it became like it was now - but consistent is fine, thanks for the efforts.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2020-07-22 17:17:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-662579269  

@vissarion   
> I am ok with both, slightly prefer the latter maybe because I have used to that structure of files.  
  
I'm more concerned about the namespaces because my guess is that the users typically don't see the directory structure. They include the whole library and don't care where the files are actually located. I think we should pick the better hierarchy of namespaces taking into account that there could be several strategies for the same algorithm and CS. And only after that when we know which one would be better put the files in the corresponding directories.

---

## Comment 22

> Username: awulkiew  
> Created_at: 2020-07-28 18:34:19 UTC  
> Updated_at: 2020-07-29 06:16:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-665207048  

@vissarion @barendgehrels I think it makes more sense to group strategies first by algorithm and then by CS, so as it is now. It allows to have nicely named strategies for all algorithms as I mentioned earlier, so e.g.: `bg::strategies::area::cartesian`, `bg::strategies::envelope::cartesian` but also `bg::strategies::cartesian` which can be used in all algorithms.  
  
An alternative would be to first group by CS (`bg::strategies::cartesian::area`, `bg::strategies::cartesian::envelope`) but then we can't have a strategy with the CS name for all algorithms because in C++ it's not possible to have a class and namespace using the same name. So a strategy for all algorithms would have to be called differently and probably put in the CS namespace, e.g.: `bg::strategies::cartesian::all`, `bg::strategies::cartesian::algorithms`.  
  
From these two I prefer the former because the naming is consistent and clear. And this means that the directory structure would be adapted to this namespace structure.  
  
Unless you have a different idea or see something I do not?

---

## Comment 23

> Username: vissarion  
> Created_at: 2020-07-29 06:21:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-665457568  

I do not have any objections, the first choice seems fine.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2020-08-05 17:28:55 UTC  
> Updated_at: 2020-08-05 17:47:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-669325997  

Ok, I relocated the strategies:  
- old area, envelope and expand strategies are in directory `boost/geometry/strategy`  
- the corresponding files in `boost/geometry/strategies` are left there but WARNINGS are added that they are deprecated  
- `area_result.hpp` and `default_area_result.hpp` are moved to `algorithms`  
- the files containing umbrella strategies are (all matching namespaces and class names):  
   - `boost/geometry/strategies/cartesian.hpp`  
   - `boost/geometry/strategies/geographic.hpp`  
   - `boost/geometry/strategies/spherical.hpp`  
   - `boost/geometry/strategies/area/cartesian.hpp`  
   - `boost/geometry/strategies/area/geographic.hpp`  
   - `boost/geometry/strategies/area/spherical.hpp`  
   - `boost/geometry/strategies/envelope/cartesian.hpp`  
   - `boost/geometry/strategies/envelope/geographic.hpp`  
   - `boost/geometry/strategies/envelope/spherical.hpp`  
   - `boost/geometry/strategies/expand/cartesian.hpp`  
   - `boost/geometry/strategies/expand/geographic.hpp`  
   - `boost/geometry/strategies/expand/spherical.hpp`  
- implementation details and utils are in `boost/geometry/strategies/detail.hpp` (matching namespace).  
- unspecialized services for umbrella strategies like `default_strategy` for an algorithm are placed in respective directory in `services.hpp` file, e.g. `boost/geometry/strategies/area/services.hpp` (matching namespace).  
- default umbrella strategies for algorithm and CS are specialized in corresponding `boost/geometry/strategies/[algorithm]/[CS].hpp` files.  
  
So the structure is slightly different than it was.  
Are you OK with the current design?  
  
As I mentioned some time ago I propose to include files containing default strategies for all coordinate systems with corresponding algorithms so e.g. the user including `boost/geometry/algorithms/area.hpp` would be able to actually call `area()` with default strategy without the need to include strategies separately. In case of algorithms divided into interface and implementation parts (like envelope and expand) the unspecialized services would be included by interface and the default strategies by the implementation. Are you still OK with that?  
  
Alternatively we could consider adding include files for algorithms in `boost/geometry/` directory, e.g. `boost/geometry/area.hpp` because this would correspond with the namespaces and algorithm names. This is only a quick thought, I didn't consider all of the consequences which would probably also be doing the same for `core` because these metafunctions are in `namespace boost::geometry` as well. So this change is probably too radical.  
  
Another issue. When I moved the old strategies I preserved the old directory structure. However I could also change it to match the namespaces like I did with umbrella `strategies`. Do you prefer to keep the old structure for old strategies or should I match the new one? Have in mind that for some strategies like `side` there may be no corresponding algorithm since they are "building blocks" for other strategies.

---

## Comment 25

> Username: vissarion  
> Created_at: 2020-08-26 10:42:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-680802837  

> As I mentioned some time ago I propose to include files containing default strategies for all coordinate systems with corresponding algorithms so e.g. the user including `boost/geometry/algorithms/area.hpp` would be able to actually call `area()` with default strategy without the need to include strategies separately. In case of algorithms divided into interface and implementation parts (like envelope and expand) the unspecialized services would be included by interface and the default strategies by the implementation. Are you still OK with that?  
  
I am OK.  
   
> Alternatively we could consider adding include files for algorithms in `boost/geometry/` directory, e.g. `boost/geometry/area.hpp` because this would correspond with the namespaces and algorithm names. This is only a quick thought, I didn't consider all of the consequences which would probably also be doing the same for `core` because these metafunctions are in `namespace boost::geometry` as well. So this change is probably too radical.  
  
OK with living it as is for now.  
  
> Another issue. When I moved the old strategies I preserved the old directory structure. However I could also change it to match the namespaces like I did with umbrella `strategies`. Do you prefer to keep the old structure for old strategies or should I match the new one? Have in mind that for some strategies like `side` there may be no corresponding algorithm since they are "building blocks" for other strategies.  
  
I am OK with the old structure.

---

## Comment 26

> Username: awulkiew  
> Created_at: 2020-08-28 14:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/726#issuecomment-682600295  

Thanks for reviews. I'll merge it now so we can start testing it. If needed I'll make changes afterwards.

---
