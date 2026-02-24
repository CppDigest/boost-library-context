# #104 - time_duration is not a complete partial order regarding time_duration(not_a_date_time) [Open]

> Username: erikhydro  
> Created at: 2019-05-23 05:59:28 UTC  
> Updated at: 2019-05-24 01:36:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/104  

hi  
  
please compile this program:  
  
#include <boost/date_time/posix_time/ptime.hpp>  
#include <iostream>  
#include <vector>  
  
using namespace boost::posix_time;  
using namespace std;  
  
#define x(v) cout << #v << " : " << (v) << '\n';  
  
int main(int argc, char *argv[])  
{  
  time_duration a(not_a_date_time);  
  time_duration b;  
  x(a<b);  
  x(a>b);    
  x(a<=b);  
  x(a>=b);    
  x(b<a);  
  x(b>a);    
  x(b<=a);  
  x(b>=a);    
  x(a==b);  
  x(b==a);  
  x(a.is_special());  
  x(b.is_special());  
  vector<time_duration> v0, v1;  
  v0.push_back(time_duration());  
  v0.push_back(time_duration(not_a_date_time));  
  sort(v0.begin(), v0.end());  
  v1.push_back(time_duration(not_a_date_time));  
  v1.push_back(time_duration());  
  sort(v1.begin(), v1.end());  
  x(v0==v1);  
  
  map<time_duration, string> c,d;  
  c[time_duration()]=5;  
  c[time_duration(not_a_date_time)]=7;  
  d[time_duration(not_a_date_time)]=7;  
  d[time_duration()]=5;  
  x(c==d);  
  
  return 0;  
}  
  
the output is:  
  
a<b : 0  
a>b : 0    
a<=b : 1  
a>=b : 1    
b<a : 0  
b>a : 0    
b<=a : 1  
b>=a : 1    
a==b : 0  
b==a : 0  
a.is_special() : 1  
b.is_special() : 0  
v0==v1 : 0  
c==d : 0  
  
if the time_duration datatype is a complete partial order which is for  
example needed for it to be able to be sorted, then the above output is  
partly illegal:  
  
if not a<b and not b<a then must be a=b. but this is not the case.  
  
if a<=b and b<=a then must be a=b. but this is not the case.  
  
so as expected the two vectors v0 and v1 are not equal after being  
sorted even though they certainly should be!  
  
also the maps c and d should be equal but aren't.  
  
I hope that this is a bug and not a feature. otherwise it should be documented that even though ptime and gregorian::date are complete partial orders (also regarding their not_a_date_time special value), time_duration isn't. But I really strongly hope that this is a bug.  
  
  
cya  
erik

---

## Comment 1

> Username: uecasm  
> Created at: 2019-05-24 01:36:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/104#issuecomment-495441487  

Related ML thread: https://lists.boost.org/boost-users/2019/05/89805.php  
  
It is odd that `ptime` treats `not_a_date_time` as an ordinary value and `time_duration` does not.  The difference appears to be that one comparison operator goes through `int_adapter` and the other does not.
