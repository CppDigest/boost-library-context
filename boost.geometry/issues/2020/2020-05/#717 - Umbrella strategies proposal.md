# #717 - Umbrella strategies proposal. [Closed]

> Username: awulkiew  
> Created at: 2020-05-25 23:05:16 UTC  
> Updated at: 2022-10-19 13:09:33 UTC  
> Closed at: 2022-10-19 13:09:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/717  

This is a followup of a discussion about umbrella strategies from this PR: https://github.com/boostorg/geometry/pull/708 and a comment (from somewhere else?) which I'm unable to find right now.  
  
THE PROBLEM  
  
I'd like to propose an interface of umbrella strategies. I had several goals in mind:  
* The interface should be clear for the user and easy to use without the need to know all small details of the algorithms.  
* The naming should be clear.  
* The strategies should be easy to extend, i.e. modular.  
  
Additional info:  
* The purpose of strategies is to define CS-specific parts of an algorithm so the naming has to involve the CS name and the algorithm name.  
* Several strategies can be used by an algorithm, including strategies of the same algorithm but different geometries.  
* In some rare cases there are interdependencies between algorithms, so an algorithm 1 can depend on algorithm 2 and vice versa at the same time.  
  
THE SOLUTION  
  
An example of what I'm proposing from user's perspective:  
```  
double d = bg::distance(pt1, pt2, bg::strategies::distance::cartesian<>());  
bg::envelope(ls, box, bg::strategies::envelope::cartesian<>());  
bool b = bg::intersects(pt1, pt2, bg::strategies::intersection::cartesian<>());  
```  
Notice clear naming and also that the scope of these particular umbrella strategies is the whole algorithm, i.e. they can be passed for all combinations of geometries. The design is modular so it is possible to implement an umbrella strategy for only one or several combinations of geometries or to have the same strategy for all algorithms, e.g.:  
```  
bg::strategies::cartesian<> cartesian;  
double d = bg::distance(pt1, pt2, cartesian);  
bg::envelope(ls, box, cartesian);  
bool b = bg::intersects(pt1, pt2, cartesian);  
```  
I think that we should focus on algorithm-scope and cs-scope umbrella strategies and to not bother with the combinations of geometries.  
  
THE IMPLEMENTATION  
  
We have to go one more level of abstraction above what we currently have as strategies. No matter what other/unrelated strategies are there in the umbrella strategy any algorithm should be able to access whatever subset of strategies it needs and pass the umbrella strategies to whatever other lower-level algorithms that are called internally. This is why I propose to have umbrella strategies with clearly-named member functions returning simple strategies (the ones with `apply()`). So any algorithm would call a member function of the same name as the algorithm, get the simple strategy and call it, then if needed pass the umbrella strategy to some other algorithms.  
  
Like that (these are combination-specific implementations, so what would typically be in `namespace dispatch`):  
```  
template <typename Point, typename Box, typename UmbrellaStrategy>  
inline void envelope_p(Point const& pt, Box & b, UmbrellaStrategy const& umbrella_strategy)  
{  
    auto strategy = umbrella_strategy.envelope(pt, b);  
    strategy.apply(pt, b);  
}  
  
template <typename Point1, typename Point2, typename UmbrellaStrategy>  
inline bool intersects_p_p(Point1 const& pt1, Point2 const& pt2, UmbrellaStrategy const& umbrella_strategy)  
{  
    auto strategy = umbrella_strategy.relate(pt1, pt2); // or intersects  
    return strategy.apply(pt1, pt2);  
}  
  
template <typename Point1, typename Point2, typename UmbrellaStrategy>  
inline double distance_p_p(Point1 const& pt1, Point2 const& pt2, UmbrellaStrategy const& umbrella_strategy)  
{  
    if (intersects_p_p(pt1, pt2, umbrella_strategy)) // note that the original umbrella_strategy is passed  
        return 0;  
    auto strategy = umbrella_strategy.distance(pt1, pt2);  
    return strategy.apply(pt1, pt2);  
}  
```  
  
So with this design it is possible to pass any umbrella strategy containing any subset of simple strategy getters as long as the minimal subset needed by a particular algorithm and all other lower level algorithms is available.  
  
Now, the implementation of strategies. The getters for particular combinations are enabled with `std::enable_if` and for each algorithm there is a convenient base class defining only the simple strategies of a given algorithm. This allows to create compound strategies easily. This probably will have to be replaced by something different in spherical and geographic to avoid storing multiple instances of the earth model in bigger compound strategies. But for the time being, here is how it looks like:  
```  
namespace boost { namespace geometry  
{  
  
namespace strategies  
{  
  
namespace envelope  
{  
  
template <typename CalculationType>  
struct cartesian_base  
{  
    template <typename Geometry, typename Box>  
    static typename std::enable_if  
        <  
            std::is_base_of<pointlike_tag, typename tag<Geometry>::type>::value,  
            strategy::envelope::cartesian_point  
        >::type  
    envelope(Geometry const&, Box const&)  
    {  
        return strategy::envelope::cartesian_point();  
    }  
  
    template <typename Geometry, typename Box>  
    static typename std::enable_if  
        <  
            std::is_base_of<linear_tag, typename tag<Geometry>::type>::value,  
            strategy::envelope::cartesian_segment<CalculationType>  
        >::type  
    envelope(Geometry const&, Box const&)  
    {  
        return strategy::envelope::cartesian_segment<CalculationType>();  
    }  
};  
  
template <typename CalculationType = void>  
struct cartesian : cartesian_base<CalculationType>  
{};  
  
} // namespace envelope  
  
namespace relate  
{  
  
template <typename CalculationType>  
struct cartesian_base  
{  
    template <typename Geometry1, typename Geometry2>  
    static typename std::enable_if  
        <  
            std::is_base_of<pointlike_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<pointlike_tag, typename tag<Geometry2>::type>::value,  
            strategy::within::cartesian_point_point  
        >::type  
    relate(Geometry1 const&, Geometry2 const&)  
    {  
        return strategy::within::cartesian_point_point();  
    }  
  
    template <typename Geometry1, typename Geometry2>  
    static typename std::enable_if  
        <  
            std::is_base_of<pointlike_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<linear_tag, typename tag<Geometry2>::type>::value  
            || std::is_base_of<linear_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<pointlike_tag, typename tag<Geometry2>::type>::value,  
            strategy::within::cartesian_winding<void, void, CalculationType>  
        >::type  
    relate(Geometry1 const&, Geometry2 const&)  
    {  
        return strategy::within::cartesian_winding<void, void, CalculationType>();  
    }  
};  
  
template <typename CalculationType = void>  
struct cartesian : cartesian_base<CalculationType>  
{};  
  
} // namespace relate  
  
namespace distance  
{  
  
template <typename CalculationType>  
struct cartesian_base  
{  
    template <typename Geometry1, typename Geometry2>  
    static typename std::enable_if  
        <  
            std::is_base_of<pointlike_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<pointlike_tag, typename tag<Geometry2>::type>::value,  
            strategy::distance::pythagoras<CalculationType>  
        >::type  
    distance(Geometry1 const&, Geometry2 const&)  
    {  
        return strategy::distance::pythagoras<CalculationType>();  
    }  
  
    template <typename Geometry1, typename Geometry2>  
    static typename std::enable_if  
        <  
            std::is_base_of<pointlike_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<linear_tag, typename tag<Geometry2>::type>::value  
            || std::is_base_of<linear_tag, typename tag<Geometry1>::type>::value  
            && std::is_base_of<pointlike_tag, typename tag<Geometry2>::type>::value,  
            strategy::distance::projected_point<CalculationType>  
        >::type  
    distance(Geometry1 const&, Geometry2 const&)  
    {  
        return strategy::distance::projected_point<CalculationType>();  
    }  
};  
  
template <typename CalculationType = void>  
struct cartesian : distance::cartesian_base<CalculationType>  
                 , relate::cartesian_base<CalculationType>  
{};  
  
} // namespace distance  
  
template <typename CalculationType = void>  
struct cartesian : distance::cartesian_base<CalculationType>  
                 , envelope::cartesian_base<CalculationType>  
                 , relate::cartesian_base<CalculationType>  
{};  
  
} // namespace strategies  
  
}} // namespace boost::geometry  
```  
  
So with the above, it is possible to use all of it like that:  
```  
using namespace boost::geometry;  
  
using point = model::point<double, 2, cs::cartesian>;  
using box = model::box<point>;  
  
point pt1{ 0, 0 }, pt2{ 1, 1 };  
box b;  
  
// algorithm-scope umbrella strategies  
envelope_p(pt1, b, strategies::envelope::cartesian<>());  
bool r1 = intersects_p_p(pt1, pt2, strategies::relate::cartesian<>());  
double d1 = distance_p_p(pt1, pt2, strategies::distance::cartesian<>());  
  
// cs-scope umbrella strategies  
strategies::cartesian<> cartesian;  
envelope_p(pt1, b, cartesian);  
bool r2 = intersects_p_p(pt1, pt2, cartesian);  
double d2 = distance_p_p(pt1, pt2, cartesian);  
```  
  
What do you think?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-06-03 08:37:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/717#issuecomment-638049711  

@awulkiew thanks a lot for the proposal, really good if we go forward with the Umbrella Strategy.  
  
I wanted to react earlier already but these things take some time.  
  
I like the examples from user perspective. I assume it is still possible to be more specific (for example: specify Haversine) or less specific (pass no strategy at all), such that all most common scenarios don't break.  
  
I also like the explicit example that we can specify one strategy for multiple algorithms, which is what we want indeed.  
  
About the implementation, I didn't dive into all the details yet - but is it also possible to implement it tag-dispatch-based instead of `enable_if`? That was really the choice we made, and I remember we started with enable_if (that was before you were on board) and have lots and lots of problems with that, all solved by tag-dispatching. So if possible I really would prefer avoiding SFINAE here and I think that should be possible. Maybe it will be slightly more verbose - but C++14 might help there too. Can you think about this?  
  
In the very last example, why do you write `envelope_p` here, and the other functions with suffices of `_p` and not just envelope/etc? Because that is what the user calls (internally we can be more specific indeed).

---

## Comment 2

> Username: vissarion  
> Created at: 2020-06-03 10:35:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/717#issuecomment-638110588  

I also agree and like the design proposal in general. Thanks @awulkiew !  
  
I have some comments regarding the user perspective. For naming I think we should use the same for both algorithm and strategy. For example even in this case    
```  
bool b = bg::intersects(pt1, pt2, bg::strategies::intersection::cartesian<>());  
```   
although there is  clear naming the user should know or search what is the correct name of the strategy for `intersects` algorithm. Moreover, if both algorithm and strategy have the same name then there is some redundancy so maybe it is better to use   
```  
bool b = bg::intersects(pt1, pt2, bg::strategies::cartesian<>());  
```  
  
At this point I have a question regarding the design, not of this proposal but in general. Where is the right place to store the coordinate system type? Right now this is stored in geometries and used to deduct the default strategy. But if a user defined specific strategy is used then the CS of the geometry is overlooked. But this is not the case for the units of the CS, i.e. the user defined strategy overlook the coordinate system but uses its units (and in case of cartecian CS it treats coordinates as radians). This is to me a bit unclear as a design and also allow the user to do weird things that are not easy to identify e.g.   
```  
typedef boost::geometry::model::d2::point_xy<double> point_type;  
  
point_type p1{1,2};  
point_type p2{0,2};  
  
std::cout << boost::geometry::distance(p1, p2) << std::endl;  
std::cout << boost::geometry::distance(p1, p2, boost::geometry::strategy::distance::haversine<>()) << std::endl;  
std::cout << boost::geometry::distance(p1, p2, boost::geometry::strategy::distance::andoyer<>()) << std::endl;  
```  
```  
1  
0.401719  
2.56932e+06  
```  
I think that the minimum would be here to throw a compile time error if the CS of the geometry is different that the CS implied by the strategy.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-06-03 11:45:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/717#issuecomment-638143900  

@barendgehrels  
  
> I assume it is still possible to be more specific (for example: specify Haversine) or less specific (pass no strategy at all)  
  
Yes, I omitted the default and creation of umbrella strategy from other strategies from the example. I assumed that UmbrellaStrategy would be the default strategy created at the top-most level of the algorithm if no strategy is passed and that there would be a backward-compatibility mechanism of creating umbrella strategy from simple strategy like haversine.  
  
> About the implementation, I didn't dive into all the details yet - but is it also possible to implement it tag-dispatch-based instead of enable_if?  
  
Yes it would be possible to dispatch with tags. Thought it would require to implement more structs and their specializations. AFAIU SFINAE was avoided in the past because older compilers didn't support it properly. With C++14 requirement this shouldn't be a problem.  
  
> In the very last example, why do you write envelope_p  
  
These algorithms are only examples. I didn't want to obscure the strategies implementation with the algorithms implementation. You may look at them as if they were parts of the algorithm dispatched for geometries, so what is typically implemented in `namespace::dispatch`.  
  
@vissarion   
  
> For naming I think we should use the same for both algorithm and strategy.   
  
I agree.  
  
> At this point I have a question regarding the design, not of this proposal but in general. Where is the right place to store the coordinate system type? Right now this is stored in geometries and used to deduct the default strategy. But if a user defined specific strategy is used then the CS of the geometry is overlooked. But this is not the case for the units of the CS, i.e. the user defined strategy overlook the coordinate system but uses its units (and in case of cartecian CS it treats coordinates as radians). This is to me a bit unclear as a design and also allow the user to do weird things that are not easy to identify (...)  
  
Right, I'm not sure how we should address this issue. Because this compiled since the beginning, because there is no CS information in strategies, because it is possible to get radians from cartesian geometries, etc. I thought that the intention is that the strategy defines the CS because this is the only way to do consistent handling of the CS when you can pass an arbitrary strategy with unknown CS. This is why I expressed it in our earlier discussions and also in this Issue: https://github.com/boostorg/geometry/issues/694 (so it's possible I was wrong there). And this is why I also proposed the `cs::undefined`: https://github.com/boostorg/geometry/pull/533 which was merged so now it is possible to avoid defining CS in a geometry (thought it's not documented).  
  
Yes, we could do so the the library fails to compile when incompatible geometry and strategy is passed and if `cs::undefined` CS is defined in geometry then the explicit strategy is required (so no default). This would be more clear. It would require to store CS information in strategies, at least the tag of the CS.  
  
As you noticed units are another issue. For now they are taken from geometry in all cases and by default they are `radians` (for `cs::cartesian` and `cs::undefined`). For consistency I'd say that conditionally they should also be defined in strategy, but I don't know really.  
  
There is also another thing, i.e. distinction between `spherical_equatorial` and `spherical_polar`. In most cases `spherical_polar` is not supported (my guess is that nobody uses it) and in rare cases where it is supported the distinction is made based on geometry as well. The solution to this issue however is simple: remove `spherical_polar` altogether. I doubt anybody uses it.

---

## Comment 4

> Username: vissarion  
> Created at: 2020-06-03 12:38:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/717#issuecomment-638169954  

>There is also another thing, i.e. distinction between spherical_equatorial and spherical_polar. In most cases spherical_polar is not supported (my guess is that nobody uses it) and in rare cases where it is supported the distinction is made based on geometry as well. The solution to this issue however is simple: remove spherical_polar altogether. I doubt anybody uses it.  
  
I agree.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2020-06-10 08:28:04 UTC  
> Updated at: 2020-06-10 08:28:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/717#issuecomment-641834217  

> Yes it would be possible to dispatch with tags. Thought it would require to  
> structs implement more and their specializations. AFAIU SFINAE was avoided   
> in the past because older compilers didn't support it properly.  
> With C++14 requirement this shouldn't be a problem.  
  
That is true. I'm OK if we try it like this, your example looks good.  
  
> I think that the minimum would be here to throw a compile time error   
> if the CS of the geometry is different that the CS implied by the strategy.  
  
This was implemented intentionally. It is not uncommon to apply cartesian strategies on geographic coordinates, on small scales you just can do that (you will lose some precision). As Adam said, it was always supported like this. I don't think it is a problem.  
  
> The solution to this issue however is simple: remove spherical_polar altogether  
  
Fine for me.
