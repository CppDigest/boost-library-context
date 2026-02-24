# #448 - "Strategy" nomenclature is confusing [Closed]

> Username: cffk  
> Created at: 2018-01-14 17:14:04 UTC  
> Updated at: 2018-01-20 22:39:17 UTC  
> Closed at: 2018-01-20 22:39:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/448  

Boost geometry uses the word "strategy" to mean two different things  
(a) what is calculated  
(b) how it is calculated  
  
This dichotomy is illustrated by 2 spherical methods:  
(a) strategy::distance::cross_track - a particular definition of distance  
(b) strategy::distance::haversine - a particular method of computing the great-circle distance  
  
Novice users have a tough time mastering this distinction which leads to confusing questions like this one on Stack Overflow: "Is the Haversine Formula or the Vincenty's Formula better for calculating distance?"  Here is my [answer](  
https://stackoverflow.com/questions/38248046/is-the-haversine-formula-or-the-vincentys-formula-better-for-calculating-distan#38663071).  
  
The English word "strategy" is usually, I would say, used in the (b) sense.  For example in doing a nearest neighbor calculation, "strategy" would indicate the underlying algorithm (k-D tree, VP tree, etc.) and not the distance metric.  
  
Even if users do understand the distinction and so can the answer the (a) question, many have no good way of assessing the answer to the (b) question.  
  
For that reason, for straightforward calculations, boost should just implement an accurate solution and not expose any b-type strategy.  Thus, I contend it is silly to have a strategy::distance::haversine.  What the user wants is distance::great_circle.  In fact, haversine isn't a terribly good generic way of calculating this since it suffers from excessive round-off for nearly antipodal points.  
  
Similarly  
    distance::pythagoras -> distance->euclidean  
    area::surveyor -> area::plane

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-01-14 23:25:37 UTC  
> Updated at: 2018-01-14 23:34:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/448#issuecomment-357551769  

The design rationale behind elements of the library is described here:  
http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/design.html  
and here for strategies in particular:  
http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/design.html#geometry.design.coordinate_system  
  
In short, a strategy is the definition of a coordinate-system-specific part (as small and encapsulated as possible) of algorithm for given input and output. E.g. in your distance() algorithm example it may define how this part has to be calculated if the distance is between PointLike and Linear geometries, let's say MultiPoint and MultiLinestring. In this case it'd define how distance between Point and a Segment is calculated in some coordinate-system. And `distance::cross_track` defines how the distance in spherical coordinate system should be calculated in this scenario. So you may say it works like a function object in standard algorithm WRT coordinate system.  
  
The name "strategy" exists since the library is in Boost so it wouldn't be wise to change it now.  
  
Regarding the remark about confusion between haversine formula and vincenty formula. As you stated in the answer at SO. One is used to calculate the distance on the surface of the sphere and one on spheroid. It's precisely what strategies are for, to define how various coordinate systems are handled inside the algorithm. In Boost.Geometry you can represent Geometries in more than one coordinate system, using different units, adapt legacy types to Boost.Geometry concepts in order to use them with the library. And then you can pass coordinate-system-specific strategy into an algorithm explicitly or rely on automatic mechanism of default strategies getting information about coordinate system from point-type used by your geometry-type. It's all by desing and is described in the Design Rationale section mentioned above.   
  
And of course the default strategy picked by the algorithm should be the one best suitable for a particular coordinate system. Unfortunately in some cases of geographic CS by default spherical strategies are used inside algorithms. The reason is that it's historical code and in the past there was no geographic formulas/strategies implemented or there were only andoyer and vincenty for Point-Point distance with andoyer used by default. Currently we're implementing more of them. However, I don't agree that by default the most accurate (and also the slowest formulas) should be used. Boost.Geometry is performance-focused, so I think that the formulas should do their job reasonably as fast as possible and only if the user wanted something else he/she should explicitly state it in the code. And secondly, as mentioned above spherical formulas or andoyer is/was used by default so switching to something radically different like jumping to vincenty or your formulas could harm performance for users already using them.  
  
Do I understand correctly that the problem you're stating is that you think that the most accurate formulas should be used by default?  
  
Are you using Boost.Geometry and run into some problems or just comparing it with your library and comenting about the design?  
  
Regarding the naming of strategies I agree that it's confusing and that it's not obvious which strategy should be used in which case. Currently I'm changing some of them and I think they could be all renamed and the interface made consistent accross various coordinate systems. Some of the strategies already have names of the coordinate system (for simplicity, so `cartesian` instead `euclidean` or `plane`), like newly added Segment-Segment intersection strategies. So if we changed other strategies like this e.g.:  
  - `distance::pythagoras` would be `distance::cartesian_pointlike`, `distance::cartesian_pointlike_pointlike` or `distance::cartesian_p_p`  
  - `area::surveyor` would be `area::cartesian`,  
  - `distance::haversine` would be `distance::spherical_pointlike`, `distance::spherical_pointlike_pointlike` or `distance::spherical_p_p`  
  - etc.  
  
The change of area strategies names is actually a part of the PR I prepared recently (https://github.com/boostorg/geometry/pull/441).

---

## Comment 2

> Username: cffk  
> Created at: 2018-01-16 22:46:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/448#issuecomment-358133386  

No, I'm not running into any specific problems with Boost.Geometry.  I'm just grousing that names like haversine and vincenty are focussing users on the relatively less consequential aspect of the calculation (the methods for solving the problem instead of the problem itself).  
  
A follow-up gripe is that some of the methods chosen, e.g., haversine, surveyor, and l'huilier, were promoted by relatively recent papers (published in the last 30 years or so) which ignored older, better established, and more accurate methods.  The problems with surveyor have been fixed and l'huilier has been replaced.  
  
Like I said, this is just a general grumble...  It looks like the changes you propose will lead to saner names.  Thanks.  Please feel free to close this issue.
