# #22 - boost::icl::interval_map::operator+= : entry doesn't get added if value is zero. [Open]

> Username: afluriach  
> Created at: 2019-03-07 20:07:03 UTC  
> Updated at: 2019-11-20 11:17:27 UTC  
> Url: https://github.com/boostorg/icl/issues/22  

When inserting an entry into a `boost::icl::interval_map<float, int>`, the entry is not inserted if the value is 0. I am guessing this is a bug that happens when the value evaluates to a boolean false. note: using VS2017, C++14. Issue is present in 1.59 - 1.69 (including C++17 for 1.69)  
  
```  
  
#include <iostream>  
#include <vector>  
#include <boost/icl/interval_map.hpp>  
  
using namespace std;  
typedef pair<float, float> float_pair;  
  
boost::icl::interval_map<float, int> makeIntervalMap(const vector<float_pair>& intervals)  
{  
	boost::icl::interval_map<float, int> result;  
	int intervalCount = 0;  
  
	for (float_pair entry : intervals) {  
		result += make_pair(  
			boost::icl::interval<float>::right_open(entry.first, entry.second),  
			intervalCount++  
		);  
	}  
  
	return result;  
}  
  
const vector<float_pair> intervals = {  
	make_pair(200.0f,250.0f),  
	make_pair(100.0f,199.0f),  
	make_pair(0.0f,50.0f),  
};  
  
int main()  
{  
	boost::icl::interval_map<float, int> m = makeIntervalMap(intervals);  
  
	for (auto it = m.begin(); it != m.end(); ++it) {  
		cout << it->first.lower() << ", " << it->first.upper() << endl;  
	}  
}  
```  
  
In the same code, the interval_map has one fewer element than it should, with the interval corresponding to value 0 missing, Changing it to use a ones-based index works as a workaround. And if I remove the post-increment and try to assign every interval the value zero, none of them get added.

---

## Comment 1

> Username: jbelloncastro  
> Created at: 2019-11-20 11:15:28 UTC  
> Updated at: 2019-11-20 11:17:27 UTC  
> Url: https://github.com/boostorg/icl/issues/22#issuecomment-555958546  

That is not a bug. See https://www.boost.org/doc/libs/1_71_0/libs/icl/doc/html/boost_icl/concepts/map_traits.html  
> - A identity absorber never stores value pairs (k,0) that carry identity elements.  
> - A identity enricher stores value pairs (k,0).  
  
  
Maps created with `partial_absorber` (default) or `total_absorber` trait will not store neutral/identity/default values.  
If you want your map to include `0` value, then you should create your map with:  
```  
boost::icl::interval_map<float, int, partial_enricher> m;  
```
