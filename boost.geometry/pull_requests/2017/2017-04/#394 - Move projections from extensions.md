# #394 Move projections from extensions. [Merged]

> Username: awulkiew  
> Created at: 2017-04-17 14:39:20 UTC  
> Updated at: 2018-01-23 00:11:11 UTC  
> Merged at: 2018-01-21 01:03:08 UTC  
> Closed at: 2018-01-21 01:03:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/394  

The intention of this PR is to move projections from extensions. It is currently in a "work in progress" state. I created this PR at this stage because I'd like to get feedback regarding the direction I'm going into. So my intention is to modify this description to present current state of the implementation and have the history of the conversation and changes below.  
  
The projections and transformations works as follows:  
- `bg::srs::projection<>` is general, both compile-time and run-time representation of map projection.  
- `bg::srs::transformation<>` is general, both compile-time and run-time representation of transformation between space reference systems.  
- by default types used to define projections and transformations and its parameters are:  
  - run-time: `bg::srs::proj4` passed into the constructor of dynamic projection  
  - compile-time: `bg::srs::static_proj4<>` passed as template parameters  
- by including additional headers it's possible to use predefined EPSG, ESRI and IAU2000 codes:  
  - run-time: `bg::srs::epsg`, `bg::srs::esri`, `bg::srs::iau2000`  
  - compile-time: `bg::srs::static_epsg<>`, `bg::srs::static_esri<>`, `bg::srs::static_iau2000<>`  
  
This is the interface:  
  
    using namespace boost::geometry;  
    using namespace boost::geometry::model;  
    using namespace boost::geometry::srs;  
    using namespace boost::geometry::srs::par4;  
  
    // run-time  
    projection<> prj = proj4("+proj=tmerc +ellps=WGS84 +units=m");  
    projection<> prj = epsg(2000);  
    transformation<> tr = {proj4("+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs"),  
                           proj4("+proj=longlat +ellps=airy +datum=OSGB36 +no_defs")};  
    // compile-time  
    projection<static_proj4<proj<tmerc>, ellps<WGS84>, datum<WGS84> > > prj;  
    projection<static_epsg<2000> > prj;  
    transformation<static_esri<37002>, static_esri<37003> > tr;  
  
In all cases the projection or transformation of any geometry besides Box can be done like this:  
  
    point<double, 2, cs::geographic<degree> > pt_ll(0, 0);  
    point<double, 2, cs::cartesian> pt_xy;  
  
    prj.forward(pt_ll, pt_xy);  
    prj.inverse(pt_xy, pt_ll);  
  
    point<double, 2, cs::geographic<degree> > pt_ll2;  
  
    tr.forward(pt_ll, pt_ll2);  
    tr.inverse(pt_ll2, pt_ll);  
  
There are 2 strategies for `transform()` algorithm:  
- `bg::strategy::transform::srs_forward_transformer<>`  
- `bg::strategy::transform::srs_inverse_transformer<>`  
  
They both take either `projection<>` or `transformation<>` as template argument and pass ctor parameters to the constructor of `projection<>` or `transformation<>`, so it's possible to initialize strategies the same way, e.g.:  
  
    srs_forward_transformer<projection<> > strategy = proj4("+proj=tmerc +ellps=WGS84 +units=m");  
  
    // forward  
    bg::transform(poly_ll, poly_xy, strategy);

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-04-17 15:03:56 UTC  
> Updated_at: 2017-04-18 21:13:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-294504326  

Currently `projection<>` requires passing LL and XX point types as template parameters. AFAIU the intention was to figure out the types for storing parameters and used internally during the conversion. However AFAIU Proj4 constants and the projections themselves are tweaked with type `double` in mind. Furthermore the user in most cases would use `double` to represent geographic points anyway. So IMO requireing to pass 2 point types is an overcomplication. In the next step I'd like to replace it with single `CalculationType` defaulted to `double` and move it into the end of template parameters list. This would allow to simplify the interface even more:  
  
    // run-time  
    projection<> prj = proj4("+proj=tmerc +ellps=WGS84 +units=m");  
    projection<> prj = epsg(2000);  
    // compile-time  
    projection<static_proj4<tmerc> > prj; // by default WGS84 spheroid used  
    projection<static_epsg<2000> > prj;  
  
Are you ok with this?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2017-04-19 08:06:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295152562  

Great that you picked this up! Thanks!

---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-04-19 16:25:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295331906  

@barendgehrels Does it mean that you're ok with replacing `LL` and `XY` point types with single `CalculationType` defaulted to `double`? :)

---

## Comment 4

> Username: mloskot  
> Created_at: 2017-04-19 16:27:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295332482  

@awulkiew I wonder, what issues you have in mind that could lead to objecting such change. Are you afraid of promoting to `double` in case of `XY` holds integral coordinates?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-04-19 17:35:56 UTC  
> Updated_at: 2017-04-19 17:55:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295359204  

@mloskot I'm asking because this is how projections was initially implemented so there might be some rationale behind it which I'm not aware of. At least besides historic reasons since this is how strategies were implemented in the past. Plus there are some issues I could think of (below) but not real problems in practice. Hence I proposed the change, because I think it's good enough.  
  
*TL;DR*  
  
Regarding your question about promotion, if anything I was considering that `double` might be too small for a user storing coordinates on some high precision type. Still, currently the internals taken from proj4 (constants in particular) are implemented with `double` in mind, e.g.:  
  
    static const double EPSILON = 1.0e-7;  
  
(https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/gis/projections/proj/aea.hpp#L67)  
So in order to fully support more precise types we probably would have to tweak the code (constants). Assuming that this even make sense in case of formulas used in a particular projection. So this is why I think that `double` is good enough.  
  
*very TL;DR*  
  
Projection and the corresponding transform strategy works more or less like stateful strategies like area or convex_hull strategies (actually projection is more limited).  
In stateless strategies when `CalculationType` is not passed it's defaulted to void and then in compile time the calculation type is figured out from coordinate types of geometries passed into `apply()` function of a strategy.  
Stateful strategies define the state type. This is why they take geometries types as template parameters. The state type is used by the algorithm and passed into `apply()` function together with geometries, Though in case of these strategies the interface could be modified so it would be possible to generate the state type inside the algorithm from geometries, so no problem here (actually I plan to propose such change in the near future).  
However in case of ~dynamic~ projection this cannot be done because before the projection is created the proj4 parameters are generated from passed proj4 string and then projection's internal parameters are created inside the actual projection type (type-erased inside dynamic projection). So they are created once when the projection is created and the types used to store parameters have to be known before points are passed into the converting function `forward()`.  
So the issue here is that it will behave slightly differently than other strategies. But I don't consider it to be a big problem.

---

## Comment 6

> Username: mloskot  
> Created_at: 2017-04-19 19:18:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295399405  

@awulkiew Thanks for the explanation. No questions, `double` makes sense.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2017-04-19 20:05:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295417848  

@awulkiew Yes, I'm OK with the change to double, thanks for the explanation.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2017-04-20 15:49:20 UTC  
> Updated_at: 2017-04-20 15:54:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-295787709  

I've replaced `LL` and `XY` tparams with one `CalculationType` param.  
  
Now I'd like to work on namespace structure, names etc.  
  
The first problem I see is that `projections` namespace has plural name. The reason is to avoid name clash with class template `projection<>`. But in Boost.Geometry namespaces have singular names: `strategy`, `range`, `math`, `detail`, etc. An exception is the namespace `concepts` but this was changed recently due to a clash with keyword in future standard and is not a part of the interface, still probably should be changed to `concept_` instead, but I digress.  
  
Another thing is that I'd like to make run-time and compile-time semantically as close as possible and separate different kinds of initialization.  
  
So I propose to:  
- move `projection<>` to `boost::geometry` namespace  
- add `bg::proj4` namespace and  
  - move run-time `bg::projections::proj4` to `bg::proj4::parameters`  
  - move compile-time `bg::projections::static_proj4<>` to `bg::proj4::static_parameters<>`  
  - move proj4 projections tags (merc, tmerc, etc.) from `bg::projections` to `bg::proj4::proj`  
  - move proj4 models I added recently from `bg::projections::ellps` to `bg::proj4::ellps`  
- add `bg::epsg` namespace and  
  - move `bg::projections::epsg` to `bg::epsg::code`  
  - move `bg::projections::static_epsg<>` to `bg::epsg::static_code<>`  
  
After applying the changes mentioned above the interface would look like this:  
  
    using namespace boost::geometry;  
    using namespace boost::geometry::proj4;  
  
    // run-time  
    projection<> prj = parameters("+proj=tmerc +ellps=WGS84 +units=m");  
    projection<> prj = epsg::code(2000);  
    // compile-time  
    projection<static_parameters<proj::tmerc, ellps::WGS84> > prj;  
    projection<epsg::static_code<2000> > prj;

---

## Comment 9

> Username: mloskot  
> Created_at: 2017-04-21 08:47:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-296131444  

I think `boost::geometry::proj4` makes sense and it also indicates provenance.  
If a general name is needed, perhaps `boost::geometry::crs` is fine.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2017-04-21 11:38:54 UTC  
> Updated_at: 2017-04-21 15:56:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-296168611  

I suggested `boost::geometry::proj4` in order to use exactly the same names of c++ types as proj4 run-time parameters. If we wanted to place them in `boost::geometry::crs` then they'd probably have different names, e.g. `WGS84` should probably be `wgs84`. I'd also prefer more descriptive names of projections, closer to ESRIWKT. So proj4 run-time and compile-time parameters wouldn't correspond 1:1. And in this case `proj4::static_parameters` would be something different.  
  
Currently we have general `boost::geometry::srs` namespace containing CS models `spheroid<>` and `sphere<>` which also can be passed into static projection. We could put ellipsoids and projection names there, but in this case I think that the names should be different as I mentioned above. Is there a difference between CRS and SRS?  
  
EDIT: Or were you thinking about something like this:  
- `boost::geometry::srs::proj4` - run-time proj4  
- `boost::geometry::srs::static_proj4` - compile-time proj4  
- `boost::geometry::srs::proj` - proj4 projections  
- `boost::geometry::srs::ellps` - proj4 ellipsoid models  
- `boost::geometry::srs::epsg` - run-time EPSG  
- `boost::geometry::srs::static_epsg` - compile-time EPSG  
  
Btw, here is the original thread I proposed the change some time ago:  
http://boost-geometry.203548.n3.nabble.com/Moving-projections-from-extensions-td4026725.html

---

## Comment 11

> Username: mloskot  
> Created_at: 2017-04-21 11:55:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-296171554  

@awulkiew   
> Is there a difference between CRS and SRS?  
  
No, in GIS domain they are synonyms.  
  
Sounds good.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2017-04-22 01:56:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-296339342  

I modified the namespaces.  
  
This PR is getting big. Are there more things that need to be done before moving projections from extensions to the official part of the library?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2017-04-23 02:00:44 UTC  
> Updated_at: 2017-04-23 02:01:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-296414057  

I updated/improved the transform strategies. There are 3 strategies:  
- `bg::strategy::transform::project_transformer<>` - automatically picking forward or inverse projection based on the `cs_tag<>::type` of the second geometry  
- `bg::strategy::transform::project_forward_transformer<>`  
- `bg::strategy::transform::project_inverse_transformer<>`  
  
The interface is similar to `bg::projection<>` and looks like this:  
  
    project_transformer<> strategy = proj4("+proj=tmerc +ellps=WGS84 +units=m");  
  
    // forward  
    bg::transform(poly_ll, poly_xy, strategy);  
    // inverse  
    bg::transform(poly_xy, poly_ll, strategy);

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2017-04-27 08:33:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-297649545  

> This PR is getting big. Are there more things that need to be done before moving projections from extensions to the official part of the library?  
  
I cannot judge that at this moment, but I have no objection to do it in phases. Thanks for all this work.  
  
> project_transformer<> strategy = proj4("+proj=tmerc +ellps=WGS84 +units=m");  
> bg::transform(poly_ll, poly_xy, strategy);  
> bg::transform(poly_xy, poly_ll, strategy);  
  
Looking good now.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2017-05-10 02:48:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-300358881  

I added preliminary version of `transformation<>` (Proj4's `pj_transform()`) allowing to convert between 2 coordinate systems. The interface is similar to `projection<>` and looks like this (in C++11):  
  
    transformation<> tr = { proj4("+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs"),  
                            proj4("+proj=longlat +ellps=airy +datum=OSGB36 +no_defs") };  
  
    tr.forward(point1, point2);  
    tr.inverse(point2, point1);  
  
    tr.forward(range1, range2);  
    tr.inverse(range2, range1);  
  
Not all features are supported yet, i.e. scaling of Height/Z, NAD grids, 3D, unusual axes orientations. These are also not supported by projections.  
  
`transform()` strategy using `transformation<>` according to naming conventions used in the library should be called `transform_transformer` which doesn't make sense. So I'm considering implementing `srs_forward_transformer` and `srs_inverse_transformer` taking either `srs::projection<>` or `srs::transformation<>` as template parameter, so:  
  
    strategy::transform::srs_forward_transformer<srs::projection<>> strategy = proj4("...");  
    strategy::transform::srs_forward_transformer<srs::transformation<>> strategy = {proj4("..."), proj4("...")};  
  
An alternative would be to support transformation of all geometries directly in `srs::projection<>`  and `srs::transformation<>` and remove `project_transformer` and inverse/forward counterparts.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2017-10-13 11:18:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-336424161  

At this point `projection<>` and `transformation<>` allows to transform any geometries using any units.  
I updated EPSG codes and added ESRI and IAU2000 codes from spatialreference.org.  
While finding run-time code I replaced `switch` with binary-search in sorted array.  
Some internals were improved, like support for arbitrary calculation type in projections, throwing exceptions, etc.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2017-10-20 15:16:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-338236352  

I decided to replace `project_transformer`, `project_forward_transformer` and `project_inverse_transformer` being map projection strategies with more general and explicit `srs_forward_transformer` and `srs_inverse_transformer` taking `projection<>` or `transformation<>` as template argument.  
  
I decided to remove the strategy picking forward or inverse projection based on the CS of the input because:  
 - the projection and transformation is defined in a way allowing to pass arbitrary geometry in arbitrary CS and the actual coordinate system is defined by the projection itself  
 - with transformation it's possible to transform between the same kind of coordinate systems (e.g. map projection 1 -> map projection 2 or longlat 1 -> longlat 2)

---

## Comment 18

> Username: awulkiew  
> Created_at: 2017-10-30 19:16:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-340554309  

I moved static parameters to `par4` namespace and added 3 more required to figure out the exact projection type at compile time in all cases.  
  
So at this point `static_proj4<>` is able to take `proj<>`, `ellps<>`, `datum<>`, `o_proj<>` and `guam` static parameters. `ellps<>` can also take user-defined `spheroid<>` and can be then passed into static_proj4<> constructor since now `static_proj4<>` behaves like a Tuple.

---

## Comment 19

> Username: awulkiew  
> Created_at: 2017-10-30 19:32:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-340558704  

@barendgehrels @mloskot @vissarion I think the PR is ready. I'm fairly pleased with the interface. For now projections are not documented. The deadline for merging of major changes is two days from now. I'd like to merge and then do minor fixes if needed and create documentation. I'm aware that this PR is huge so if you were not checking it out periodically then it might be hard for you to catch up and review. So I'd be ok with postponing it in case you needed more time.  
  
What do you think?

---

## Review 20 [Commented]

> Username: vissarion  
> Created_at: 2017-10-31 10:56:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-73122450  

📁 include/boost/geometry/srs/projections/exception.hpp

```diff
  89 |+     static std::string msg(std::string const& proj_name)
  90 |+     {
  91 |+         return std::string("projection (") + proj_name + ") is not invertable";
```

> Username: vissarion  
> Created_at: 2017-10-31 10:56:48 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r147955984  

invertable --> invertible


---

## Review 21 [Commented]

> Username: vissarion  
> Created_at: 2017-10-31 11:31:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-73131220  

📁 include/boost/geometry/srs/projections/impl/pj_ell_set.hpp

```diff
  60 |-     double b = 0.0;
  61 |-     double e = 0.0;
  73 |+     T b = 0.0;
```

> Username: vissarion  
> Created_at: 2017-10-31 11:31:19 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r147963267  

why not just `0`?

> Username: awulkiew  
> Created_at: 2017-10-31 14:09:30 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r148006542  

Because `T` may be `double` or user-defined type, smaller fundamental types are promoted to `double` anyway.


---

## Comment 22

> Username: vissarion  
> Created_at: 2017-10-31 11:51:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-340739403  

Indeed this is very long. I sampled the code a bit but I need more time. I agree with the comments and changes discussed above in this thread. For me it is ok to merge now. I can have a look afterwards.   
  
One question: what about parts of code related to projections but not from proj4 e.g. `formulas/gnomonic_spheroid.hpp`? Do you plan to move them in one place?   
  
Thanks Adam.

---

## Comment 23

> Username: awulkiew  
> Created_at: 2017-10-31 14:37:48 UTC  
> Updated_at: 2017-10-31 14:40:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-340782396  

Good point about spheroidal gnomonic projection. I don't have a clear answer. The problem is that it's possible to run spherical-only projection with ellipsoid model/data, in this case the major axis is taken as the radius of the sphere. This is the standard proj4 behavior. So everything else than that would be inconsistent with proj4. I'm fine with being inconsistent with proj4 if we agree that it's reasonable. But proj4 is quite established and there are many proj4 parameter strings on the internet. The users probably expect consistent behavior.  
  
We could add ellipsoidal/spheroidal gnomonic projection. But I'm not sure if this is what the users would expect because this projection has different properties than the spherical one, i.e. the accuracy decreases with the distance. And as mentioned above, currently major axis is taken if `+ellps` parameter is passed.  
  
We could go even further and throw an exception if ellipsoid was passed into any spherical projection, i.e. the library would force the user to pass also one of the parameters turning ellipsoid into sphere explicitly (e.g. `+R_A` or `+R_V`). The problem is that there is no parameter defining the shere's radius as major axis of ellipsoid passed as `+ellps` parameter, so we'd have to add parameter e.g. `+R_M` which would also be inconsistent with proj4 or the user would have to pass radius value with `+R` parameter explicitly.

---

## Review 24 [Commented]

> Username: vissarion  
> Created_at: 2018-01-18 14:29:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-89799073  

📁 include/boost/geometry/srs/projections/proj/isea.hpp

```diff
 156 |+             template <typename T>
 157 |+             inline
 158 |+             int hexbin2(int horizontal, T const& width, T x, T y,
```

> Username: vissarion  
> Created_at: 2018-01-18 14:29:51 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r162356554  

horizontal parameter is not used


---

## Review 25 [Commented]

> Username: vissarion  
> Created_at: 2018-01-18 14:31:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-89799664  

📁 include/boost/geometry/srs/projections/proj/lsat.hpp

```diff
  78 |+             template <typename Parameters, typename T>
  79 |+             inline void
  80 |+             seraz0(T lam, T const& mult, Parameters& par, par_lsat<T>& proj_parm)
```

> Username: vissarion  
> Created_at: 2018-01-18 14:31:19 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r162357028  

parameter par is not used


---

## Review 26 [Commented]

> Username: vissarion  
> Created_at: 2018-01-18 14:32:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-89800015  

📁 include/boost/geometry/srs/projections/proj/cc.hpp

```diff
 101 |-             void setup_cc(Parameters& par, par_cc& proj_parm)
 117 |+             template <typename Parameters, typename T>
 118 |+             inline void setup_cc(Parameters& par, par_cc<T>& proj_parm)
```

> Username: vissarion  
> Created_at: 2018-01-18 14:32:24 UTC  
> Updated_at: 2018-01-21 00:51:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#discussion_r162357316  

proj_parm parameter is not used


---

## Review 27 [Commented]

> Username: vissarion  
> Created_at: 2018-01-18 14:34:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/394#pullrequestreview-89800749  

There are some unused parameters in some functions (I commented for some but there are a few more). Apart from this I am ok with merging. Thanks

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2018-01-18 18:52:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-358744908  

I'm OK with merging, anyway. And let's do it as soon as possible now we are still early in the release.  
Next Wednesday I still want to view some of the changes, but it is no problem to do that after merging - actually it is more convenient, being able to step through the code.  
Great that we will have projections released!  
Thanks, Barend

---

## Comment 29

> Username: awulkiew  
> Created_at: 2018-01-21 01:03:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/394#issuecomment-359214965  

Thanks. I removed unused parameters. Btw, they are not existing in the recent version of proj4.  
  
I'd prefer to agree about the interface now but if you prefer to have the code merged I'm ok with that too. If needed I'll address any remarks after merging. 1.67 is closed for major changes on 28.02 so we have plenty of time.

---
