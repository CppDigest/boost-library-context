# #260 - how to specify the full type for boost::histogram::histogram (instead of "auto") [Closed]

> Username: jvo203  
> Created at: 2019-12-16 02:43:26 UTC  
> Updated at: 2020-03-10 15:49:42 UTC  
> Closed at: 2020-03-10 15:49:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/260  

This is not a bug but a newbie question. All the boost.histogram examples and the source code make a heavy use of "auto". But unfortunately one cannot declare "auto h" in an own class header.  
  
I need to separate the declaration and its corresponding allocation of boost.histogram via make_histogram():  
  
SeedHist2D.hpp:  
class XXX {  
boost::histogram::histogram _hist;  
//putting "auto _hist;" does not work in here, the compiler complains  
}  
  
then in SeedHist2D.cpp:  
_hist = make_histogram(axis::regular<float>(600, x_min, x_max, "_x"),  
                         axis::regular<float>(600, y_min, y_max, "_y"));  
  
Combing through the source code of boost.histogram does not help much, it is full of templates and autos!!! Is there a way to learn the real type hiding behind auto?

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-12-19 12:55:24 UTC  
> Updated at: 2019-12-19 12:57:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/260#issuecomment-567478216  

Hm, I answered this in another issue, but now I cannot find it. This should be documented in the user guide, so please keep this issue open. I recommend to use `decltype`.  
  
```  
namespace bh = boost::histogram;  
  
// in .hpp  
class XXX {  
  using histogram_t = decltype(bh::make_histogram(bh::axis::regular<>(), bh::axis::regular<>()));  
  histogram_t hist_;  
};  
  
// in .cpp  
XXX x;  
x.hist_ = bh::make_histogram(<etc etc>);  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-12-19 13:07:36 UTC  
> Updated at: 2019-12-19 13:11:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/260#issuecomment-567482198  

If your class needs to store histograms with a variable number of axes, you should do:  
```  
// in .hpp  
namespace bh = boost::histogram;  
  
// in .hpp  
class XXX {  
  // put all axis types you are going to use in the variant  
  using axis_t = bh::axis::variant<bh::axis::regular<>, bh::axis::integer<>>;  
  using axes_t = std::vector<axis_t>;  
  using histogram_t = bh::histogram<axes_t>;  
  histogram_t hist_;  
};  
  
// in .cpp  
XXX x;  
x.hist_ = bh::make_histogram(<etc etc>); // as before  
```

---

## Comment 3

> Username: jvo203  
> Created at: 2019-12-20 00:33:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/260#issuecomment-567737599  

Thank you.

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-03-10 15:49:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/260#issuecomment-597161153  

now fixed in develop branch
