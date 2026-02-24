# #201 ASCII output for 1D histograms [Merged]

> Username: przemb  
> Created at: 2019-06-13 06:33:45 UTC  
> Updated at: 2020-02-23 14:06:50 UTC  
> Merged at: 2019-09-28 14:41:01 UTC  
> Closed at: 2019-09-28 14:41:01 UTC  
> Url: https://github.com/boostorg/histogram/pull/201  

Hi,   
I have prepared an initial version of ascii visualization for 1d histograms.  
I still need to add a few things and refine tests, this PR is only to get early-stage feedback.

---

## Review 1 [Changes requested]

> Username: HDembinski  
> Created_at: 2019-06-13 11:29:40 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/histogram/pull/201#pullrequestreview-249266548  

📁 include/boost/histogram/display.hpp

```diff
   1 |+ #ifndef DISPLAY_HPP
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:45:26 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293315986  

Please follow the include guard naming convention, see how other headers do it.

> Username: przemb  
> Created_at: 2019-06-22 10:45:49 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r296441994  

Changed.

---

📁 include/boost/histogram/display.hpp

```diff
   2 |+ #define DISPLAY_HPP
   3 |+ 
   4 |+ #include <boost/format.hpp>
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:46:46 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293316491  

Boost.Format is very nice, but we try to reduce interdependencies between boost libraries. Could you please rewrite the formatting code without boost::format, using just the C++ std lib?

> Username: przemb  
> Created_at: 2019-06-22 10:45:07 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r296441968  

I rewrote this part to _setw()_ and _setprecision()_.  
If you prefer the version with better performance and sprintf() please let me know.

---

📁 include/boost/histogram/display.hpp

```diff
  12 |+ #include <vector>
  13 |+ 
  14 |+ namespace display {
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:47:52 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293316810  

Everything in boost.histogram must be in the boost::histogram namespace. You don't need to set up a new namespace for the display.

---

📁 include/boost/histogram/display.hpp

```diff
  14 |+ namespace display {
  15 |+ namespace {
  16 |+ using namespace boost::histogram;
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:48:54 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293317139  

Instead of using an anonymous namespace, please use the namespace boost::histogram::detail for implementation details.

---

📁 include/boost/histogram/display.hpp

```diff
  24 |+     std::vector<int> values_;
  25 |+     unsigned int size() const {return values_.size();}
  26 |+ };
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:51:25 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293317939  

Is this necessary? I think you can write the code without allocating any storage. This is preferred. You can loop over the histogram several times if necessary. That's fast. Allocating buffers is very expensive.

> Username: przemb  
> Created_at: 2019-08-10 17:47:00 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r312710636  

vectors removed.

---

📁 include/boost/histogram/display.hpp

```diff
  28 |+ struct visualization_data {
  29 |+   std::vector<std::string> str_values_;
  30 |+   std::vector<int> scale_factors_;
```

> Username: HDembinski  
> Created_at: 2019-06-13 10:52:20 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293318234  

try to write the code without allocating memory from the heap.

> Username: przemb  
> Created_at: 2019-08-10 17:47:30 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r312710648  

struct removed

---

📁 include/boost/histogram/display.hpp

```diff
  60 |+     lower = str( boost::format("%.1f") % x.bin().lower() );
  61 |+     upper = str( boost::format("%.1f") % x.bin().upper() );
  62 |+     ex.lower_bounds_.push_back(lower);
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:00:41 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293321031  

where is `str` coming from? you will need to rewrite this. doing one pass over the histogram to figure out all the widths and then a second pass where you actually generate all the text strings.  
  
The main function should not generate a string, because a string has to be allocated. The idiomatic way is to accept a `std::ostream&` and return it. Look how it is done in the `ostream.hpp` headers. The main function should have this signature `template <class Histogram> std::ostream& display(std::ostream& os, const Histogram& h, unsigned terminal_width=0)`. The value `0` indicates that the library should an internal default value. If the user passes a non-zero value, then this should be used as the terminal width. For now, you can use the 60 character limit as the default value. I will implement some terminal width detection code later to change that to something adapted to the current terminal.

---

📁 include/boost/histogram/display.hpp

```diff
  67 |+ }
  68 |+ 
  69 |+ std::string get_single_label(const extract& data,
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:01:31 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293321297  

No `std::string`s should be allocated anywhere.

---

📁 include/boost/histogram/display.hpp

```diff
  88 |+ }
  89 |+ 
  90 |+ std::string get_single_str_value(const std::vector<std::string>& str_values,
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:01:55 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293321406  

see previous comment

---

📁 include/boost/histogram/display.hpp

```diff
  97 |+ }
  98 |+ 
  99 |+ std::vector<int> calculate_scale_factors(const std::vector<int>& values) {
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:02:44 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293321639  

You can use `std::max_element` directly on the histogram.

> Username: przemb  
> Created_at: 2019-08-10 17:47:54 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r312710662  

Thank you, I missed it.

---

📁 include/boost/histogram/display.hpp

```diff
 123 |+   string_values.reserve(values.size());
 124 |+ 
 125 |+   std::transform(values.begin(), values.end(), std::back_inserter(string_values),
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:04:01 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293322069  

Nice to see you using the STL, but again, please no allocations. You can write all this just using std::ostream.

---

📁 include/boost/histogram/display.hpp

```diff
 128 |+ }
 129 |+ 
 130 |+ std::string draw_line(const unsigned int num, const char c = '*', bool complete = true) {
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:05:09 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293322435  

It is good style to define and separate utility functions such as this one. Well done. This is comparably easy to rewrite using a `std::ostream&`.

---

📁 include/boost/histogram/display.hpp

```diff
 169 |+ }
 170 |+ 
 171 |+ visualization_data precalculate_visual_data(extract& h_data){
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:07:15 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293323123  

Here you do a two-pass like I mentioned above. This is the right approach, only that you allocate a lot of intermediate storage for data that you can just query from the histogram.

> Username: przemb  
> Created_at: 2019-08-10 17:48:54 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r312710682  

visualization_data struct removed.

---

📁 include/boost/histogram/display.hpp

```diff
 200 |+ 
 201 |+ template <class histogram>
 202 |+ void display(const histogram& h, std::ostream& output=std::cout) {
```

> Username: HDembinski  
> Created_at: 2019-06-13 11:28:30 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r293330294  

This is not good design. You provide the same functionality as the ostream operator `template <class Histogram> std::ostream& operator<<(std::ostream& os, const Histogram& h)` but you don't use it. The idiomatic way to print things in C++ is the ostream operator and your implementation will replace the crude one that is currently implemented. :)  
  
The only complication here is, that the ostream operator does not allow us to add some options. Therefore we use a little wrapper that allows us to pass an option together with the histogram.  
  
```  
namespace boost {  
namespace histogram {  
namespace detail {  
// the main private display function  
void display_histogram(std::ostream& os, const Histogram& h, unsigned terminal_width) {  
   // ...  
}  
} // ns detail  
  
template <class Histogram>  
struct display_t {  
   const Histogram& histogram;  
   unsigned terminal_width;  
};  
  
template <class Histogram>  
auto display(const Histogram& h, terminal_width=0) {  
   return display_t<Histogram>{h, terminal_width == 0 ? 60 : terminal_width};  
}  
  
template <class Histogram>  
std::ostream& operator<<(std::ostream& os, const display_t<Histogram>& dh) {  
   // call your main display function here  
   detail::display_histogram(os, dh.histogram, dh.terminal_width);  
   return os;  
}  
  
template <class Histogram>  
std::ostream& operator<<(std::ostream& os, const Histogram& h) {  
   os << display(h);  
   return os;  
}  
  
} // ns histogram  
} // ns boost  
```  
  
So the user can either do `std::cout << hist << std::endl` or `std::cout << display(hist, 40) << std::endl`.

> Username: przemb  
> Created_at: 2019-06-19 09:40:55 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r295209922  

@HDembinski You are right, it was a very early version. ;)  
I would like to clarify requirements related to ostream operator.  
At this time, basic ostream operator is defined in _ostream.hpp_, in namespace _boost::histogram_.  
  
You mentioned previously that display.hpp should be "optional".   
Does it mean that after including display.hpp  
`#include <boost/histogram/display.hpp>` _in include/boost/histogram.hpp_  
 **default** ostream operator should be changed to "ascii ostream" which I am defining in _display.hpp_?  
It would mean that existing ostream operator should be overwritten? (each time, only when display.hpp is included?)  
  
Defining two ostream operators in namespace _boost::histogram_ results   
"ambiguous overload error".   
  
Furthermore, It seems to me that BOOST_TEST_NE requires ostream operator.   
After changing default ostream to "ascii version", I would like to force BOOST_TEST_NE to use below form for comparison:   
`histogram(regular(5, -0.5, 2, options=underflow | overflow))`.  
  
What do you think about this approach?

> Username: przemb  
> Created_at: 2019-08-10 18:00:16 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r312710955  

@HDembinski   
edit:  
I visited two "dead ends" and seems that the ambiguity problem is finally solved. ;)   
Now I see that it was a bad idea to enclose existing ostream operator in new namespace (ostream.hpp). I tried this approach, but it requires modification of existing tests, which was painful.   
I noticed also that below form is insufficient (incorrect?):   
```  
template <class Histogram>  
std::ostream& operator<<(std::ostream& os, const Histogram& h) {  
  os << display(h);  
  return os;  
}  
```  
full log: https://pastebin.com/BhajkQzG  
Is it a bug or rather I didn't sepcify required parameters?


---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-06-13 11:46:25 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-501668580  

Thank you for the first draft of the implementation. I wrote you a first review. You need to redo it from scratch, sorry, but unfortunately that's how it works when you want to play in the Boost league. I had to rewrite this library three times from scratch as well until it was in the current state.  
  
One thing you do a lot and which I also used to do in the past is to first collect all the information you need into your own data structures and then work on those. That's nice for you, because you decouple your implementation from the surrounding implementation, but you do it at a high cost, a lot of dynamic memory allocations. Allocating memory from the heap costs 1000s of CPU cycles, meaning you could do 1000s of computations in the meantime for each allocation. Performance is usually not that important for printing to the terminal, but you don't know how the library is going to be used, so you need to prepare for scenarios where people want to avoid dynamic allocations.  
  
Boost.Histogram supports such environments. You can generate histograms completely on the stack. This is a standard that should be maintained.  
  
You can still decouple your implementation from the surrounding code with transforming iterators. A transforming iterator walks over the original data structure but when you dereference it, it turns a transformed result. See https://www.boost.org/doc/libs/1_66_0/libs/iterator/doc/transform_iterator.html to understand what I mean. Unfortunately, you may not use Boost.Iterator, because we want the library to be decoupled, so you have to write your own transforming iterators.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-06-13 11:46:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-501668692  

Once the basic implementation stands, you should also add some unit tests.

---

## Comment 4

> Username: przemb  
> Created_at: 2019-06-16 12:40:25 UTC  
> Updated_at: 2019-06-16 16:17:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-502448608  

Hi,   
thank you for all comments.   
Soon I will rewrite the whole _display()_ function.   
I have just added tests for the existing implementation (based on serialization tests).  
Of course, I will modify them when I introduce missing ostream operator.

---

## Comment 5

> Username: HDembinski  
> Created_at: 2019-08-06 08:58:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-518578626  

Hi, let me know when I should have another look

---

## Comment 6

> Username: przemb  
> Created_at: 2019-08-11 11:24:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-520220346  

Hi,  
I am ready for the second review.

---

## Review 7 [Changes requested]

> Username: HDembinski  
> Created_at: 2019-08-25 16:16:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/histogram/pull/201#pullrequestreview-279336665  

This implementation is much better, but there are a few more improvements possible.

📁 include/boost/histogram/display.hpp

```diff
   1 |+ // Copyright 2019 Hans Dembinski
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:32:54 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317398686  

Please put your own name here :)

---

📁 include/boost/histogram/display.hpp

```diff
  20 |+ namespace detail {
  21 |+ 
  22 |+ unsigned int histogram_width = 60;  // 60 characters
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:35:30 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317398773  

perhaps put these constants into a display_settings struct for clarity.

---

📁 include/boost/histogram/display.hpp

```diff
  43 |+ std::ostream& get_value(std::ostream& out, 
  44 |+                         typename indexed_range<const Histogram>::range_iterator ri,
  45 |+                         const unsigned int& column_width) {
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:40:25 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317398970  

It is not wrong to pass column_width by const reference, but bad style. Everything up to a size of a pointer should be passed by value. The optimizer fixes this, but if the code was executed literally, passing a reference in this would be slower than passing by value in this case.

---

📁 include/boost/histogram/display.hpp

```diff
  47 |+   std::ostringstream tmp;
  48 |+   tmp << std::defaultfloat << *(ri);
  49 |+   out << std::left << std::setw(column_width) << tmp.str();
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:42:04 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399024  

This looks like it can be done without calling the .str() method of ostringstream, which may allocate a temporary string.

---

📁 include/boost/histogram/display.hpp

```diff
 214 |+ } // ns detail
 215 |+ 
 216 |+ template <class Histogram>
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:05:21 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399984  

thinking more about it, i think we should use the setw https://en.cppreference.com/w/cpp/io/manip/setw property of the stream to set the terminal width. setw has a special value 0 which means it is unspecified. In this case we use our terminal width. If the user sets setw directly, we use that. In other words, we make the following code work  
  
```  
std::cout << histogram << std::endl; // OK, terminal width not set, we use our value  
std::cout << std::setw(50) << histogram << std::endl; // OK, terminal width is 50 when histogram is printed  
```

> Username: przemb  
> Created_at: 2019-09-15 15:16:45 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324467945  

@HDembinski   
I implemented version which scales visualization, i.e makes it wider or narrower, based on _std::setw(value)_ .

---

📁 include/boost/histogram/display.hpp

```diff
 221 |+ 
 222 |+ template <class Histogram>
 223 |+ auto display(const Histogram& h, unsigned int terminal_width = 0){
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:07:55 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317400101  

this can be removed, see below

---

📁 include/boost/histogram/display.hpp

```diff
 226 |+ 
 227 |+ template <class Histogram>
 228 |+ std::ostream& operator<<(std::ostream& os, const display_t<Histogram>& dh) {
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:08:29 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317400124  

this can also be removed, see below

---

📁 include/boost/histogram/display.hpp

```diff
 234 |+ std::basic_ostream<CharT, Traits>& operator<<(std::basic_ostream<CharT, Traits>& os,
 235 |+                                               const histogram<A, S>& h) {
 236 |+   os << display(h);
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:17:27 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317400607  

this line changes to  
```  
auto saved_flags = os.flags();  
if (os.width() == 0)  
  os.width(detail::histogram_width);  
os << h;  
os.flags(saved_flags);  
```  
See  
https://stackoverflow.com/questions/2273330/restore-the-state-of-stdcout-after-manipulating-it

---

📁 include/boost/histogram/display.hpp

```diff
 116 |+ template <typename Histogram>
 117 |+ unsigned int get_max_width(const Histogram& h,
 118 |+                            std::function<double(typename indexed_range<const Histogram>::range_iterator)> fcn) {
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:35:51 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317401297  

Please use a templated functor here. std::function is not needed here, because the argument of this function is always known at compile-time.  
```  
template <class Histogram, class Getter>  
unsigned get_max_width(const Histogram& h, const Getter& fcn) { ... }  
```

> Username: HDembinski  
> Created_at: 2019-08-25 15:25:01 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317403278  

Since get_num_of_chargs is very complex and uses knowledge of how string formatting for floats is implemented, I would prefer to simply format the output with a null stream to check how many characters are used see https://stackoverflow.com/questions/11826554/standard-no-op-output-stream and  
https://stackoverflow.com/questions/16825055/c-find-size-of-ofstream

> Username: przemb  
> Created_at: 2019-09-15 10:30:20 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324455934  

@HDembinski   
I like idea with _tellp_ :)   
I am not sure if I understand your suggestion correctly, but I wasn't able to get the number of chars from null_stream.

---

📁 include/boost/histogram/display.hpp

```diff
  25 |+ 
  26 |+ template <class Histogram>
  27 |+ std::ostream& get_lower_bound(std::ostream& os,
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:42:01 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317401548  

it looks a bit weird that define get_lower_bound and get_lower_bound_d. I think there should be only get_lower_bound_d, which should be renamed get_lower_bound, likewise for the the other *_d functions.

> Username: HDembinski  
> Created_at: 2019-08-25 14:44:07 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317401641  

also, get_lower_bound is a bad name for this function, it should be stream_lower_bound or similar.

> Username: przemb  
> Created_at: 2019-09-15 10:17:52 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324455499  

Changed.

---

📁 include/boost/histogram/display.hpp

```diff
  78 |+ 
  79 |+   out << '[' << std::right << std::setw(column_width1);
  80 |+   get_lower_bound<Histogram>(out, ri);
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:43:17 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317401610  

shoud be replaced by a call to get_lower_bound_d and formatting instructions put in here.

---

📁 include/boost/histogram/display.hpp

```diff
  67 |+ 
  68 |+ template <typename Histogram>
  69 |+ std::ostream& get_label(std::ostream& out,
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:43:32 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317401618  

get_label is a bad name, better would be stream_label

---

📁 include/boost/histogram/display.hpp

```diff
  87 |+ }
  88 |+ 
  89 |+ unsigned int get_num_of_chars(double number) {
```

> Username: HDembinski  
> Created_at: 2019-08-25 15:19:53 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317403062  

This seems error prone to get the number of chars. See comment below.

---

📁 include/boost/histogram/display.hpp

```diff
 206 |+   histogram_width = terminal_width;
 207 |+   const auto l_bounds_width = get_max_width(h, get_lower_bound_d<Histogram>);
 208 |+   const auto u_bounds_width = get_max_width(h, get_upper_bound_d<Histogram>);
```

> Username: HDembinski  
> Created_at: 2019-08-25 15:26:22 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317403327  

Isn't this always the same as l_bounds_width?

> Username: przemb  
> Created_at: 2019-09-01 17:02:15 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r319767704  

Not always...   
in each expected [tests](https://github.com/boostorg/histogram/pull/201/files#diff-455050fddd5f6a4ac0224a712f61c3e7R22)    
_l_bounds_width_ = 4 and _u_bounds_width_ = 4,  
but there is one case, when _l_bound_ = 4 and _u_bounds_width_ = 3  
```  
  [-inf, 0.0)  0 |  
  [ 0.0, 0.5)  2 |  
  [ 0.5, 1.0)  0 |  
  [ 1.0, 1.5)  1 |  
  [ 1.5, 2.0)  1 |  
  [ 2.0, inf]  1 |  
```  
to simplify it, I can ignore above case and display it as previously:  
_l_bound_ = 4 and _u_bounds_width_ = 4  
```  
  [-inf,  0.0)  0 |  
  [ 0.0,  0.5)  2 |  
  [ 0.5,  1.0)  0 |  
  [ 1.0,  1.5)  1 |  
  [ 1.5,  2.0)  1 |  
  [ 2.0,  inf]  1 |  
```  
@HDembinski   
which version do you prefer?

> Username: HDembinski  
> Created_at: 2019-09-09 13:41:11 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r322248064  

Ah you are right. I prefer your original version then, where `l_bounds_width` and `u_bounds_width` can be different values. Thanks!


📁 test/Jamfile

```diff
  90 |     [ run storage_adaptor_serialization_test.cpp libserial : $(THIS_PATH) ]
  91 |     [ run unlimited_storage_serialization_test.cpp libserial : $(THIS_PATH) ]
  92 |+     [ run display_serialization_test.cpp libserial : $(THIS_PATH) ]
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:52:59 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399515  

just call it display_test.cpp there is no need to use serialization here. the difference between serialization and what you are doing with display is that serialization is loss-less and allows conversion in both directions. The display you wrote is lossy and works only into one direction.

> Username: przemb  
> Created_at: 2019-09-15 10:50:53 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324456704  

Please check my next comment.


📁 test/display_serialization_test.cpp

```diff
  70 |+   auto h2 = decltype(h1)(); // create a default-constructed second histogram
  71 |+ 
  72 |+   BOOST_TEST_NE(h1, h2);
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:53:57 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399555  

just generate some histograms on the fly in memory, no need to read them from the disk

> Username: przemb  
> Created_at: 2019-09-15 10:16:20 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324455440  

@HDembinski   
I can do it, but I was using serialization especially to provide at least one test with more data. I preferred to avoid defining one big vector of samples and instead I loaded it from xml file.  
Should I then define _h3_ analogous to _h4_?

---

📁 test/display_serialization_test.cpp

```diff
   8 |+ #include <boost/core/lightweight_test.hpp>
   9 |+ #include <boost/histogram/display.hpp>
  10 |+ #include <boost/histogram/serialization.hpp>
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:55:26 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399600  

this test should not use serialization

> Username: przemb  
> Created_at: 2019-09-15 10:50:41 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324456698  

Please check my next comment.

---

📁 test/display_serialization_test.cpp

```diff
   1 |+ // Copyright (c) 2019 Hans Dembinski
```

> Username: HDembinski  
> Created_at: 2019-08-25 13:55:38 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317399606  

put your own name here :)

---

📁 test/display_serialization_test.cpp

```diff
  77 |+   os << h2;
  78 |+   BOOST_TEST_EQ(os.str(), expected);
  79 |+ }
```

> Username: HDembinski  
> Created_at: 2019-08-25 14:06:30 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r317400033  

You should also check the case where the terminal width is set to a lower or higher value.

> Username: przemb  
> Created_at: 2019-09-15 10:04:57 UTC  
> Updated_at: 2019-09-27 16:54:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#discussion_r324455039  

Tests added, I also introduced min_width and max_width.


---

## Comment 8

> Username: HDembinski  
> Created_at: 2019-08-25 16:24:27 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-524643286  

It is difficult to find performance guarantees for std::function, but for boost::function (precursor) they are listed https://www.boost.org/doc/libs/1_71_0/doc/html/function/misc.html

---

## Comment 9

> Username: HDembinski  
> Created_at: 2019-08-25 16:31:07 UTC  
> Updated_at: 2019-08-25 16:31:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-524643746  

Since the printing for simple 1D histograms is close to final, we also need to think about how this change affects the printing for histograms which are not 1D. If the new display cannot handle the histogram, it should fall back to the old-style streaming.

---

## Review 10 [Approved]

> Username: HDembinski  
> Created_at: 2019-09-26 23:45:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/histogram/pull/201#pullrequestreview-294057468  

Looks great!

---

## Comment 11

> Username: HDembinski  
> Created_at: 2019-09-28 14:40:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-536195350  

Excellent and thank you very much for your contribution :) I will work on extending this for N dimensions in another branch.

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-02-23 14:06:12 UTC  
> Updated_at: 2020-02-23 14:06:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/201#issuecomment-590072252  

Awesome work! Cross-referencing the related issue #74

---
