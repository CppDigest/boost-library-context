# #290 Feature: Improve Variant support (run-time exception, lazy_not_implemented) [Closed]

> Username: awulkiew  
> Created at: 2015-05-05 18:26:54 UTC  
> Updated at: 2024-10-11 21:26:33 UTC  
> Closed at: 2024-10-11 21:26:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/290  

This PR adds 2 new utilities which could be used to improve the Variant support:  
- lazy_not_implemented - a replacement for not_implemented which checks static assertions in `apply()` member functions instead of a struct body. It allows to check if the algorithm is not implemented at compile-time without the static assertion failure. A side effect is the generation of lesser number of errors v.s. not_implemented.  
- is_not_implemented - a tool which can be used to check if e.g. an algorithm is implemented for some specific Geometries. It takes 2 types and a `MetaPolicy` which must define a `struct apply<G1, G2>` defining a value indicating if e.g. an algorithm is implemented. It's specialized for `boost::variant` and "returns" true only if all of the combinations of Variants elements are not defined.  
  
Both of those tools allows to check in compile-time if an algorithm should be able to work for specific Variant types if only the user stored supported Geometries in those Variants. So compile-time errors works like before. In the case of Variants, for supported combinations the code calling the algorithm is generated and for not supported combinations the code throwing `bg::not_implemented_exception` is generated. This allows to use an algorithm like this:  
  
```  
point_t pt;  
polygon_t poly;  
variant<point_t, polygon_t> v1, v2;  
variant<polygon_t> v3;  
variant<point_t> v4;  
  
within(pt, poly); // compiles  
//within(poly, pt); // does not compile  
  
within(v1, v2); // compiles and runs (Pt/Pt)  
  
v1 = poly;  
within(v1, v2); // compiles but throws an exception (Poly/Pt)  
  
within(v3, v4); // does not compile, all combinations not supported, actually only one (Poly/Pt)  
```

---

## Comment 1

> Username: sdebionne  
> Created_at: 2015-05-07 11:43:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-99829599  

This is awesome! I have been struggling on this problem for a while now, trying to find the less intrusive way to implement that functionality.  
  
I like your solution: AFAIU adapting an algorithm means adding an extra step at the variant resolve stage to check if the algo is implemented using is_not_implemented and the MetaPolicy. Getting this MetaPolicy right may be a bit tricky though since not every "not implement feature" are "reported" to the dispatch class. IOW, checking if `dispatch` derived from `nyi::not_implemented_tag` may be not enough. For instance:  
- for algorithms with policy, the policy may not be implemented;  
- for algorithms that call an other algorithm internally, that second algo may not be implemented. For  `within`, I would have thought that testing if `relate` derive from `nyi::not_implemented_tag` would be necessary;  
- for algorithm that have an output (e.g. intersection), the output type should be checked as well.  
  
It could be handy to have a few predefined metapolicy (e.g. one that would check that the dimension and coordinate systems of the geometries are the same) and compose them to build the `within_metapolicy`.  
  
May be some tests with `BOOST_CHECK_THROW` could be added to check the behavior of the `within` function itself ?  
  
Thank you for this excellent PR.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-05-07 12:56:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-99855331  

Indeed you're right. Thanks for bringing those points up!  
  
`within` depends on `relate` and `detail::point_in_geometry()`. So what I think we could do would be to care more about the way how `dispatch::within<>` is specialized. E.g. if the dispatching was falling down to the `relate` then this `dispatch::within<>` should be somehow derived from `not_implemented_tag`, e.g. has `dispatch::relate<>` as a base class.  
  
As for the `Strategy`, unless I'm missing something I think currently using Variants with non-default strategy probably has little sense because currently it's possible to pass only one `Strategy` into an algorithm. Now, if we had only one coordinate system I'd say that a lack of default strategy for an implemented algorithm is a bug. But with various CSes indeed it may be that a Strategy is not implemented, and currently the instantiation of `default_strategy` for not supported combination just fails at compile-time. So we'd also need to add some mechanism similar to `lazy_not_implemented` to strategies. E.g. by default return some `lazy_not_implemented_strategy` derived from `not_implemented_tag`. And then indeed also check it the same way, e.g. on the `resolve_strategy` step.  
  
I'm not sure if we should allow to store in a Variant type Geometries using different dimensions or coordinate systems. The library in general dissallows such usage.  
  
So yes, we could improve the support even more. This is only the first step. Before moving forward I'd like to get feedback from others if they agree that it's a good direction.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2015-05-11 08:57:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-100823462  

This is definitely a good start and the potential issues mentioned above should not stop moving forward!  
  
> I'm not sure if we should allow to store in a Variant type Geometries using different dimensions or coordinate systems. The library in general disallows such usage.  
  
My opinion is that variants of different dimensions or coordinate systems should be allowed. If not, the user has to handle this use case (which is quite common IMHO) upstream and that may be a bit  cumbersome: that would probably mean re-implementing the resolve variant stage. Unless the MetaPolicy is exposed in a public interface of the algorithm and could be customized by the user:  
  
``` cpp  
 template<typename Geometry1, typename Geometry2, typename MetaPolicy = resolve_variant::within_metapolicy>  
 inline bool within(Geometry1 const& geometry1, Geometry2 const& geometry2)  
```  
  
This would give the maximum flexibility at the expense of an additional (defaulted) template parameter.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-06-02 08:08:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-107853068  

So what are we going to do with this PR?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-06-02 08:45:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-107866942  

@brunolalande What is your opinion on this? Is it okay or would you have liked to do something else?

---

## Comment 6

> Username: brunolalande  
> Created_at: 2015-06-02 19:48:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-108075485  

Hi,  
  
I never really had the time to dig for a solution to this problem, and this approach looks promising so I'd give it a go. I would just recommend to try it on a more complicated algorithm just to check we are not going to come across any showstopper that would force us to change directions.  
  
Regarding the situation where algorithm A falls back to B, it has always been my feeling that A's dispatch class should systematically inherit from B's dispatch class. Doing this consistently means we can rely on it in the situation described in this conversation, and many others (I tried to generalize it when I implemented support_status, as it was critical to have correct results). The problem is how to enforce it - technically, inheriting from a dispatch class is not needed to call its apply function, so it's easy to miss. We could reimplement dispatch classes to make this mandatory, by making the apply function protected or something...  
  
Regarding mixing several dimensions/CS in one variant, I'm fundamentally against it but I'd like examples of when this would be needed. Samuel you're saying it's "quite common", what situations do you have in mind?  
  
Regards  
Bruno

---

## Comment 7

> Username: sdebionne  
> Created_at: 2015-06-03 14:54:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-108468372  

> Regarding mixing several dimensions/CS in one variant, I'm fundamentally against it but I'd like examples of when this would be needed. Samuel you're saying it's "quite common", what situations do you have in mind?  
  
Well, "it's quite common" is always easier said than proved! Anyway, here is my use case: I'm building a spatial extension for sqlite, in the same vein than [spatialite](https://www.gaia-gis.it/fossil/libspatialite) but with Boost.Geomtry instead of GEOS.  
  
See a minimalist implementation of ST_Within for example:  
  
``` cpp  
void fnct_within(  
    sqlite3_context *context,  
    int argc,  
    sqlite3_value **argv)  
{  
    GEOMLITE_ASSERT(argc == 2);  
    GEOMLITE_CHECK_ARGS_TYPE(argv[0], SQLITE_BLOB);  
    GEOMLITE_CHECK_ARGS_TYPE(argv[1], SQLITE_BLOB);  
  
    gis::cgeometries_t geom;  
    gis::read_blob(argv[0], geom1);  
    gis::read_blob(argv[1], geom2);  
  
    try  
    {  
        bool b = bg::within(geom1, geom2);  
        sqlite3_result_int(context, b ? 1 : 0);  
    }  
    catch(bg::not_implemented_exception&)  
    {  
        sqlite3_result_error_code(context, SQLITE_MISMATCH);  
    }  
}  
```  
  
`geom1` and `geom2`  is a variant holding any of the OGC SFS types, that is Point, Linestring, Polygon (an their multi counterparts) in 2D and 3D. `read_blob()` parses a BLOB and constructs the variant bounded type that matches the BLOB geometry type (dynamic io).  
  
Supposing that mixed dimensions is not supported, I would need to make sure that `geometry::within()` is not instantiated when dim/CS are different since it would fail to compile. Basically I would have to rewrite the `resolve_variant` stage. I don't say that it's not feasible but it's a bit cumbersome...  
  
Within the framework of this PR, it would be helpfull to let the user override the MetaPolicy.  
  
In return, why are you fundamentally against it ?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-06-03 15:45:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-108491521  

> I'm building a spatial extension for sqlite,   
> in the same vein than spatialite   
> but with Boost.Geometry instead of GEOS.  
  
Perfect!!! That is good news.

---

## Comment 9

> Username: brunolalande  
> Created_at: 2015-06-04 12:32:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-108873565  

Hmm sorry, it looks like I missed one important word in my reply, I meant "I'm NOT fundamentally against it" :-)  
  
Thanks for the example, I was too much focused on situations where you would indeed want to have geometries of different dimensions interact, while the problem is more that you don't know at runtime what will be the dimension. I agree that such cases will arise as soon as you deal with dynamic input.  
  
Regards  
Bruno

---

## Comment 10

> Username: sdebionne  
> Created_at: 2015-06-23 06:55:08 UTC  
> Updated_at: 2015-06-23 06:57:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-114382901  

@awulkiew I have been playing a bit with your PR. With MSVC 12 all is fine but with clang 3.6, I get a compile time error reported by the default strategy. My variant is made of `point_2d_cs`, `linestring_2d_cs`, `polygone_2d_cs` (same dimensions / coordinate systems). It seems that the Strategy is instantiated with `<point_2d_cs, point_2d_cs>` and that triggers an `MPL_ASSERT`. I'm still investigating, but does the tests pass with clang on your side ?  
  
@barendgehrels Thank you for your support! It's a side project though, not a full time commitment, so it's a slow work in progress. The results are rather promising in terms of performance.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-06-23 11:59:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-114467045  

@sdebionne I haven't tested with clang yet. It doesn't surprise me that strategies can fail because I haven't touch them yet, so the old code is used for strategies and as you noticed an MPL_ASSERT is checked there. As I mentioned above the strategies should also be altered in a similar way how the dispatched algorithm were. Sorry that I left this PR hanging but I don't have time right now to play with it. I'm planning to get back to it for a while, but if you'd like to play with it feel free to do it. I wouldn't have anything against closing this PR if you came up with a more mature solution.  
  
I'm also not surprised that something wich should fail doesn't fail with MSVC. We must have in mind that the code should fail with all compilers in a similar way. So the fact that it doesn't is worrying. But does it fail if you pass non-supported combination, e.g. `within(poly, ls)` if the geometries are passed directly, without variants?

---

## Comment 12

> Username: sdebionne  
> Created_at: 2015-06-23 16:31:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-114564273  

@awulkiew Here is what if found that could explain the difference of behavior between the two compilers: Clang is fairly strictly conforming to the two-phase name lookup. However, MSVC has a template parsing model that delays nearly every lookup to instantiation time, which is part of phase 2. This is probably why the code would compile with MSVC (which is non-conforming bu compiling) and not in clang. There is a pretty neat article on the clang blog:  
  
[The Dreaded Two-Phase Name Lookup](http://blog.llvm.org/2009/12/dreaded-two-phase-name-lookup.html)  
  
> But does it fail if you pass non-supported combination, e.g. within(poly, ls) if the geometries are passed directly, without variants?  
  
Yes, it does fail as expected. In a way, MSVC behavior is more user-friendly, as it does no instantiate template that will never been used (the resolve variant stage would throw `not_implemented_exception` before any strategy is actually used). But I agree that the difference of behavior is annoying and a fix [is not schedule for MSVC 2015](http://blogs.msdn.com/b/vcblog/archive/2015/04/29/c-11-14-17-features-in-vs-2015-rc.aspx) so we will have to deal with it:  
  
> The remaining features to be implemented will all involve major overhauls that we just couldn't cram into this release (Expression SFINAE, the C99 preprocessor, and C++98's two-phase name lookup).  
  
Strategies are not currently using `not_implemented`, is there any reason for that ? I'll try to modify the `within` default strategy to use the `lazy_not_implemented` and report here.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2016-01-22 00:27:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-173760258  

@sdebionne Regarding failing with Clang, which algorithms, geometries and coordinate systems have you tested?

---

## Comment 14

> Username: sdebionne  
> Created_at: 2016-03-30 16:10:36 UTC  
> Updated_at: 2016-03-30 16:11:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-203506917  

@awulkiew From the top of my head, I was trying to implement `ST_Within` using the within algorithm and the variant bounded types were `point_2d_cs`, `linestring_2d_cs`, `polygone_2d_cs` (same dimensions / coordinate systems). But in my real life use case, the bounded types are every possible OGC geometries (dynamic input)... Let me know if I can help.

---

## Comment 15

> Username: mloskot  
> Created_at: 2018-03-07 18:34:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-371238081  

@sdebionne   
> I'm building a spatial extension for sqlite, in the same vein than spatialite but with Boost.Geomtry instead of GEOS.  
  
Has this been published in any repository?  
  
@awulkiew What is the status of this PR?

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2024-09-30 18:33:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-2383899255  

@awulkiew this is quite old ;-)  
Can we close it?

---

## Comment 17

> Username: awulkiew  
> Created_at: 2024-10-11 21:25:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/290#issuecomment-2408139983  

@barendgehrels sure, I don't mind.

---
