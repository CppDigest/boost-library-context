# #181 - Compilation problems with greg_serialize.hpp [Open]

> Username: yunior-peralta-gonzalez  
> Created at: 2021-01-29 13:58:12 UTC  
> Updated at: 2021-02-01 08:06:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/181  

I noticed that the following code doesn't compile:  
```  
#include <sstream>  
#include <iostream>  
  
#include <boost/date_time/gregorian/greg_serialize.hpp>  
#include <boost/archive/text_oarchive.hpp>  
  
int main()  
{  
    boost::gregorian::date d(2021, boost::date_time::Feb, 14);  
  
    std::ostringstream oss;  
    boost::archive::text_oarchive oa(oss);  
    oa & d;  
  
    std::cout << oss.str() << std::endl;  
    return 0;  
}  
```  
it throws the following error:  
```  
boost/date_time/gregorian/greg_serialize.hpp:34:15: error: ‘split_free’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
```  
And if we invert the order of the two boost inclusion headers (putting first `text_oarchive.hpp` and then `greg_serialize.hpp`) then it compiles but doesn't link:  
```  
main.cpp:(.text._ZN5boost13serialization4saveINS_7archive13text_oarchiveEEEvRT_RKNS_9gregorian4dateEj[_ZN5boost13serialization4saveINS_7archive13text_oarchiveEEEvRT_RKNS_9gregorian4dateEj]+0x36): undefined reference to `boost::gregorian::to_iso_string[abi:cxx11](boost::gregorian::date const&)'  
```  
In order to get it linked I added a new header: `#include <boost/date_time/gregorian/gregorian.hpp>`  
  
So it seems that the header `boost/date_time/gregorian/greg_serialize.hpp` has missing dependencies, right? I didn't see any special consideration in the documentation of the library about using this header in a specific order.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-01-29 22:31:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/181#issuecomment-770085069  

So I think this is a similar report as this one https://github.com/boostorg/date_time/issues/176 - which even has a stack overflow question.  
  
Documentation is certainly in order if the code isn't going to be changed -- which was the answer above since we're trying to uncouple the dependencies.  Right now I don't know a better way to fix things.

---

## Comment 2

> Username: yunior-peralta-gonzalez  
> Created at: 2021-02-01 08:06:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/181#issuecomment-770657379  

Thanks @JeffGarland, but I don't think the documentation is in order because I followed the doc and I had several compilation issues, I had to look at the code of the boost library to solve them. Maybe adding some notes here https://www.boost.org/doc/libs/1_75_0/doc/html/date_time/serialization.html would help.  
  
What about having a new library **date_time_serialization** (for _greg_serialize_ and _time_serialize_) that depends on **date_time** and **serialization**? This would be modular, the code wouldn't be duplicated just to avoid dependencies on another library and it wouldn't have the include ordering problem.
