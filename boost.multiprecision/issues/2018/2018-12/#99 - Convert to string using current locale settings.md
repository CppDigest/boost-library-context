# #99 - Convert to string using current locale settings. [Open]

> Username: Shalie  
> Created at: 2018-12-05 16:06:37 UTC  
> Updated at: 2020-01-28 21:34:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99  

I can't find how to convert a cpp_dec_float_50 to a string with the current regional settings (in my case: '.' for digit grouping symbol and ',' for decimal symbol). Looking through the format_float_string function in detail/number_base it seems like it always inserts '.' as a decimal symbol.  
Also posted this to [StackOverflow](https://stackoverflow.com/questions/53496971/converting-boost-multiprecision-to-a-string-with-current-locale-settings).  
  
This is what I get with a regular double:  
```  
double d = 1494.33;  
std::stringstream ss;  
ss.imbue(std::locale(""));  
ss << std::fixed << std::setprecision(6) << d;  
std::cout << ss.str() << std::endl;  
```  
outputs 1.494,330000  
  
Trying the same with dec_float:  
```  
boost::multiprecision::cpp_dec_float_50 df = 1494.33;  
std::stringstream ss;  
ss.imbue(std::locale(""));  
ss << std::fixed << std::setprecision(6) << df;  
std::cout << ss.str() << std::endl;  
```  
outputs 1494.330000  
  
Is there any way to get a regional string out of a boost::multiprecision?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-05 18:40:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-444595447  

I'm afraid the short answer is that we haven't implemented that yet :(

---

## Comment 2

> Username: ram-nad  
> Created at: 2020-01-23 05:00:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577504498  

Sir, i would like to try to add this feature to the the library. Can I just give this a try?

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-01-23 05:21:29 UTC  
> Updated at: 2020-01-23 05:25:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577510130  

> try to add this feature to the the library. Can I just give this a try?  
  
In principle, yes. You could start by looking at the functions that format output strings and see where this kind of change would fit in. Those functions already make detailed use of the stream format flags. But they do not use the locale information.  
  
But two things.  
* Fistly, I am writing this out of memory at the moment. I will take a more detailed look later to see how deep this kind of change would penetrate into the call architecture of the project.  
* Secondly, I fear, however, that this change might appear easier than it is.  
  
If we adhere to the locale settings when printing the results of multiprecision, there will be not only the changes to the running code, but perhaps also to many, possibly numerous, test cases that make use of string output for result verification. There are also library users that have already made significasnt use of the string result information for such things as testing equality and more. There are might even be a few very isolated instances within the library code itself that make use of the string representation. I believe the square root and k'th root might (or might no longer) use a string representation for aiding in getting the first guess of the first Newton iteration.  
  
On the other hand, it might be a good place to start familiarizing yourself with the architecture of the library.  
  
In summary, this is an interesting change request. But it might involve significantly more changes to code and tests than immediately apparent. So I would not deep investigations and large code changes at the moment in this area that might lead to "getting stuck" in this area for too long.  
  
I will look at this and also consider other potential places to start looking at the library in more general terms.  
  
Kind regards, Chris

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-01-23 10:26:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577620186  

+1 to giving this a go.  
  
What I was thinking of as a minimal first step was to isolate all the localisation code inside the iostream operators - the rationale being that there are many back ends (MPFR etc) whose string conversion routines will never be locale-aware.  
  
It's then a case of reformatting according to the numpunct facet in the streams locale, but basically:  
  
* On input, remove grouping separators and replace the decimal point character.  
* On output, replace the decimal point, and then insert grouping punctuation.  
  
Anything else I'm missing?  Do we need wide character iostream support?  How about localised digit characters (but I don't think iostream supports this)?

---

## Comment 5

> Username: ram-nad  
> Created at: 2020-01-23 10:45:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577627040  

Sir, i have started reading the code. I figure that `std::string str(std::streamsize digits = 0, std::ios_base::fmtflags f = std::ios_base::fmtflags(0)) const` function in `number` template is responsible for formatting the string. It just delegates the task to specific .`str()` function of the used backend. Here we will need to change the function to be able to send `std::locale` setting to backend's `.str()` function as well. Thus we will need to modify all the backend. Instead what i was thinking that all the backend's that will support this new parameter could define a static member `locale_string_supported = true` and based upon that we could decide whether to call `str` with `locale` or not.

---

## Comment 6

> Username: ram-nad  
> Created at: 2020-01-23 10:48:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577628047  

I was trying to come up with a template code to achieve this, and i wrote the following code to mimic the situation. Can we implement this in the library?  
```cpp  
#include <iostream>  
#include <type_traits>  
  
template <class T>  
class check_if_defined {  
  template <class R>  
  static typename std::enable_if<  
      std::is_same<const bool, decltype(R::is_locale)>::value, int>::type  
  check(int);  
  
  template <class>  
  static double check(...);  
  
 public:  
  static constexpr bool value = sizeof(decltype(check<T>(0))) == sizeof(int);  
};  
  
template <class T, bool b>  
class detect_base;  
  
template <class T>  
class detect_base<T, true> {  
 public:  
  static constexpr bool value = T::is_locale;  
};  
  
template <class T>  
class detect_base<T, false> {  
 public:  
  static constexpr bool value = false;  
};  
  
template <class T>  
class detect : public std::integral_constant<  
                   bool, detect_base<T, check_if_defined<T>::value>::value> {};  
  
class SupportedBackend {  
 public:  
  static constexpr bool is_locale = true;  
  bool is_supported(bool b) { return b; }  
};  
  
class UnsupportedBackend1 {  
 public:  
  static constexpr bool is_locale = false;  
  bool is_supported() { return false; }  
};  
  
class UnsupportedBackend2 {  
 public:  
  bool is_supported() { return false; }  
};  
  
template <class Backend>  
class Container {  
 public:  
  Backend b;  
  
  bool getValue(std::false_type) { return b.is_supported(); }  
  
  bool getValue(std::true_type) { return b.is_supported(true); }  
  
  bool call() { getValue(detect<Backend>()); }  
};  
  
int main() {  
  Container<SupportedBackend> sb;  
  std::cout << sb.call() << std::endl;  
  
  Container<UnsupportedBackend1> usb1;  
  Container<UnsupportedBackend2> usb2;  
  
  std::cout << usb1.call() << " " << usb2.call() << std::endl;  
}  
```

---

## Comment 7

> Username: ram-nad  
> Created at: 2020-01-23 10:56:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577631029  

> +1 to giving this a go.  
>   
> What I was thinking of as a minimal first step was to isolate all the localisation code inside the iostream operators - the rationale being that there are many back ends (MPFR etc) whose string conversion routines will never be locale-aware.  
>   
> It's then a case of reformatting according to the numpunct facet in the streams locale, but basically:  
>   
> * On input, remove grouping separators and replace the decimal point character.  
> * On output, replace the decimal point, and then insert grouping punctuation.  
>   
> Anything else I'm missing? Do we need wide character iostream support? How about localised digit characters (but I don't think iostream supports this)?  
  
Sir I can try this, I didn't think of this solution previously. I think doing this should be very easy. Do we support `do_grouping()` function as well?

---

## Comment 8

> Username: ckormanyos  
> Created at: 2020-01-23 20:05:05 UTC  
> Updated at: 2020-01-23 20:07:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577852190  

> I was trying to come up with a template code to achieve this, and i wrote the following code to mimic the situation. Can we implement this in the library?  
  
It is time to begin investigating along the lines that John mentioned and in your example code. Nice code.  
  
I got _very_ busy to day in other work. So I think I need to pause until the weekend on issue 99. I also should make sure that my recent PR is finished before starting this.  
  
So Ram, are you familiar with Git, branching and that stuff? I not, you can send PM if you need help on the basics. If so and you have basic Git skill, I encourage you to make a branch with a name that somehow references this issue, which seems to be issue99. I will attempt to catch up with you on this topic and this thread on the weekend.  
  
Thanks for your interest. Kind regards, Chris

---

## Comment 9

> Username: ram-nad  
> Created at: 2020-01-24 01:38:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-577956558  

> So Ram, are you familiar with Git, branching and that stuff? I not, you can send PM if you need help on the basics. If so and you have basic Git skill, I encourage you to make a branch with a name that somehow references this issue, which seems to be issue99. I will attempt to catch up with you on this topic and this thread on the weekend.  
  
Sir, I have implemented changes in iostream operators for making them locale aware in a new branch 'issue99'. Should I open a PR.

---

## Comment 10

> Username: ckormanyos  
> Created at: 2020-01-24 06:36:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-578010114  

> ...implemented changes in iostream operators for making them locale aware in a new branch 'issue99'.  
  
Good.  
  
> Should I open a PR.  
  
I think we should work in the branch a while longer. I would like to take a closer look at your work. i will only have time on the weekend.  
  
We can, and it is usually a good idea to, do significant testing steps prior to issueing a PR.  
  
Kind regards, Chris

---

## Comment 11

> Username: ram-nad  
> Created at: 2020-01-28 05:43:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579089963  

Sir, do I need to add something else in the code?

---

## Comment 12

> Username: ckormanyos  
> Created at: 2020-01-28 20:57:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579452102  

> do I need to add something else in the code?  
  
To be honest, I got so busy that I've not yet had a chance to look into this issue.  
I'll taka a look at it soon.  
  
At a first glance, I'm wondering where the code will come that adds commas and dots to the numerical strings. I think the sample code might be the first step that John had mentioned.  
I think there might be significant effort in this issue regarding string format and figuring out what to do in each locale.  
  
But maybe I have not fully understood the situatin yet.  
  
Kind regards, Chris.

---

## Comment 13

> Username: ram-nad  
> Created at: 2020-01-28 21:00:01 UTC  
> Updated at: 2020-01-28 21:06:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579453226  

Sir i have already added the code as suggested in `ostream& operator<<` and `istream& operator>>` functions in `number.hpp` file.

---

## Comment 14

> Username: ram-nad  
> Created at: 2020-01-28 21:01:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579453969  

I have pushed the code to a new branch `issue99` in my fork.

---

## Comment 15

> Username: dlaugt  
> Created at: 2020-01-28 21:19:23 UTC  
> Updated at: 2020-01-28 21:26:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579461115  

> I have pushed the code to a new branch issue99 in my fork.  
  
For grouping, you should handle values `<= 0` and `CHAR_MAX`. Here is the documentation from the standard:  
  
>string do_grouping() const;  
>  
>Returns: A basic_string vec used as a vector of integer values, in which each element vec[i] represents the number of digits in the group at position i, starting with position 0 as the rightmost group. If vec.size() <= i, the number is the same as group (i-1); if (i<0 || vec[i]<=0 || vec[i]==CHAR_MAX), the size of the digit group is unlimited.  
>The required specializations return the empty string, indicating no grouping.

---

## Comment 16

> Username: ram-nad  
> Created at: 2020-01-28 21:26:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579463988  

Sir, i have handled that case that if empty string is returned then we do unlimited grouping. Does the condition `(i<0 || vec[i]<=0 || vec[i]==CHAR_MAX)` mean that if anytime i come across a `char` that satisfies this conditions then we need to stop inserting separation characters.

---

## Comment 17

> Username: dlaugt  
> Created at: 2020-01-28 21:28:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579464713  

yes

---

## Comment 18

> Username: ram-nad  
> Created at: 2020-01-28 21:34:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/99#issuecomment-579466908  

Sir, I updated the code.
