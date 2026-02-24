# #1207 - Offcpu 15ms during boost::geometry::distance in tbb::parallel_for [Closed]

> Username: littlebr369  
> Created at: 2023-10-08 10:20:36 UTC  
> Updated at: 2023-12-04 18:55:26 UTC  
> Closed at: 2023-12-04 18:55:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1207  

We have a tbb::parallel_for function which will calls the boost::geometry::distance function in different threads which are bind in different cpu cores in linux 4.18.This problem only occurs when using multi-thread concurrency.  
Occasionally there will be 15ms offcpu, this is the stack diagram captured by perf.  
![image](https://github.com/boostorg/geometry/assets/63218417/1db68535-b19f-4cf1-9502-5cd0237dbd8b)  
The stack of the last function looks like this  
`boost::geometry::policies::relate::segments_intersection_points<boost::geometry::segment_intersection_points<math::geometry::Point2<double>, boost::geometry::segment_ratio<double> > >::return_type boost::geometry::strategy::intersection::cartesian_segments<void>::unified<boost::geometry::segment_ratio<double>, boost::geometry::strategy::intersection::cartesian_segments<void>::segment_intersection_info<double, boost::geometry::segment_ratio<double> >, boost::geometry::model::referring_segment<math::geometry::Point2<double> const>, boost::geometry::model::referring_segment<math::geometry::Point2<double> const>, boost::geometry::policies::relate::segments_intersection_points<boost::geometry::segment_intersection_points<math::geometry::Point2<double>, boost::geometry::segment_ratio<double> > >, boost::geometry::detail::segment_as_subrange<boost::geometry::model::pointing_segment<math::geometry::Point2<double> const> >, boost::geometry::detail::segment_as_subrange<boost::geometry::model::pointing_segment<math::geometry::Point2<double> const> > >(boost::geometry::strategy::intersection::cartesian_segments<void>::segment_intersection_info<double, boost::geometry::segment_ratio<double> >&, boost::geometry::model::referring_segment<math::geometry::Point2<double> const> const&, boost::geometry::model::referring_segment<math::geometry::Point2<double> const> const&, boost::geometry::policies::relate::segments_intersection_points<boost::geometry::segment_intersection_points<math::geometry::Point2<double>, boost::geometry::segment_ratio<double> > > const&, boost::geometry::detail::segment_as_subrange<boost::geometry::model::pointing_segment<math::geometry::Point2<double> const> > const&, boost::geometry::detail::segment_as_subrange<boost::geometry::model::pointing_segment<math::geometry::Point2<double> const> > const&)`

---

## Comment 1

> Username: vissarion  
> Created at: 2023-12-01 14:29:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1207#issuecomment-1836210241  

It is not clear to me what the error is. Please provide more information about the error as well as a minimal example to reproduce.

---

## Comment 2

> Username: littlebr369  
> Created at: 2023-12-04 14:48:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1207#issuecomment-1838801586  

I have figure out why, it is because ros thread steal the time slice of tbb::parallel_for and make it offcpu. It just occurs at boost functions. Thanks a lot for reply.  
  
  
  
At 2023-12-01 22:29:32, "Vissarion Fisikopoulos" ***@***.***> wrote:  
  
It is not clear to me what the error is. Please provide more information about the error as well as a minimal example to reproduce.  
  
—  
Reply to this email directly, view it on GitHub, or unsubscribe.  
You are receiving this because you authored the thread.Message ID: ***@***.***>
