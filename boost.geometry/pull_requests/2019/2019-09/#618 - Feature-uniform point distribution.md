# #618 Feature/uniform point distribution [Open]

> Username: tinko92  
> Created at: 2019-09-05 07:52:19 UTC  
> Updated at: 2020-04-06 14:01:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/618  

This is the second of three branches that I created during the GSoC. It does not depend on any of the other two branches. It contains uniform_point_distribution (for now in the extension directory), a class modeled after the random number distribution found in Boost.Random and the STL and designed to generate random geometries; in this case, points uniformly distributed on some domain.  
  
Strategies for domains that are pointlike (CS-agnostic), box (n-d cartesian, 2d/3d spherical), segment (everything supported by line_interpolate), linear (everything supported by line_interpolate) or areal (everything that is cartesian or spherical and supported by envelope) are provided. Tests and examples are included, pictures of how the output may look like are found in this blog post:  
https://tinko92.github.io/gsoc2019/2019/09/24/work-product.html (second and fourth picture).  
  
@vissarion @awulkiew This branch slightly differs from the one I submitted to you at the end of GSoC because I replaced some of my implementations for uniform sampling on segments with the usage of line_interpolate. I only noticed the algorithm yesterday which is the reason for this last-minute change.

---

## Review 1 [Changes requested]

> Username: vissarion  
> Created_at: 2019-10-17 10:56:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-301307493  

Thanks for this PR Tinko!  
  
My main comment here is that you create strategies for random points in geometries and you dispatch w.r.t. the geometry type. Currently, in the library this dispatching is happening in the algorithms side and strategies are dispatching w.r.t. coordinate systems. So all the code that is coordinate system unspecific should be moved from `agnostic` strategies to algorithms. Then the code that is going to work only for one coordinate system e.g. `cartesian`  should constitute one (or more) strategy.   
  
Did I understand your intention correctly, or am I missing something?

📁 extensions/example/random/random_example.cpp

```diff
  37 |+     std::default_random_engine generator(1);
  38 |+     polygon poly;
  39 |+     boost::geometry::read_wkt("POLYGON((16 21,17.1226 17.5451,20.7553 17.5451,17.8164 15.4098,18.9389 11.9549,16 14.0902,13.0611 11.9549,14.1836 15.4098,11.2447 17.5451,14.8774 17.5451,16 21))", poly);
```

> Username: vissarion  
> Created_at: 2019-10-14 13:31:06 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334483493  

please reduce line's length

> Username: awulkiew  
> Created_at: 2019-10-18 09:42:42 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336406405  

e.g.:  
  
    boost::geometry::read_wkt("POLYGON((16 21,17.1226 17.5451,20.7553 17.5451,17.8164 15.4098,"  
                                       "18.9389 11.9549,16 14.0902,13.0611 11.9549,14.1836 15.4098,"  
                                       "11.2447 17.5451,14.8774 17.5451,16 21))",  
                              poly);


📁 extensions/test/random/random.cpp

```diff
   2 |+ // Unit Test
   3 |+ 
   4 |+ // Copyright (c) 2019 Tinko Bartels, Berlin, Germany.
```

> Username: vissarion  
> Created_at: 2019-10-14 13:35:41 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334485437  

you can ack gsoc (see other PR)

---

📁 extensions/test/random/random.cpp

```diff
  74 |+     polygon poly;
  75 |+     bg::read_wkt(
  76 |+         "POLYGON((16 21,17.1226 17.5451,20.7553 17.5451, 17.8164 15.4098,18.9389 11.9549,16 14.0902,13.0611 11.9549, 14.1836 15.4098,11.2447 17.5451,14.8774 17.5451,16 21))",
```

> Username: vissarion  
> Created_at: 2019-10-17 09:05:39 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r335890326  

please reduce length


📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  33 |+     typename SideStrategy   //Actually, we need a triangle area strategy here.
  34 |+ >
  35 |+ struct uniform_convex_fan
```

> Username: vissarion  
> Created_at: 2019-10-14 13:41:13 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334488060  

is this really a strategy (i.e. coordinate type specific) or an algorithm? To my point of view agnostic strategies are just algorithms

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  41 |+     // realistically occur in a random sample anyway.
  42 |+ public:
  43 |+     uniform_convex_fan(DomainGeometry const& g)
```

> Username: vissarion  
> Created_at: 2019-10-14 13:48:58 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334491567  

any clue for choosing that name? As far as I know, in geometry, fan is a collection of cones and thus fan makes no sense. Maybe I am missing some notation or something.

> Username: tinko92  
> Created_at: 2019-10-17 22:05:13 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336247920  

The name was chosen based on the fact that this class implicitly uses the fan triangulation of convex polygons for sampling. Reconsidering that choice, the fact that it uses the fan triangulation may be an implementation detail and alluding to it may be unnecessary.

> Username: vissarion  
> Created_at: 2019-10-18 09:18:59 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336395924  

I see, so this is probably not the right name. The name should declare what this class is doing, e.g. `uniform_convex_polygon_sampler` makes sense?

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  66 |+     }
  67 |+     template<typename Gen>
  68 |+     Point apply(Gen& g, DomainGeometry const& d)
```

> Username: vissarion  
> Created_at: 2019-10-14 14:01:05 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334497173  

it is more clear to name it `Generator` instead of `Gen`

> Username: vissarion  
> Created_at: 2019-10-14 14:02:25 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334497750  

also please be consistent; above `g` had `DomainGeometry` type, here `d` is `DomainGeometry` and `g` type `Gen`


📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_hull_rejection.hpp

```diff
  41 |+     typedef typename point_type<DomainGeometry>::type domain_point_type;
  42 |+     typedef boost::geometry::model::ring<domain_point_type> ring;
  43 |+     ring hull;
```

> Username: vissarion  
> Created_at: 2019-10-14 13:51:05 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334492535  

should be `m_hull`


📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_envelope_rejection.hpp

```diff
  32 |+     typename Point,
  33 |+     typename DomainGeometry,
  34 |+     typename BoxStrategy = services::default_strategy
```

> Username: vissarion  
> Created_at: 2019-10-14 14:17:16 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r334504542  

if that was an algorithm instead of agnostic strategy, default strategy could have been dispatched as in other bg algorithms

> Username: awulkiew  
> Created_at: 2019-10-18 21:01:42 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336674464  

Or we could leave it to the user to randomize points and then reject samples when they don't meet some criteria like being within a box in this case.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:46:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303779674  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  39 |+     // It is hard to see a reason not to use double here. If a triangles
  40 |+     // relative size is smaller than doubles epsilon, it is too unlikely to
  41 |+     // realistically occur in a random sample anyway.
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:46:56 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336408280  

The type should probably be taken from area strategy if that's what's being calculated here (not side) .


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:50:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303781590  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  31 |+     typename DomainGeometry,
  32 |+     typename TriangleStrategy,
  33 |+     typename SideStrategy   //Actually, we need a triangle area strategy here.
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:50:21 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336409739  

Would it make sense to use already existing AreaStrategy here? Can this agnostic strategy (or algorithm) be used in noncartesian CS? Side strategy would not allow to calculate areas in general case as you noticed.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303782291  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  44 |+ 	{
  45 |+         accumulated_areas.push_back(0);
  46 |+         for (int i = 2 ; i < g.size() ; ++i) {
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:51:39 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336410255  

We put `{` in the next line.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:52:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303782491  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  57 |+                 uniform_convex_fan const& r_strategy) const
  58 |+     {
  59 |+         if( l_domain.size() != r_domain.size() ) return false;
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:52:01 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336410395  

{  
        return false;  
    }  
  
Yeah, I know it looks ugly. I personally would put `return false;` without brackets in the next line but this is what our guidelines say about formatting.  
  
Btw, @barendgehrels what do you think about allowing single-line returns without brackets?

> Username: vissarion  
> Created_at: 2019-10-21 07:39:24 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336873058  

+1 for one line returns.  
also in `if(` add a space


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:55:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303784161  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  61 |+             if( !boost::geometry::equals(*(l_domain.begin() + i),
  62 |+                                          *(r_domain.begin() + i)))
  63 |+                 return false;
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:55:08 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336411734  

So like that, though in this case it would make sense to have brakcets because it's hard to see the `return false`.

> Username: vissarion  
> Created_at: 2019-10-21 07:38:24 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336872707  

Also, the `for`'s `{` should be in new line  and a space between `if` and `(`


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:57:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303785552  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  57 |+                 uniform_convex_fan const& r_strategy) const
  58 |+     {
  59 |+         if( l_domain.size() != r_domain.size() ) return false;
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:57:31 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336412728  

What is `DomainGeometry`? Is it always STL container? Always `std::vector`? I'm asking because it's a generic type but you call `.size()`. For generic ranges we use Boost.Range's `boost::size()`.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 09:59:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303786587  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  58 |+     {
  59 |+         if( l_domain.size() != r_domain.size() ) return false;
  60 |+         for (int i = 0; i < l_domain.size(); ++i) {
```

> Username: awulkiew  
> Created_at: 2019-10-18 09:59:18 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336413475  

And then here `int` should either be `DomainGeometry::size_type`, `std::vector<...>::size_type` or `boost::size_type<DomainGeometry>::type`. Or at least `std::size_t`.


---

## Review 9 [Commented]

> Username: vissarion  
> Created_at: 2019-10-18 10:21:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-303732886  

I am adding a few more comments. I am stopping here my review since I found them many. I will do a second round after addressing / discussing them.

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  55 |+     bool equals(DomainGeometry const& l_domain,
  56 |+                 DomainGeometry const& r_domain,
  57 |+                 uniform_convex_fan const& r_strategy) const
```

> Username: vissarion  
> Created_at: 2019-10-18 08:23:21 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336372078  

this strategy is unused

> Username: tinko92  
> Created_at: 2019-10-18 21:21:32 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336680604  

I have that parameter because the rhs strategy is used in equal-comparison for other strategies and so the code in uniform_point_distribution passes it. Would it be ok, if I just removed the name (r_strategy)? Or is their a way around the necessity for having all strategies take all values that other strategies could need for comparison?

> Username: vissarion  
> Created_at: 2019-10-21 07:41:01 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336873607  

I think removing the name `r_strategy` would be ok for now

> Username: vissarion  
> Created_at: 2019-10-23 08:14:25 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r337905310  

alternatively you can use `boost::ignore_unused(r_strategy)`

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  69 |+     {
  70 |+         std::uniform_real_distribution<double> dist(0, 1);
  71 |+         double r = dist(g) * accumulated_areas.back(),
```

> Username: vissarion  
> Created_at: 2019-10-18 08:35:58 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336376858  

I cannot find it in guidelines but bg uses one initialization per variable as    
```  
double r = dist(g) * accumulated_areas.back();  
double s = dist(g);  
```  
@barendgehrels right?

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  29 |+ <
  30 |+     typename Point,
  31 |+     typename DomainGeometry,
```

> Username: vissarion  
> Created_at: 2019-10-18 09:24:28 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336398295  

this is either a convex ring or convex polygon (they are actually the same but the can have different types)

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  53 |+         }
  54 |+     }
  55 |+     bool equals(DomainGeometry const& l_domain,
```

> Username: vissarion  
> Created_at: 2019-10-18 09:28:39 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336400120  

I do not see why do you need this function? BG has functions for checking equality of geometries.

> Username: vissarion  
> Created_at: 2019-10-18 09:31:08 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336401271  

Also you check all the possible linestrings of the convex polygons from a fixed point. What if the geometries are equal and they start at different vertices i.e.   `domain.begin()`. What am I missing ?

> Username: tinko92  
> Created_at: 2019-10-18 20:23:57 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336662552  

The notion of equality as defined by the C++ named requirement "RandomNumberDistribution" on which I based my design of random point distributions considers two distribution instances A, B equal if their parameters are equal and they will produce the same output given the same generators, i.e. from A == B and g1 == g2 it needs to follow that A(g1) == A(g2).  
  
Because of the way I generate samples for convex rings, two distributions will only produce the same output if the convex rings start from the same point.

> Username: vissarion  
> Created_at: 2019-10-21 07:43:04 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336874312  

OK, I see, I remember also our discussions on this topic. Please add a comment or document this for future reference

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_fan.hpp

```diff
  76 |+                              accumulated_areas.end(),
  77 |+                              r));
  78 |+         return TriangleStrategy::template map
```

> Username: vissarion  
> Created_at: 2019-10-18 10:19:46 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336421264  

why `map` and not `sampling` or something that declares sampling from a `triangle` with `TriangleStrategy`?


📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_hull_rejection.hpp

```diff
  68 |+     {
  69 |+         Point p;
  70 |+         do{
```

> Username: vissarion  
> Created_at: 2019-10-18 09:40:01 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336405301  

The intersection of `hull` with `d` must be non-empty otherwise you end up in infinite loop. More interestingly, if dim(d) < 2 e.g. `d` is a linestring, or point, you end up in a long (if not endless) loop because the probability for hitting the line or point by sampling in an intersecting polygon is very small.

> Username: tinko92  
> Created_at: 2019-10-18 21:11:23 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336677545  

By the implementation of uniform_point_distribution which is the user of all those strategies, hull will always be the convex hull of d, hence their intersection must be non-empty if d has a non-empty interior. I can see that this is problematic since the strategy in itself is an exposed interface that could be misused by passing different geometries to constructor and apply-call. I will defer that issue since it may be rendered moot by future refactoring because of the question of what belongs into strategies.  
  
If d (as an areal geometry) has an empty interior (area is zero), I consider it to be invalid input since I am sampling from the interior with the areal types. I understand that this will need to be documented.

> Username: vissarion  
> Created_at: 2019-10-21 07:45:15 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336875079  

Yes, please put (at least) a comment here.

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_hull_rejection.hpp

```diff
  36 |+     typename SideStrategy   //Actually, we need a triangle area strategy here.
  37 |+ >
  38 |+ struct uniform_convex_hull_rejection
```

> Username: vissarion  
> Created_at: 2019-10-18 09:46:57 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336408291  

is there any reason to have rejection sampling with convex polygons that are not envelopes (boxes)? Since it is more difficult to generate random points in polygon than boxes.

> Username: tinko92  
> Created_at: 2019-10-18 20:54:58 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336672489  

They have different performance characteristics.  
  
Rejection sampling with envelope:  
cost of initialization: obtain the envelope  
cost per sample: (sample from the envelope (constant) + evaluate within-predicate (linear, I guess) ) * ( area of envelope / area of geometry)  
  
Rejection sampling with convex hull:  
cost of initialization: obtain the convex hull  
cost per sample: (sample from the convex hull (logarithmic in convex hull size) + evaluate within-predicate (linear, I guess) ) * (area of convex hull / area of geometry)  
  
Now, (area of convex / area of geometry) is usually smaller and at most equal to ( area of envelope / area of geometry) and the other factor should be dominated by the work to check for within. I guess the computation of the convex hull is more expensive than the computation of the envelope. So I'd expect rejection sampling with the convex hull to be slower if few samples are requested but faster if many samples are requested.  
  
I did some benchmarks and was able to confirm that, if you are interested I can create a gist with the code and the results.

> Username: awulkiew  
> Created_at: 2019-10-18 20:58:41 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336673514  

I'm wondering why do we need rejection sampling at all? Are there any benefits of this solution vs. forcing the user to reject samples? AFAIU with this strategy it's the same as randomizing points using non-rejection sampling strategy and then manually rejecting samples if they are not within a polygon or a convex hull of a polygon. Or am I missing something?

> Username: tinko92  
> Created_at: 2019-10-18 21:51:01 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336688681  

I guess that depends on the interface one wants to provide. E.g. consider uniform_real_distribution. It takes a domain from the user, even though it could just provide random numbers in [0,1], since mapping that to [a,b] is trivial, but for some reason they decided to add those constructor parameters.  
  
My intended design for uniform_point_distribution was such that a user of this facility could just pass some geometry to the constructor and get the expected results. I could remove both rejection strategies and then the uniform_point_distribution would not work with arbitrary polygons. The user would have to call envelope or and use within or, if he wants more performance for a higher number of samples, use convex_polygon. As a related anecdote, until near the end of GSoC I was unaware of the existence of line_interpolate, so user users might also not know all facilities.  
  
Another point is that the advantage of having an interface where the constructor of uniform_point_distribution takes polygons directly may speed up code that uses it that way, once a faster algorithm for sampling in polygons has been implemented. For example, if Boost.Geometry some day gets an algorithm for the triangulation of non-convex polygons then that would allow a strategy that is faster than the rejection-based strategies.

> Username: vissarion  
> Created_at: 2019-10-21 08:04:04 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336881904  

@tinko92 if you have the code and results already, making them public in a gist will benefit all of us. There are also (at least) two more parameters that play a role in trade-offs between those two rejection samplers, the ratio of area(envelope)/area(polygon) and the size of the hull. But I agree that since there should be a better way of sampling (via non-convex triangulations) spending more time in comparisons between those two could be redundant.

> Username: tinko92  
> Created_at: 2019-10-21 11:11:57 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336959475  

@vissarion Here is the gist: https://gist.github.com/tinko92/21d72e4dac4b38f3723511960d3613ca

> Username: awulkiew  
> Created_at: 2019-10-22 16:31:54 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r337623958  

@tinko92 Right, it makes sense. The interface allows to pass a geometry and internally various algorithms can be used during randomization. Whether it is rejection sampling or not doesn't really matter.


📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_envelope_rejection.hpp

```diff
  59 |+         typedef typename point_type<DomainGeometry>::type domain_point;
  60 |+         domain_point p;
  61 |+         do {
```

> Username: vissarion  
> Created_at: 2019-10-18 09:43:05 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336406587  

similarly with above comment that could end up in infinite loop

---

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_envelope_rejection.hpp

```diff
  61 |+         do {
  62 |+             p = m_env_strat.apply(g, m_env);
  63 |+         }while( !::boost::geometry::within(p, d) );
```

> Username: vissarion  
> Created_at: 2019-10-18 09:55:53 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336412078  

space missing in `}while`


---

## Comment 10

> Username: vissarion  
> Created_at: 2019-10-18 11:55:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-543699150  

@tinko92 one last comment, in gsoc project you also included the documentation of the code. Is it going to be in a different PR, or will be left as future work?

---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 20:59:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-304134532  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_convex_hull_rejection.hpp

```diff
  34 |+     typename DomainGeometry,
  35 |+     typename TriangleStrategy,
  36 |+     typename SideStrategy   //Actually, we need a triangle area strategy here.
```

> Username: awulkiew  
> Created_at: 2019-10-18 20:59:09 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336673693  

Could existing area strategy be used here?


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 21:07:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-304138157  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_linear.hpp

```diff
  40 |+             >::type
  41 |+ >
  42 |+ struct uniform_linear_single
```

> Username: awulkiew  
> Created_at: 2019-10-18 21:07:45 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336676422  

This also looks like an algorithm. Are there any other strategies defined for single linear geometries?


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 21:08:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-304138493  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_linear.hpp

```diff
  58 |+                 typename coordinate_type<Point>::type
  59 |+             >::type sample_type;
  60 |+         std::uniform_real_distribution<sample_type> dist(0, length(d));
```

> Username: awulkiew  
> Created_at: 2019-10-18 21:08:33 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336676670  

It's `bg::length()` right? If that's the case then it needs pt-pt distance strategy.


---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-18 21:15:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-304141153  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_linear.hpp

```diff
  61 |+         sample_type r = dist(g);
  62 |+         Point p;
  63 |+         boost::geometry::line_interpolate(d, r, p);
```

> Username: awulkiew  
> Created_at: 2019-10-18 21:15:16 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r336678737  

`line_interpolate()` needs a strategy as well.  
  
Would it make sense to make `uniform_linear_single` a part of the randomization algorithm (so algorithm dispatched for single linear geometries) and add umbrella strategy (or 3, one for each CS) containing getters for `distance`/`length` and `line_interpolate` strategies?  
  
It's also the case for multi strategy below and in fact AFAIU in both cases (single and multi) the same umbrella strategy could be used because below also `length` and `line_interpolate` is called.


---

## Comment 15

> Username: tinko92  
> Created_at: 2019-10-20 23:22:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-544303381  

Thanks for all the comments, I already addressed a couple of them in comments and some with a commit. I think the main question, though, is the matter of strategies and algorithms.  
  
I am not yet sure how to best change my code to address the problem that it dispatches strategies w.r.t. geometry tag. Here is what makes it difficult:  
- the uniform_point_distribution is a little different from algorithms. It is a class meant to be used in such a way that the user obtains an instance that he may use multiple times (I think it is not always clear how many random points one might need before starting to process them). It makes sense to keep the instance because there is some work that only needs to be done once per domain before generating samples.  
- for some geometry types such as polygons or rings, multiple implementations are sensible. Rejection sampling via envelope has a lower one-time cost, rejection sampling via convex hull has lower cost per sample if more than a couple of samples are needed.  
- The class has some methods that are the same for all geometries and coordinate systems, but operator() and operator== depend on the implementations.  
  
So what would be the preferable way to make parts of the class implementation interchangeable and extensible? It seems to be a situation where one could use the strategy pattern, but I understand that strategies should only be used for coordinate_system dependent differences.

---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-21 17:32:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-304728020  

📁 include/boost/geometry/extensions/random/uniform_point_distribution.hpp

```diff
  51 |+     };
  52 |+     uniform_point_distribution(DomainGeometry const& domain)
  53 |+         : m_strategy(domain),
```

> Username: awulkiew  
> Created_at: 2019-10-21 17:32:11 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r337145436  

It should be possible to pass a CS-specific strategy into the distribution somehow. If not in the constructor then maybe in the operator(). It's because a strategy could take CS-specific parameters like radius in spherical CS or spheroid in geographic CS. This is another argument backing up the division of CS-specific part and algorithm-specific part of strategies that are currently implemented. EDIT: And by that I mean that e.g. `uniform_linear_single` would work in all coordinate systems if the user passed correct length and linear interpolation strategies into it.


---

## Comment 17

> Username: awulkiew  
> Created_at: 2019-10-21 17:45:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-544628083  

@tinko92 I think we should have another level of abstraction between a CS-specific part of what is now a strategy and algorithmic part. Note that currently the CS-specific part is not really exposed, because the algorithms are called with default strategies in random strategies. E.g. in case of `uniform_linear_single` CS-specific part would be pt-pt distance strategy passed into `length` and `line_interpolate` strategy.  
  
I think we need additional layer somewhere between the standard random-generator and uniform-point-distribution which would be at the algorithms/distribution side and then on the strategies side there would be CS-specific part. But I'm not sure what the interface should look like. The easiest thing would be to implement it as various point distributions and then have one default distribution able to take any geometry as a Domain. Another solution would be to take Policy in distribution, or a static tag defining the algorithm used internally. But I don't really like these ideas. I feel that it would be better to divide distribution, distributing/randomizing algorithm, CS-specific-strategy and generator somehow and combine them by either passing in constructors or operator(). I'm not sure how exactly though.

---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-22 14:37:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-305259999  

📁 include/boost/geometry/extensions/random/strategies/agnostic/uniform_envelope_rejection.hpp

```diff
  91 |+ > : public uniform_envelope_rejection<Point, DomainGeometry> {
  92 |+     typedef uniform_envelope_rejection<Point, DomainGeometry> base;
  93 |+     using base::base;
```

> Username: awulkiew  
> Created_at: 2019-10-22 14:37:38 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r337557452  

In Boost.Geometry `services::default_strategy` is not the strategy itself but instead it's a utility defining the type of the default strategy as `type` type.


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-22 14:40:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/618#pullrequestreview-305262441  

📁 include/boost/geometry/extensions/random/uniform_point_distribution.hpp

```diff
  31 |+                 Point,
  32 |+                 DomainGeometry
  33 |+             >
```

> Username: awulkiew  
> Created_at: 2019-10-22 14:40:31 UTC  
> Updated_at: 2020-04-06 12:59:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#discussion_r337559330  

This is not how `default_strategy` would typically be used. It should define `type` member type and be used like this:  
  
    typename xxx::services::default_strategy<X, Y, Z>::type


---

## Comment 20

> Username: awulkiew  
> Created_at: 2019-10-22 16:36:37 UTC  
> Updated_at: 2019-10-22 16:49:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-545048823  

There are 4 parts of the randomization:  
- distribution  
- randomizing algorithm  
- CS-specific strategy  
- generator  
  
Randomizing algorithm has to know the domain/geometry and may need to preprocess data once and store the results for future use. This means it needs geometry and CS-specific strategy during construction (passed into the constructor). Then it has to be called while randomizing and for that it again needs CS-specific strategy and generator.  
  
Let's start from the obvious. Generator is std or Boost.Random engine. So this is known and has to be passed into the `operator()` of the distribution.  
  
Then the strategy. I propose to contain the CS-specific parts needed by all randomizing algorithms in one umbrella strategy. So to have 3 strategies, one for each CS, e.g.:  
  
    bg::strategy::random::cartesian<CalculationType> sc;  
    bg::strategy::random::spherical<RadiusOrSphere, CalculationType> ss(r); // Radius if needed.  
    bg::strategy::random::geographic<Formula, Order, Spheroid, CalculationType> sg(spheroid); // Order if needed  
  
And internally define strategies used by whatever is needed by all of the algorithms: distance, interpolation, convex_hull, envelope, expand, etc. Maybe add a new strategy if there is a specific method of randomization for some CS like `uniform_inverse_transform_sampling` and also define it in the umbrella strategy.  
  
Then there is distribution and algorithm. I can think of several possible solutions:  
  
1. Integrate the algorithm and distribution:  
  
```  
namespace bgr = boost::geometry::random;  
// naming is only an example, cartesian strategy used in all cases  
bgr::uniform_point_distribution_linear_xxx<Linear, Point, Strategy> d1(linear, sc);  
bgr::uniform_point_distribution_linear_yyy<Linear, Point, Strategy> d2(linear, sc);  
bgr::uniform_point_distribution_box_zzz<Box, Point, Strategy> d3(box, sc);  
// default algorithm based on Geometry, user-defined strategy  
bgr::uniform_point_distribution<Geometry, Point, Strategy> dd(g, sc);  
// default algorithm and strategy  
bgr::uniform_point_distribution<Geometry> dd(g);  
  
std::mt19937 gen;  
Point p1 = d1(gen);  
Point pd = dd(gen);  
```  
  
  * Pros:  
    * the relation between geometries, strategies and algorithms is clear.  
  * Cons:  
    * long names.  
  
2. Pass algorithm as a policy or tag.  
  
```  
// arguments may need some reordering  
bgr::uniform_point_distribution<Linear, bgr::linear_xxx, Point, Strategy> d1(linear, sc);  
bgr::uniform_point_distribution<Linear, bgr::linear_yyy, Point, Strategy> d2(linear, sc);  
bgr::uniform_point_distribution<Box, bgr::box_zzz, Point, Strategy> d3(box, sc);  
// default algorithm  
bgr::uniform_point_distribution<Geometry, bgr::default_policy, Point, Strategy> ds(g, sc);  
// default algorithm and strategy  
bgr::uniform_point_distribution<Geometry> d(g);  
```  
  
  * Pros:  
    * more expressive than integrated solution above  
    * actually needed if there are different distributions and the same algorithms can be used in several of them  
  * Cons:  
    * the user has to care more about the geometry-policy pairing  
    * if implemented as tags then it requires to add a more templates  
    * if implemented as policies with `uniform_point_distribution` taking them as template template parameter then all policies has to have the same number of template parameters in order to `uniform_point_distribution` define the policy type for all of them the same way.  
  
3. Pass algorithm as optional part of the Domain  
  
```  
bgr::uniform_point_distribution<bgr::linear_xxx<Linear>, Point, Strategy> d1(linear, sc);  
bgr::uniform_point_distribution<bgr::linear_yyy<Linear>, Point, Strategy> d2(linear, sc);  
bgr::uniform_point_distribution<bgr::box_zzz<Box>, Point, Strategy> d3(box, sc);  
// default algorithm  
bgr::uniform_point_distribution<Geometry, Point, Strategy> ds(g, sc);  
// default algorithm and strategy  
bgr::uniform_point_distribution<Geometry> d(g);  
```  
  
  * Pros:  
    * more expressive than integrated solution above  
    * actually needed if there are different distributions and the same algorithms can be used in several of them  
    * the algorithm and geometry relation is clear  
  * Cons:  
    * the semantics of the Domain is not clear since it may be a geometry or optionally an algorithm with geometry  
    * the difference between this interface and std random interface is bigger  
  
There is also an issue mentioned in one of the points above. That is, what different distributions there might be aside from `uniform_point_distribution` and could the same algorithms be used in them as well? What would be the common part? Rephrasing the questions: could other distributions be built on top of uniform distribution or would they require some different input? It's like with std random distributions and engines. Engines always return a random integer in some range and this is used by all distributions to generate e.g. floating point numbers. Would something like this (well defined interfacing based on a very simple operation) be possible with various geometrical distributions whatever they might be? Probably not because even now there is a specific algorithm randomizing in convex polygon so the basic operation in this case is quite complex.  
  
If only certain algorithms can be used with certain distributions then probably dividing them into 2 parts doesn't make sense.  
  
Btw, it's not clear to me. Is `uniform_convex_polygon_sampler` cartesian-only or CS-agnostic?  
  
What do you think?  
Do you have different ideas?  
Is there something I didn't take into account?

---

## Comment 21

> Username: tinko92  
> Created_at: 2019-10-23 22:07:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-545656470  

@awulkiew  thank you for your elaborate comment. I'll address the easy questions first:   
  
- Is `uniform_convex_polygon_sampler` cartesian-only or CS-agnostic? I think `uniform_convex_polygon_sampler` in itself is CS-agnostic, however, it requires a CS-specific strategy for sampling on a spherical triangle. I did not implement that because I only created `uniform_convex_polygon_sampler` close to the deadline back then and the spherical geometry of directly sampling uniformly on spherical triangles seemed a little too involved for me to implement it before the end of GSoC back then.  
  
- What different distributions there might be aside from `uniform_point_distribution` and could the same algorithms be used in them as well? I have considered the question and I could think of random walks, Gaussian distributions, and stratified distributions. Out of those three, the last one might reuse algorithms implemented for the uniform distribution.  
  
I will think more about the designs you proposed. Some tentative comments though:  
- I think the policy solution is strictly superior to the tag solution because with tags the user has to provide the same information but the whole thing is less extensible and the single parts less composable.  
- I feel like the policy solution is the most intuitive. We are always doing the same thing (obtaining uniformly distributed points in some geometry), so we always use the same interface but we have an optional parameter to optionally switch between implementations.  
  
I have a question regarding the policy solution: What is the difference between a strategy and a policy (conceptually)? Some sources suggest, it is the same thing.

---

## Comment 22

> Username: vissarion  
> Created_at: 2019-10-24 09:19:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-545828181  

I also lean towards the policy solution, since it seems possible that some algorithms could be used to sample from other distributions as well. In particular, rejection sampling can be used to sample from Gaussian (or any other distribution by first sampling in a bounding box). Random walks (that referred above) can also be used to sample from various distributions (depends on the particular random walk though). All in all, I think it is a good idea to have that interface and not integrate the algorithm with the distribution.   
  
Regarding policies vs strategies in Boost Geometry strategies are used (or supposed to be used, since there is no official documentation for this) only to separate the CS specific part of the algorithm. We use policies for the other "CS-non-specific" uses.

---

## Comment 23

> Username: awulkiew  
> Created_at: 2019-10-24 16:24:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-545996503  

> I think the policy solution is strictly superior to the tag solution  
  
This is my suggestion number 2, correct? You differentiate between policies and tags while I considered policies and tags as the same interface, so interface 2. At least from the user's perspective they would look the same. So it seems I was not precise enough and I'd like to make sure we're on the same page. So the difference I was originally thinking about would be something like this:  
  
* 2.1 Policies  
  
```  
template <typename Domain, typename Point>  
struct policy  
{  
    // static_assert(is_compatible_with_Domain);  
  
    policy(Domain const&, Strategy const&) {}  
  
    template <typename Generator>  
    Point apply(Generator const&, Strategy const&) { return Point(); }  
};  
  
// if void is used below then this is not needed  
template <typename Domain, typename Point>  
struct default_policy {};  
  
template  
<  
    typename Domain,  
    template Strategy = default_strategy, /*or void, though we already have this type*/  
    template <typename, typename> typename Policy = default_policy, /*or void*/  
    typename Point = typename point_type<Domain>::type, /*or void*/  
>  
struct distribution  
{  
public:  
    distribution(Domain const& d, Strategy const& s)  
        : m_strategy(s)  
        , m_policy(d, m_strategy)  
    {}  
  
    template <typename Generator>  
    Point operator()(Generator const& g)  
    {  
        // do something here  
  
        // or instead of Generator pass something else here,  
        // something which has the characteristic of the distribution  
        Point p = m_policy.apply(g, m_strategy);  
  
        // do something here  
  
        return p;  
    }  
  
private:  
    Strategy m_strategy;  
    Policy<Domain, Strategy, Point> m_policy;  
};  
```  
  
Note that in this case all policies has to have the same interface:  
  
```  
template <typename Domain, typename Point> struct policy2 {};  
```  
  
* 2.2 Tags  
  
The only difference is that Tags are used to indirectly define the Policy.  
  
```  
namespace detail {  
template <typename Tag, typename Domain, typename Point = typename point_type<Domain>::type>  
struct policy_type : not implemented<Tag> {};  
}  
  
template <typename Domain, typename Point> struct policy {};  
  
struct policy_tag {};  
  
namespace detail {  
template <typename Domain, typename Point>  
struct policy_type  
{  
    typedef policy<Domain, Point> type;  
};  
}  
  
struct default_policy_tag {};  
  
template <typename Domain, typename Tag = default_policy_tag /*, ...*/>  
struct distribution  
{  
    /*...*/  
    typename detail::policy_type<Tag, Domain /*, ...*/>::type m_policy;  
};  
  
```  
  
So then in the future if we e.g. need to add a policy taking more types than the other policies we're able to modify the internals like `policy_type` and everything works. While in the previous case (2.1) It wouldn't. I'm not sure if this is needed though because if we faced the need to change the Policy concept then it would also interfere with the distributions, i.e. we'd have to pass some additional information to them in the first place in order to pass it further into the Policy.  
  
So this is the different between Policies and Tags I had in mind. Are we talking about the same thing?  
  
Of course instead of template template parameter we could take the Policy as a type but it'd mean we'd force the user to duplicate the types like that:  
  
```  
distribution<Domain, policy<Domain, Point>, Strategy, Point> d;  
```

---

## Comment 24

> Username: tinko92  
> Created_at: 2020-03-17 22:24:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-600329442  

I am sorry for the long delay. I will resume work on this PR and I will attempt to implement the policy solution in the near future and commit it to this PR.

---

## Comment 25

> Username: tinko92  
> Created_at: 2020-04-04 12:05:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-609018605  

Hello @awulkiew , I have reconsidered your solutions 2.1 und 2.2 and I genereally lean towards solution 2.1.  
  
I have tried to consider the sensible design space for uniform random point generation and how to properly separate the non-cs/cs concerns into policies and strategies. I came up with the following proposal:  
  
For pointlike domains, everything is trivial, no strategy/policy questions.  
For linear, single domains (segments), the policy is trivial, the strategy is a matter of interpolation and calculating length.  
  
For linestrings and multilinestrings, one needs to obtain the total length L and interpolate.  
policies:  
1. walk over all subsegments, store cumulative lengths for each segment and total length L, sample uniform number in [0, L], find appropriate subsegment via a) linear or b) binary search, interpolate output point on selected subsegment  
2. obtain total length L, sample number in [0, L], linear search for appropriate subsegment and interpolate (constant memory)  
strategies required: length, uniform map to segment (interpolate)  
  
For areal domains there are more sensible solutions, depending on the situation  
policies:  
1. Obtain envelope, sample uniformly from envelope until sample point is determined to be within domain.  
2. Obtain convex hull, implicitly use the fan triangulation, store cumulative triangle areas and total area A, sample uniformly in [0,A], find appropriate triangle via a) linear or b) binary search, uniformly sample on triangle, repeat until sample point is determined to be within domain.  
3. Obtain triangulation, then proceed as in 2. but no within-test is required.  
strategies:  
for 1.: envelope strategy, uniform map to envelope (box) type [new], within strategy  
for 2.: convex hull strategy, triangle area strategy (side strategy), uniform map to triangle [new], within strategy   
for 3.: triangulation strategy, triangle area strategy (side strategy), uniform map to triangle [new]  
  
The ideas for areal domains should work similarly for volumes as well.  
  
My GSoC 2019 does not include implementations for all of this (e.g. I do not have an algorithm to triangulate arbitrary polygons) but a lot of this and I should be able to quickly refactor my code to match this proposed layout.  
  
Consequences of these considerations:  
- Strategies will only be used to uniformly map numbers to geometric primitives (such as lines, boxes, triangles) depending on the coordinate system. They do not need to have a state, i.e. their apply-method can be static. The new strategies are not necessarily specific to the application of random point generation, like it is the case for interpolate.  
- Policies are templated types, they take different kinds of strategies. Maybe they can be template template, such that the user does not need to respecify domain and point type which are already specified in the uniform_distribution interface.  
  
I hope these remarks present my proposed refactoring clearly, if not it should become more clear with the next commit.

---

## Comment 26

> Username: tinko92  
> Created_at: 2020-04-06 13:19:44 UTC  
> Updated_at: 2020-04-06 14:01:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/618#issuecomment-609789521  

I have committed and pushed changes that move non-specific code into policies.  
  
Please consider this commit WIP. I opted to push this early, so that it can discussed whether this general approach is appropriate with regard to the paradigms of Boost.Geometry and is a sound design, generally. If this approach is deemed correct, I will do one more check of the formatting, unnecessary headers, and so on, and rebase everything, so that it is a single commit that can be applied to the current develop branch.  
  
Having said that, I will give a summary of the changes:  
The new interface for uniform_point_distribution is:  
`template<typename DomainGeometry, typename Point, typename Policy> uniform_point_distribution`  
The parameter Policy has a default value that depends on DomainGeometry and Point.  
  
There are the following policies:  
single_point, multi_point (trivial, take no strategies)  
linear_single, linear_multi (for segments and line strings, take two strategies for distance and line_interpolate)  
box (for box domains, takes a box-strategy, see below)  
envelope_rejection (for areal and volumetric domains, takes three strategies: envelope, within, box, see below)  
convex_polygon (for convex rings and polygons, takes a triangle-strategy, see below, and an area strategy)  
convex_hull_rejection (for areal domains, takes all strategies for convex_polygon and additionally, a within-strategy)  
  
There are the following strategies:  
uniform_point_distribution::cartesian_box  
uniform_point_distribution::spherical_box  
uniform_point_distribution::cartesian_triangle  
  
These are basically for boxes and triangles, what line_interpolate is for segments. They map numbers from [0,1] to the primitive geometries (boxes or triangles) in a uniform way, which is the CS-specific aspect of the uniform point distributions. Here is an illustration of what the strategies do:  
https://tinko92.github.io/images/triangle.svg  
https://tinko92.github.io/images/box.svg  
https://tinko92.github.io/images/spherical_box.svg (this is from a large square on the northern hemisphere, mapped back to cartesian coordinates)  
  
The implementation that I managed to get working in the end is closer to 2.1, but I take the policy as a type. I am aware that this causes the problem of having to duplicate template parameters that you mentioned in the end, but I don't think the policies can all have the same interface because the number of strategies that they take depends on the policy.

---
