# #139 - Does this library has an analogue of `ranges::views::intersperse`? [Open]

> Username: denzor200  
> Created at: 2022-10-24 22:06:50 UTC  
> Updated at: 2022-11-14 18:46:02 UTC  
> Url: https://github.com/boostorg/range/issues/139  

Can i write some code like this:  
```  
#include <range/v3/all.hpp>  
#include <vector>  
#include <iterator>  
#include <algorithm>  
#include <iostream>  
int main()  
{  
        auto op = [](auto & input, int i, auto & ins)  
        {  
            return input | ranges::views::intersperse(ins)  
                         | ranges::to<std::string>();  
        };  
        std::string input{"foobarbaxbat"};  
        char insert{','};  
        auto rng = op(input, 1, insert);  
        std::cout << rng << '\n';      // Outputs: f,o,o,b,a,r,b,a,x,b,a,t  
}  
```  
without range-v3 library, using Boost.Range instead of range-v3?

---

## Comment 1

> Username: denzor200  
> Created at: 2022-11-14 18:46:01 UTC  
> Url: https://github.com/boostorg/range/issues/139#issuecomment-1314217662  

I tried to do it by myself, like this:  
https://godbolt.org/z/xb3vMezdb  
If there is interest in this, I can move forward and make a PR with tests and doc.
