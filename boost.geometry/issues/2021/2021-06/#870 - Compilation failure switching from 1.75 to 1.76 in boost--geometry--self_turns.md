# #870 - Compilation failure switching from 1.75 to 1.76 in boost::geometry::self_turns [Closed]

> Username: LeoPizzo1  
> Created at: 2021-06-18 09:59:24 UTC  
> Updated at: 2021-10-17 14:26:13 UTC  
> Closed at: 2021-10-17 14:26:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/870  

Hello, I am trying to solve a compilation problem moving from 1.75 to 1.76 on vs2019.  
My function is used to insert points at the self intersection of a trimmed surface uv boundary to ensure that the subsequent   
triangulation will not fail.  
It accepts as input the 2d points as well as the 3d points, find the self intersections in UV space, compute the linearized 3d positions and add to the corresponding 3d vector.  
I adapted my Pt2d structure, so that I can use directly in the polygon.  
  
My code is the following:  
``` C++  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/linestring.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <ptd2d.h>  
#include <pt3d.h>  
  
  
bool BoundaryLoopFix::FixLoop( std::vector<Pt2d> &vUv, std::vector<Pt3d> &vPts )  
{  
 typedef boost::geometry::model::polygon<Pt2d > Polygon;  
  typedef boost::geometry::point_type<Polygon>::type point_type;  
  typedef boost::geometry::detail::overlay::turn_info<point_type, boost::geometry::segment_ratio<double> > TurnInfoType;  
  typename boost::geometry::strategy::intersection::services::default_strategy<typename boost::geometry::cs_tag<Polygon>::type>::type strategy;  
  
  Polygon poly;  
  int idx = 0;  
  poly.outer( ).swap( vUv );  
  
  boost::geometry::detail::no_rescale_policy robust_policy;  
  boost::geometry::detail::self_get_turn_points::no_interrupt_policy interrupt_policy;  
  std::vector<TurnInfoType> turns;  
  boost::geometry::self_turns<boost::geometry::detail::overlay::assign_null_policy>( poly.outer( ), strategy, robust_policy, turns, interrupt_policy );  
  ....  
}  
```  
  
It fails the compilation at boost self turn with the output below.  
Do you have an idea on how it is possible to solve this problem?  
  
Thanks a lot!  
  
Leonardo  
  
```  
1>C:\local\boost\boost\geometry\algorithms\detail\disjoint\box_box.hpp(64,22): error C2672: 'boost::geometry::strategy::intersection::cartesian_segments<void>::apply': no matching overloaded function found  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(101): message : see reference to function template instantiation 'bool boost::geometry::detail::disjoint::disjoint_box_box<Box,Box,Strategy,0>(const Box1 &,const Box2 &,const Strategy &)' being compiled  
1>        with  
1>        [  
1>            Box=box_type,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            Box1=box_type,  
1>            Box2=box_type  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\partition.hpp(722): message : see reference to function template instantiation 'bool boost::geometry::detail::self_get_turn_points::self_section_visitor<false,Geometry,Turns,TurnPolicy,Strategy,RobustPolicy,InterruptPolicy>::apply<boost::geometry::section<Box,2>>(const Section &,const Section &)' being compiled  
1>        with  
1>        [  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            TurnPolicy=turn_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            Box=box_type,  
1>            Section=boost::geometry::section<box_type,2>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\partition.hpp(722): message : see reference to function template instantiation 'bool boost::geometry::detail::self_get_turn_points::self_section_visitor<false,Geometry,Turns,TurnPolicy,Strategy,RobustPolicy,InterruptPolicy>::apply<boost::geometry::section<Box,2>>(const Section &,const Section &)' being compiled  
1>        with  
1>        [  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            TurnPolicy=turn_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            Box=box_type,  
1>            Section=boost::geometry::section<box_type,2>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\partition.hpp(659): message : see reference to function template instantiation 'bool boost::geometry::partition<box_type,boost::geometry::detail::partition::include_all_policy,boost::geometry::detail::partition::include_all_policy>::apply<ForwardRange,VisitPolicy,ExpandPolicy,OverlapsPolicy,boost::geometry::detail::partition::visit_no_policy>(const ForwardRange &,VisitPolicy &,const ExpandPolicy &,const OverlapsPolicy &,size_t,VisitBoxPolicy)' being compiled  
1>        with  
1>        [  
1>            ForwardRange=sections_type,  
1>            VisitPolicy=boost::geometry::detail::self_get_turn_points::self_section_visitor<false,boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,std::vector<TurnInfoType,std::allocator<TurnInfoType>>,turn_policy,boost::geometry::strategy::intersection::cartesian_segments<void>,boost::geometry::detail::no_rescale_policy,boost::geometry::detail::self_get_turn_points::no_interrupt_policy>,  
1>            ExpandPolicy=boost::geometry::detail::section::get_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            OverlapsPolicy=boost::geometry::detail::section::overlaps_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            VisitBoxPolicy=boost::geometry::detail::partition::visit_no_policy  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\partition.hpp(658): message : see reference to function template instantiation 'bool boost::geometry::partition<box_type,boost::geometry::detail::partition::include_all_policy,boost::geometry::detail::partition::include_all_policy>::apply<ForwardRange,VisitPolicy,ExpandPolicy,OverlapsPolicy,boost::geometry::detail::partition::visit_no_policy>(const ForwardRange &,VisitPolicy &,const ExpandPolicy &,const OverlapsPolicy &,size_t,VisitBoxPolicy)' being compiled  
1>        with  
1>        [  
1>            ForwardRange=sections_type,  
1>            VisitPolicy=boost::geometry::detail::self_get_turn_points::self_section_visitor<false,boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,std::vector<TurnInfoType,std::allocator<TurnInfoType>>,turn_policy,boost::geometry::strategy::intersection::cartesian_segments<void>,boost::geometry::detail::no_rescale_policy,boost::geometry::detail::self_get_turn_points::no_interrupt_policy>,  
1>            ExpandPolicy=boost::geometry::detail::section::get_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            OverlapsPolicy=boost::geometry::detail::section::overlaps_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            VisitBoxPolicy=boost::geometry::detail::partition::visit_no_policy  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(171): message : see reference to function template instantiation 'bool boost::geometry::partition<box_type,boost::geometry::detail::partition::include_all_policy,boost::geometry::detail::partition::include_all_policy>::apply<sections_type,boost::geometry::detail::self_get_turn_points::self_section_visitor<false,Geometry,Turns,TurnPolicy,Strategy,RobustPolicy,InterruptPolicy>,boost::geometry::detail::section::get_section_box<Strategy>,boost::geometry::detail::section::overlaps_section_box<Strategy>>(const ForwardRange &,VisitPolicy &,const ExpandPolicy &,const OverlapsPolicy &)' being compiled  
1>        with  
1>        [  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            TurnPolicy=turn_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            ForwardRange=sections_type,  
1>            VisitPolicy=boost::geometry::detail::self_get_turn_points::self_section_visitor<false,boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,std::vector<TurnInfoType,std::allocator<TurnInfoType>>,turn_policy,boost::geometry::strategy::intersection::cartesian_segments<void>,boost::geometry::detail::no_rescale_policy,boost::geometry::detail::self_get_turn_points::no_interrupt_policy>,  
1>            ExpandPolicy=boost::geometry::detail::section::get_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            OverlapsPolicy=boost::geometry::detail::section::overlaps_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(169): message : see reference to function template instantiation 'bool boost::geometry::partition<box_type,boost::geometry::detail::partition::include_all_policy,boost::geometry::detail::partition::include_all_policy>::apply<sections_type,boost::geometry::detail::self_get_turn_points::self_section_visitor<false,Geometry,Turns,TurnPolicy,Strategy,RobustPolicy,InterruptPolicy>,boost::geometry::detail::section::get_section_box<Strategy>,boost::geometry::detail::section::overlaps_section_box<Strategy>>(const ForwardRange &,VisitPolicy &,const ExpandPolicy &,const OverlapsPolicy &)' being compiled  
1>        with  
1>        [  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            TurnPolicy=turn_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            ForwardRange=sections_type,  
1>            VisitPolicy=boost::geometry::detail::self_get_turn_points::self_section_visitor<false,boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,std::vector<TurnInfoType,std::allocator<TurnInfoType>>,turn_policy,boost::geometry::strategy::intersection::cartesian_segments<void>,boost::geometry::detail::no_rescale_policy,boost::geometry::detail::self_get_turn_points::no_interrupt_policy>,  
1>            ExpandPolicy=boost::geometry::detail::section::get_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>,  
1>            OverlapsPolicy=boost::geometry::detail::section::overlaps_section_box<boost::geometry::strategy::intersection::cartesian_segments<void>>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(309): message : see reference to function template instantiation 'bool boost::geometry::detail::self_get_turn_points::get_turns<false,TurnPolicy>::apply<Geometry,IntersectionStrategy,RobustPolicy,Turns,InterruptPolicy>(const Geometry &,const Strategy &,const RobustPolicy &,Turns &,InterruptPolicy &,int,bool)' being compiled  
1>        with  
1>        [  
1>            TurnPolicy=turn_policy,  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            IntersectionStrategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(308): message : see reference to function template instantiation 'bool boost::geometry::detail::self_get_turn_points::get_turns<false,TurnPolicy>::apply<Geometry,IntersectionStrategy,RobustPolicy,Turns,InterruptPolicy>(const Geometry &,const Strategy &,const RobustPolicy &,Turns &,InterruptPolicy &,int,bool)' being compiled  
1>        with  
1>        [  
1>            TurnPolicy=turn_policy,  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            IntersectionStrategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp(359): message : see reference to function template instantiation 'void boost::geometry::detail::self_get_turn_points::self_turns<false,AssignPolicy,Geometry,Strategy,RobustPolicy,Turns,InterruptPolicy>(const Geometry &,const IntersectionStrategy &,const RobustPolicy &,Turns &,InterruptPolicy &,int,bool)' being compiled  
1>        with  
1>        [  
1>            AssignPolicy=boost::geometry::detail::overlay::assign_null_policy,  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy,  
1>            IntersectionStrategy=boost::geometry::strategy::intersection::cartesian_segments<void>  
1>        ]  
1>C:\hcntrepositories\trunk\hcroot\baselibraries\geom\tessellatorvs2019\boundaryloopfix.cpp(93): message : see reference to function template instantiation 'void boost::geometry::self_turns<boost::geometry::detail::overlay::assign_null_policy,boost::geometry::model::ring<Point,true,true,PointList,PointAlloc>,boost::geometry::strategy::intersection::cartesian_segments<void>,boost::geometry::detail::no_rescale_policy,std::vector<TurnInfoType,std::allocator<TurnInfoType>>,boost::geometry::detail::self_get_turn_points::no_interrupt_policy>(const Geometry &,const Strategy &,const RobustPolicy &,Turns &,InterruptPolicy &,int,bool)' being compiled  
1>        with  
1>        [  
1>            Point=Pt2d,  
1>            PointList=std::vector,  
1>            PointAlloc=std::allocator,  
1>            Geometry=boost::geometry::model::ring<Pt2d,true,true,std::vector,std::allocator>,  
1>            Strategy=boost::geometry::strategy::intersection::cartesian_segments<void>,  
1>            RobustPolicy=boost::geometry::detail::no_rescale_policy,  
1>            Turns=std::vector<TurnInfoType,std::allocator<TurnInfoType>>,  
1>            InterruptPolicy=boost::geometry::detail::self_get_turn_points::no_interrupt_policy  
1>        ]  
1>C:\local\boost\boost\geometry\algorithms\detail\disjoint\box_box.hpp(64,1): error C2780: 'Policy::return_type boost::geometry::strategy::intersection::cartesian_segments<void>::apply(const UniqueSubRange1 &,const UniqueSubRange2 &,const Policy &,const ModelledUniqueSubRange1 &,const ModelledUniqueSubRange2 &)': expects 5 arguments - 2 provided  
1>C:\local\boost\boost\geometry\strategies\cartesian\intersection.hpp(334): message : see declaration of 'boost::geometry::strategy::intersection::cartesian_segments<void>::apply'  
1>C:\local\boost\boost\geometry\algorithms\detail\disjoint\box_box.hpp(64,1): error C2780: 'Policy::return_type boost::geometry::strategy::intersection::cartesian_segments<void>::apply(const UniqueSubRange1 &,const UniqueSubRange2 &,const Policy &)': expects 3 arguments - 2 provided  
1>C:\local\boost\boost\geometry\strategies\cartesian\intersection.hpp(315): message : see declaration of 'boost::geometry::strategy::intersection::cartesian_segments<void>::apply'  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-18 10:56:27 UTC  
> Updated at: 2021-06-18 11:00:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/870#issuecomment-863951962  

Hi Leonardo,  
  
Yes, this was caused by the change in strategies handling. Because `self_turns` is an undocumented, semi-internal utility I missed this algorithm while I was adapting the other ones. Basically what should've happen is that the legacy intersection strategy should've been automatically converted to the new umbrella strategy. So what you need to do is to create new kind of strategy instead of the old one.  
  
Either like this:  
```  
typename boost::geometry::strategies::relate::services::default_strategy<Polygon, Polygon>::type strategy;  
```  
or like this:  
```  
boost::geometry::strategies::relate::cartesian<> strategy;  
```  
or like this:  
```  
boost::geometry::strategies::cartesian<> strategy;  
```  
  
@barendgehrels @vissarion Do you think I should add umbrella strategies support in this algorithm? I'm not sure considering it's undocumented and doesn't have a "proper" interface anyway.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-06-23 08:37:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/870#issuecomment-866645835  

@awulkiew understood. I think so, because this function is apparently used and it is useful. We should document it at some point. So it should work like the other functions do.

---

## Comment 3

> Username: vissarion  
> Created at: 2021-06-24 08:33:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/870#issuecomment-867448974  

It looks useful but I cannot comment more on the usability. Also it is partially documented https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/self_turn_points.hpp#L315

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-06-24 15:30:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/870#issuecomment-867733184  

Ok, I'll treat it as a bug then and add automatic conversion from legacy to umbrella strategy.
