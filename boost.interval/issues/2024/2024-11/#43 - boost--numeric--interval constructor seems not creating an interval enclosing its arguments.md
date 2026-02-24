# #43 - boost::numeric::interval constructor seems not creating an interval enclosing its arguments [Open]

> Username: Shengyis  
> Created at: 2024-11-16 02:58:17 UTC  
> Updated at: 2025-10-18 06:59:43 UTC  
> Url: https://github.com/boostorg/interval/issues/43  

In the document of boost interval [https://www.boost.org/doc/libs/1_86_0/libs/numeric/interval/doc/interval.htm#interval](url)  
If the interval constructor has only one argument, "The constructors create an interval enclosing their arguments."  
But in the following code, seems not working like this, it just sets interval's upper and lower bounds as the same value of my input. This is NOT what we expect for interval arithmetic, since the input may not be precisely stored in a computer.   
```  
#include <iostream>  
#include <boost/numeric/interval.hpp>  
  
using namespace std;  
  
int main() {  
    cout << setprecision(20);  
    boost::numeric::interval<double> I = boost::numeric::interval<double>(0.1);  
    cout << I.upper() << endl; // print: 0.10000000000000000555  
    cout << I.lower() << endl; // print: 0.10000000000000000555  
}  
```  
another strange thing is when I add a double number, sometimes it gives the desired bound, but sometimes NOT! See below, adding 0.1 to the interval and adding 1.1 to the interval gives different type of the result. adding 1.1 gives the desired result.  
```  
#include <iostream>  
#include <boost/numeric/interval.hpp>  
  
using namespace std;  
  
int main() {  
    cout << setprecision(20);  
    boost::numeric::interval<double> I = boost::numeric::interval<double>(0.1);  
    I += 0.1 // add 0.1  
    cout << I.upper() << endl; // print: 0.2000000000000000111  
    cout << I.lower() << endl; // print: 0.2000000000000000111  
}  
```  
```  
#include <iostream>  
#include <boost/numeric/interval.hpp>  
  
using namespace std;  
  
int main() {  
    cout << setprecision(20);  
    boost::numeric::interval<double> I = boost::numeric::interval<double>(0.1);  
    I += 1.1 // add 1.1  
    cout << I.upper() << endl; // print: 1.2000000000000001776  
    cout << I.lower() << endl; // print: 1.1999999999999999556  
}  
```  
When compiling I add -frounding-math, and the boost version is v1.85.0.

---

## Comment 1

> Username: jeking3  
> Created at: 2024-12-05 23:13:30 UTC  
> Url: https://github.com/boostorg/interval/issues/43#issuecomment-2521724702  

Is this an issue for https://github.com/boostorg/interval perhaps?

---

## Comment 2

> Username: Shengyis  
> Created at: 2024-12-06 02:24:56 UTC  
> Url: https://github.com/boostorg/interval/issues/43#issuecomment-2521948257  

> Is this an issue for https://github.com/boostorg/interval perhaps?  
  
yes. Thanks. I did not find this repo at first time.

---

## Comment 3

> Username: mglisse  
> Created at: 2025-10-18 06:59:43 UTC  
> Url: https://github.com/boostorg/interval/issues/43#issuecomment-3417919364  

It seems that all the behavior described here is by design. Assuming that the input is exact is useful in many applications, making `interval(d)` create the interval `[nextafter(d,-inf),nextafter(d,+inf)]` would be a painful pessimization. You could experiment with writing a custom policy so `interval("0.1")` would do what you want, though it doesn't look like policies can change the behavior of `interval(0.1)`. Or you can create an interval `[-denorm_min,+denorm_min]` and add it to every interval you create.  
If the 2 inputs of an addition are intervals of length 0 and the result fits in a double, it is normal for the output to have length 0 as well.
