# #682 - Will overlay operations handle spikes? [Open]

> Username: gigimava  
> Created at: 2020-03-12 12:06:01 UTC  
> Updated at: 2025-03-26 14:51:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/682  

In a past private email exchange, @barendgehrels mentioned it was planned that all overlay operations would be able to handle spikes (including, in particular, intersection against a degenerated box).  
  
I see that's not the case yet, in boost 1.72.0  
Is this feature still on its way, or has it been scrapped?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-04-15 11:46:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-613989174  

I don't remember the context, and I can't see from your nickname who you are, so I can't look up our conversation.  
  
In general spikes are handled, already for several versions, but there are some exceptions.  
  
If you have a case where it is not handled correctly, you're welcome to create an issue including the WKT of the inputs.

---

## Comment 2

> Username: gigimava  
> Created at: 2020-05-27 11:55:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-634610711  

I'm sorry, my previous message was too vague.  
I contacted you after reading this (now ancient) exchange with my collegue https://lists.boost.org/geometry/2014/11/3249.php  
  
We were having issues when subtracting a degenerated box from a (OGC valid) polygon.  
From boost's documentation, I could get the following  
- boost::geometry::difference (1.59.0) requires its two operands to be OGC-valid : http://www.boost.org/doc/libs/1_59_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html   
- a degenerated box is (in terms of OGC) a polygon consisting of a single spike. Meaning it’s not a legitimate input to difference.  
- difference is able to handle empty geometries, but the concept of emptiness is defined in terms of the set of points. So, a degenerated box is not empty (in fact, boost::geometry::is_empty will always return false for boxes) http://www.boost.org/doc/libs/1_59_0/libs/geometry/doc/html/geometry/reference/algorithms/is_empty.html   
  
So, at that point, it was clearly our responsibility to make sure we were not passing degenerated boxes to boost::geometry::difference.  
  
In response, you mentioned that you were planning to make all overlay operations handle spikes, which should include degenerated rectangles.  
  
Fast forward to now, with boost 1.73.0, I get the following  
  
	int main()  
	{  
		using point = boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian>;  
		using polygon = boost::geometry::model::polygon<point>;  
		using box = boost::geometry::model::box<point>;  
		using multipolygon = boost::geometry::model::multi_polygon<polygon>;  
	  
		polygon poly;  
		boost::geometry::read_wkt("POLYGON((0 0,0 4,4 4,4 0,0 0))", poly);  
	  
		{  
			box b;  
			boost::geometry::read_wkt("BOX(0 1,4 2)", b);  
	  
			multipolygon mpolyResult;  
			boost::geometry::difference(poly, b, mpolyResult);  
			// MULTIPOLYGON(((0 2,0 4,4 4,4 2,0 2)),((0 1,4 1,4 0,0 0,0 1)))  
			std::cout << boost::geometry::wkt(mpolyResult) << std::endl;  
		}  
	  
		{  
			box b;  
			boost::geometry::read_wkt("BOX(0 2,4 2)", b);  
	  
			multipolygon mpolyResult;  
			boost::geometry::difference(poly, b, mpolyResult);  
			// Actual:   MULTIPOLYGON()  
			// Expected: MULTIPOLYGON((0 0,0 4,4 4,4 0,0 0))  
			std::cout << boost::geometry::wkt(mpolyResult) << std::endl;  
		}  
	  
		{  
			polygon polySubtract;  
			boost::geometry::read_wkt("POLYGON((0 2,4 2,0 2))", polySubtract);  
	  
			multipolygon mpolyResult;  
			boost::geometry::difference(poly, polySubtract, mpolyResult);  
			// Actual:   MULTIPOLYGON()  
			// Expected: MULTIPOLYGON((0 0,0 4,4 4,4 0,0 0))  
			std::cout << boost::geometry::wkt(mpolyResult) << std::endl;  
		}  
	  
		{  
			polygon polySubtract;  
			boost::geometry::read_wkt("POLYGON((0 2,4 2,0 2))", polySubtract);  
			boost::geometry::remove_spikes(polySubtract);  
	  
			multipolygon mpolyResult;  
			boost::geometry::difference(poly, polySubtract, mpolyResult);  
			// MULTIPOLYGON((0 0,0 4,4 4,4 0,0 0))  
			std::cout << boost::geometry::wkt(mpolyResult) << std::endl;  
		}  
	  
		return 0;  
	}  
  
This leads me back to the question: can I expect that, at some point, `difference` will handle degenerated boxes? Or have I misunderstood the meaning of "handle", and you deem the current behavior to be appropriate?

---

## Comment 3

> Username: vschoech  
> Created at: 2020-06-17 11:47:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-645325397  

The problem is still not fixed in boost 1.73.0.

---

## Comment 4

> Username: vschoech  
> Created at: 2022-07-08 14:36:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-1179061039  

Apparently, we must still pre-process degenerated boxes so that we do not pass them to boost::geometry::difference(...), in boost 1.79.0.

---

## Comment 5

> Username: vschoech  
> Created at: 2023-08-28 13:40:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-1695722291  

⚠️ Still the same behavior in boost 1.83.0.

---

## Comment 6

> Username: vschoech  
> Created at: 2025-03-26 14:51:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/682#issuecomment-2754712516  

⚠️ Still the same behavior in boost 1.87.0.
