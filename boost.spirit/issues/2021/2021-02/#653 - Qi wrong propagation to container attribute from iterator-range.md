# #653 - Qi wrong propagation to container attribute from iterator-range [Closed]

> Username: sehe  
> Created at: 2021-02-27 00:35:36 UTC  
> Updated at: 2021-02-27 12:37:59 UTC  
> Closed at: 2021-02-27 12:37:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/653  

Adding an empty sequence element (like `>> ""` or `>> eps`, or even `>> omit[whatever]`) makes the attribute propagation for `raw[]` fail.  
  
The second assert fails, because only the first element of the iterator range is copied:  
  
```c++  
#include <boost/spirit/include/qi.hpp>  
int main() {  
    using namespace boost::spirit::qi;  
    auto *const f = "123",  
         *const l = f + 3;  
    std::string a, expected { '1', '2', '3' }; // or e.g. vector<char>  
  
    assert(parse(f, l, raw[int_], a));  
    assert(a == expected);                     // pass  
  
    a.clear();  
  
    assert(parse(f, l, raw[int_] >> eps, a));  
    assert(a == expected);                     // <- FAIL!  
}  
```  
  
(the actual value of `a` is `"1", instead of "123" in the failing assert).  
  
I chased the code down a little bit. In the correct case we hit this line in assign_to:  
  
![image](https://user-images.githubusercontent.com/324097/109369038-4fdbc900-789b-11eb-84b5-5c1fbd3a3c2c.png)  
  
In the incorrect case we end up here:  
  
![image](https://user-images.githubusercontent.com/324097/109369083-6e41c480-789b-11eb-8a9e-0c663bd2388d.png)  
  
 - My intuitive read is that this is like the inverse of "single-attribute fusion sequence" problem: "synthesized elements from a  sequence parser add single values to container attribute".  
  
 - Clearly, if all other limbs of a sequence are attribute-less, there should not be a change in behaviour.  
 - In that sense this resembles analogous issues in X3 (e.g. $610 and potentially others)  
  
------  
  
Informative note: this behaviour has not changed since 1.54.0 and does not depend on compilers as far as I could see.  
  
This sample reduced from [this SO question](https://stackoverflow.com/questions/66379272/how-to-get-the-substring-matched-by-a-boost-spirit-numeric-parser/66388463#66388463)

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-02-27 02:13:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/653#issuecomment-786986476  

Does not work in X3 either, seems to be simple to fix in Qi though.
