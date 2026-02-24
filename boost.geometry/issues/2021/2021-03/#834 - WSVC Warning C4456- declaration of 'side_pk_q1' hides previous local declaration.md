# #834 - WSVC Warning C4456: declaration of 'side_pk_q1' hides previous local declaration [Closed]

> Username: vschoech  
> Created at: 2021-03-26 14:50:06 UTC  
> Updated at: 2022-07-08 08:14:15 UTC  
> Closed at: 2021-05-19 07:16:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/834  

Compiling with boost 1.75.0, MSVC 2019 Version 16.9.2 with compiler options `<WarningLevel>Level4</WarningLevel>` and `<LanguageStandard>stdcpplatest</LanguageStandard>`, I get the following warning:  
  
```  
22>\boost_1_75_0\boost\geometry\algorithms\detail\overlay\get_turn_info.hpp(731,1): warning C4456: declaration of 'side_pk_q1' hides previous local declaration  
22>\boost_1_75_0\boost\geometry\algorithms\detail\overlay\get_turn_info.hpp(608,19): message : see declaration of 'side_pk_q1'  
22>\boost_1_75_0\boost\geometry\algorithms\detail\overlay\get_turn_info.hpp(1414): message : see reference to function template instantiation 'void boost::geometry::detail::overlay::touch<TurnInfo>::apply<UniqueSubRange1,UniqueSubRange2,boost::geometry::segment_intersection_points<TurnPoint,boost::geometry::segment_ratio<boost::long_long_type>>,boost::geometry::policies::relate::direction_type,boost::geometry::detail::overlay::side_calculator<boost::geometry::cartesian_tag,boost::geometry::detail::overlay::robust_subrange_adapter<boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,UniqueSubRange1,RobustPolicy>,boost::geometry::detail::overlay::robust_subrange_adapter<boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,UniqueSubRange1,RobustPolicy>,boost::geometry::strategy::side::side_by_triangle<CalculationType>>,UmbrellaStrategy>(const UniqueSubRange1 &,const UniqueSubRange2 &,TurnInfo &,const IntersectionInfo &,const DirInfo &,const SideCalculator &,const UmbrellaStrategy &)' being compiled  
22>        with  
22>        [  
22>            TurnInfo=turn_info,  
22>            UniqueSubRange1=boost::geometry::detail::get_turns::unique_sub_range_from_section<true,boost::geometry::section<box_type,2>,tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::ever_circling_iterator<boost::geometry::closing_iterator<const ring_type>>,strategy_type,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,  
22>            UniqueSubRange2=boost::geometry::detail::get_turns::unique_sub_range_from_section<true,boost::geometry::section<box_type,2>,tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::ever_circling_iterator<boost::geometry::closing_iterator<const ring_type>>,strategy_type,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,  
22>            TurnPoint=tc::geo::no_adl::pointWORKAROUND<double>,  
22>            RobustPolicy=boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>,  
22>            CalculationType=void,  
22>            UmbrellaStrategy=strategy_type,  
22>            IntersectionInfo=boost::geometry::segment_intersection_points<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::segment_ratio<boost::long_long_type>>,  
22>            DirInfo=boost::geometry::policies::relate::direction_type,  
22>            SideCalculator=boost::geometry::detail::overlay::side_calculator<boost::geometry::cartesian_tag,boost::geometry::detail::overlay::robust_subrange_adapter<boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,boost::geometry::detail::get_turns::unique_sub_range_from_section<true,boost::geometry::section<box_type,2>,tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::ever_circling_iterator<boost::geometry::closing_iterator<const ring_type>>,strategy_type,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,boost::geometry::detail::overlay::robust_subrange_adapter<boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,boost::geometry::detail::get_turns::unique_sub_range_from_section<true,boost::geometry::section<box_type,2>,tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::ever_circling_iterator<boost::geometry::closing_iterator<const ring_type>>,strategy_type,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,boost::geometry::detail::robust_policy<tc::geo::no_adl::pointWORKAROUND<double>,boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>,double>>,boost::geometry::strategy::side::side_by_triangle<void>>  
22>        ]  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-05-12 08:58:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/834#issuecomment-839597477  

Thanks, will fix today!

---

## Comment 2

> Username: wanghan02  
> Created at: 2022-07-08 08:14:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/834#issuecomment-1178692876  

Thanks! The warning is gone in boost 1.79.0.
