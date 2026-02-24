# #98 actor: Remove copy assignment operator [Merged]

> Username: Kojoley  
> Created at: 2020-06-08 12:22:03 UTC  
> Updated at: 2020-06-25 22:46:09 UTC  
> Merged at: 2020-06-14 22:57:18 UTC  
> Closed at: 2020-06-14 22:57:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/98  

In #64 when I made actor utilize Proto generated assignment operators, I missed that Phoenix copy assignment operator was not producing a lazy expression, instead it simply copied the state over. I do not know why that was done in the first place, the previous behavior is not logical to me, and the current one is more justified, however after thinking thoroughly there is not a lot of use cases for it, the only I had come with is:  
  
```cpp  
#include <boost/phoenix.hpp>  
#include <iostream>  
  
int main()  
{  
  using namespace boost::phoenix;  
  
  int i = 0, j = 123;  
  auto ri = ref(i);  
  (ri = ref(j))();  
  std::cout << "i=" << i << ", j=" << j << "\n";  
}  
```  
  
Since the behavior was broken 6 releases (2.5 years) ago, what is enough time to some one to spot the change and report a regression, but we have not received any, and the new behavior has a little value, I think it is better to simply remove the copy assignment operator, because it will allow us to solve naturally the issue with the `-Wdeprecated-copy` warning (otherwise we need either to define copy constructor and it will break code that relies on `actor` being an aggregate, or suppress the warning what currently impossible on GCC).  
  
Closes #83  
Closes #97

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-06-14 20:54:53 UTC  
> Url: https://github.com/boostorg/phoenix/pull/98#issuecomment-643820932  

@djowel could you take a look, please? Beta is in less than three weeks, so the merge window is tight on this.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-06-25 14:59:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/98#issuecomment-649603190  

Please, merge to master branch

---

## Comment 3

> Username: djowel  
> Created_at: 2020-06-25 22:46:09 UTC  
> Url: https://github.com/boostorg/phoenix/pull/98#issuecomment-649854814  

> Please, merge to master branch  
  
Done. Many thanks!

---
