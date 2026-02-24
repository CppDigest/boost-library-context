# #998 - buffering features with integer coordinates may result in numeric overflow in segment length calculation [Closed]

> Username: MaartenHilferink  
> Created at: 2022-05-12 22:02:51 UTC  
> Updated at: 2022-09-24 13:37:06 UTC  
> Closed at: 2022-09-24 13:37:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/998  

in "boost/geometry/strategies/cartesian/buffer_side_straight.hpp"  
  
lines 78..83:  
  
        // Simulate a vector d (dx,dy)  
        coordinate_type const dx = get<0>(input_p2) - get<0>(input_p1);  
        coordinate_type const dy = get<1>(input_p2) - get<1>(input_p1);  
  
       // For normalization [0,1] (=dot product d.d, sqrt)  
        promoted_type const length = geometry::math::sqrt(dx * dx + dy * dy);  
  
fail to calculate a correct length when coordinate_type is int and dx * dx + dy * dy should be bigger than INT_MAX, which it technically never is as the result is also an int, which becomes negative when for example dx = dy = 47000.  
  
When the calculation of dx and dy are promoted directly to promoted_type, this problem is fixed:  
  
        // Simulate a vector d (dx,dy)  
        promoted_type const dx = get<0>(input_p2) - get<0>(input_p1);  
        promoted_type const dy = get<1>(input_p2) - get<1>(input_p1);  
  
       // For normalization [0,1] (=dot product d.d, sqrt)  
        promoted_type const length = geometry::math::sqrt(dx * dx + dy * dy);

---

## Comment 1

> Username: MaartenHilferink  
> Created at: 2022-05-12 22:05:43 UTC  
> Updated at: 2022-05-12 22:06:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/998#issuecomment-1125463078  

Tested with: Boost 1.78, but seen in code of Boost 1.79 too.

---

## Comment 2

> Username: MaartenHilferink  
> Created at: 2022-05-13 11:55:46 UTC  
> Updated at: 2022-05-13 12:02:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/998#issuecomment-1125978769  

seen with:  
  
	boost::geometry::strategy::buffer::distance_symmetric<Float64> distStrategy(10.0);  
	boost::geometry::strategy::buffer::join_round          joinStrategy(8);  
	boost::geometry::strategy::buffer::end_round          endStrategy(8);  
	boost::geometry::strategy::buffer::point_circle         circleStrategy(8);  
	boost::geometry::strategy::buffer::side_straight       sideStrategy;  
  
	using PointType = Point<int>; // for example: rd-coordinates in millimeters  
	boost::geometry::model::ring<PointType> tmpRing;  
	boost::geometry::model::polygon<PointType> tmpPolygon;  
	boost::geometry::model::multi_polygon<decltype(tmpPolygon)> currMP, resMP;  
	// read currMP as pand from BAG containing line segments of at least 65m long (> 2^16 mm).  
	boost::geometry::buffer(currMP, resMP, distStrategy, sideStrategy, joinStrategy, endStrategy, circleStrategy);  
  
where the following traits were defined for Point:  
  
	namespace boost::geometry::traits  
	{  
		// define Point<T> as model for concept boost::geometry::point  
		template <typename T> struct tag<Point<T>> { using type = point_tag; };  
		template <typename T> struct dimension<Point<T>> : std::integral_constant<std::size_t, 2> {};  
		template <typename T> struct coordinate_type<Point<T>> { using type = T; };  
		template <typename T> struct coordinate_system<Point<T>> { using type = boost::geometry::cs::cartesian; };  
  
		template <typename T, std::size_t Index>  
		struct access<Point<T>, Index> {  
			static_assert(Index < 2, "Out of range");  
			using CoordinateType = T;  
			static inline CoordinateType get(Point<T> const& p) { if constexpr (Index == 0) return p.X(); else return p.Y(); }  
			static inline void set(Point<T>& p, CoordinateType const& value) { if constexpr (Index == 0) p.X() = value; else p.Y() = value; }  
		};  
	} //  namespace boost::geometry::traits  
  
but also seen with boost::geometry::buffer on boost::geometry::model::linestring<Point<int>>

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-05-14 12:12:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/998#issuecomment-1126704250  

Hi Maarten!  
  
Nice to hear from you!  
  
And thanks for your report! I cannot tackle it immediately but will come back to this soon.  
  
Regards, Barend

---

## Comment 4

> Username: barendgehrels  
> Created at: 2022-06-29 12:52:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/998#issuecomment-1169942800  

Hi Maarten,  
A bit delayed, but I could reproduce it, added a unit test and applied your fix. See #1028   
Thanks again for reporting it, including the fix.
