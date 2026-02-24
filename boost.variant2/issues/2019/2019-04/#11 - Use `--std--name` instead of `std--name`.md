# #11 - Use `::std::name` instead of `std::name` [Closed]

> Username: akrzemi1  
> Created at: 2019-04-08 20:37:31 UTC  
> Updated at: 2021-09-11 15:12:59 UTC  
> Closed at: 2021-09-11 15:12:59 UTC  
> Url: https://github.com/boostorg/variant2/issues/11  

The implementation of `variant` as well as `mp11` uses construct `std::name` to refer to entities from the Standard Library. This makes the following code fail to compile:  
  
```c++  
// included from <scheduled_times_tibrary.hpp>  
namespace scheduled_times_tibrary {   
  namespace std { // Scheduled Time of Departure       
  }  
}  
  
// my program:  
using namespace ScheduledTimesLibrary;  
  
#include <boost/variant/variant.hpp>  
  
int main() {}  
```  
  
Even if this situation is unlikely, it is easily avoided by referring to the `std` in the global namespace with `::std::name`.
