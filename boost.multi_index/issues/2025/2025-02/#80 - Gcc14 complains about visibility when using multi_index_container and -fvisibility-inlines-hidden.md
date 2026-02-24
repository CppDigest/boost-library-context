# #80 - Gcc14 complains about visibility when using multi_index_container and -fvisibility-inlines-hidden [Closed]

> Username: PhamLeHai  
> Created at: 2025-02-05 07:57:26 UTC  
> Updated at: 2025-02-06 16:39:00 UTC  
> Closed at: 2025-02-05 19:57:52 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80  

Hello,   
  
### Description  
  
When using multi_index_container, compiling with `-fvisibility-inlines-hidden` flag, Gcc14 complains that the visibility of the user class (`Test` in the example) is greater than its member, which is the container itself.  
  
What is the reason for this error? And what is the solution suggested?  
  
### Example  
  
```c++  
#include <string>  
  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/mem_fun.hpp>  
  
class Item {  
  public:  
    const std::string_view name() const {  
        return "";  
    }  
};  
  
namespace bmi = boost::multi_index;  
  
struct item_tag;  
  
using Container = bmi::multi_index_container<  
    Item,  
    bmi::indexed_by<  
        bmi::hashed_unique<  
            bmi::tag<item_tag>,  
            bmi::const_mem_fun<Item, const std::string_view, &Item::name>>  
        >  
    >;  
  
class Test {  
  private:  
    Container items_;  
};  
```  
  
View online on [Compiler Explorer](https://godbolt.org/z/14MW6noPb)

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-02-05 19:57:52 UTC  
> Updated at: 2025-02-05 19:58:41 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80#issuecomment-2637901530  

This warning is due to the fact that you're using a pointer to a member function (`&Item::name`), whose visibility is hidden by `-fvisibility-inlines-hidden`, in a context with default visibility. The easiest fix is to make `Item::name` visible (https://godbolt.org/z/6GEaaMhr4):  
```cpp  
#include <string>  
  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/mem_fun.hpp>  
  
class Item {  
  public:  
    __attribute__ ((visibility ("default"))) const std::string_view name() const {  
        return "";  
    }  
};  
  
namespace bmi = boost::multi_index;  
  
struct item_tag;  
  
using Container = bmi::multi_index_container<  
    Item,  
    bmi::indexed_by<  
        bmi::hashed_unique<  
            bmi::tag<item_tag>,  
            bmi::const_mem_fun<Item, const std::string_view, &Item::name>>  
        >  
    >;  
  
class Test {  
  private:  
    Container items_;  
};  
  
int main() {  
  
}  
```  
Another option is to avoid using `Item::name` altogether by replacing`const_mem_fun` with a user-defined key extractor (https://godbolt.org/z/d79Kd8vEG):  
```cpp  
 #include <string>  
  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/mem_fun.hpp>  
  
class Item {  
  public:  
    const std::string_view name() const {  
        return "";  
    }  
};  
  
struct ItemNameExtractor  
{  
  using result_type = const std::string_view;  
  result_type operator()(const Item& x) const { return x.name(); }  
};  
  
namespace bmi = boost::multi_index;  
  
struct item_tag;  
  
using Container = bmi::multi_index_container<  
    Item,  
    bmi::indexed_by<  
        bmi::hashed_unique<  
            bmi::tag<item_tag>,  
            ItemNameExtractor>  
            //bmi::const_mem_fun<Item, const std::string_view, &Item::name>>  
        >  
    >;  
  
class Test {  
  private:  
    Container items_;  
};  
  
int main() {  
  
}  
```

---

## Comment 2

> Username: PhamLeHai  
> Created at: 2025-02-06 10:31:06 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80#issuecomment-2639430905  

Excellence!!  
I did not expect the reason to be our own `name()` function! Thanks a lot for the help.   
  
One extra question for my learning: Can you share the process to figure this out? When I looked at the error messages from GCC, they did not mention anything about that function but only pointed to the class `multi_index_container` (Or other components from `boost` itself).

---

## Comment 3

> Username: joaquintides  
> Created at: 2025-02-06 10:37:03 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80#issuecomment-2639442013  

I just kept trying to narrow the problem down to one of the constituent parts of the definition of `Container` till it dawned on me it was `const_mem_fun` and its use of pointers to member function that was causing the problem. After this, it's kind of obvious why the compiler is warning (`Item::name` is inlined and hence not visible by virtue of `-fvisibility-inlines-hidden`).

---

## Comment 4

> Username: PhamLeHai  
> Created at: 2025-02-06 10:42:25 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80#issuecomment-2639454708  

How did you infer and narrow the problem for each component?   
  
When I tried to resolve the error, I tried to put  `__attribute__ ((visibility ("default")))` around anything reported on Gcc's messages and then tried to compile again.   
  
I don't know if we have any better way, since in this case, their error messages were confusing and vague.

---

## Comment 5

> Username: joaquintides  
> Created at: 2025-02-06 16:38:59 UTC  
> Url: https://github.com/boostorg/multi_index/issues/80#issuecomment-2640366545  

> How did you infer and narrow the problem for each component?  
  
Breaking the definition of `Container` in its different subtypes.  
  
> I don't know if we have any better way, since in this case, their error messages were confusing and vague.  
  
Yes, the compiler coud have easily pointed to the problematic inline function.
