# #131 - Provide more flexible ways for outputting the tested types [Open]

> Username: akrzemi1  
> Created at: 2018-02-15 13:31:57 UTC  
> Updated at: 2020-06-19 22:50:56 UTC  
> Url: https://github.com/boostorg/test/issues/131  

According to the docs, if I want a custom output from tests for my type, I either have to provide an ADL-visible `operator<<` or ADL-visible function `boost_test_print_type`.  This is not satisfactory in a number of cases.  
  
### 1. Sometimes I operate on pointers or pairs:  
  
```c++  
struct Item  
{  
  int uid;  
  string name;  
  string title;  
};  
  
using ItemPtr = std::shared_ptr<Item>;  
using Count = std::pair<Item, int>;  
```  
  
And I would like to serialize `ItemPtr p` by `p->uid`.  
  
I cannot do this because I would have to add a declaration to namespace `std` which is an undefined behavior.  
  
### 2. Sometimes I want to display the same object in two different ways in two different test cases.  
  
In a different test case I may want to display `ItemPtr p` as  
  
```c++  
 "{"s + p->name + "," + p->title + "}"  
```  
  
Issue 1 could be addressed in two ways.  
  
 1. The library provides a class template `boost::test::print_type<T>::print(T const&, std::ostream&)` which by default goes to `boost_test_print_type` but I can provide a specialization for my types.  
  
 2. You provide a function ADL-based interface, but one of the arguments is from `boost::test` namespace:  
  
```c++  
namespace boost { namespace test {  
  
  struct tag {};  
  
  template <typename T>  
  void print_type(T const&, std::ostream&, tag); // default impl  
  
}}  
```  
  
This way, I can always add overload into namespace `boost::test`:  
  
  
```c++  
namespace boost { namespace test {  
  
  void print_type(std::shared_ptr<int> const&, std::ostream&, tag); // overload  
  
}}  
```  
  
Tags could also solve the second issue, if they were somehow associated with test cases of test case fixtures. Or if rather than tags one would use fixture or test case namesnames:  
  
  
```c++  
namespace boost { namespace test {  
  
  template <typename TT>  
  struct tag {};  
  
  template <typename T, typename TT>  
  void print_type(T const&, std::ostream&, tag<TT>); // default impl  
  
}}  
```  
  
```c++  
namespace boost { namespace test {  
  
  // overloads:  
  void print_type(std::shared_ptr<int> const&, std::ostream&, tag<fixture_1>);   
  void print_type(std::shared_ptr<int> const&, std::ostream&, tag<fixture_2>);   
  
}}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-10-02 00:26:23 UTC  
> Updated at: 2018-10-02 00:26:32 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-426106865  

Can I just modify `boost_test_print_type` to add a tag with a default one? The problem as I understand is that with one argument, the overload needs to be done in the namespace of the type. As soon as `boost_test_print_type` is equipped with a second `boost::test` specific argument, the overload can be done in the `boost::test` namespace.  
  
Is this correct?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2018-10-02 00:27:06 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-426106972  

and most importantly ... thank you for the suggestion!

---

## Comment 3

> Username: akrzemi1  
> Created at: 2018-10-02 08:45:24 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-426196476  

> Is that correct?  
  
Yes. So, currently the interface is:  
  
```c++  
template <typename ArgumentType>  
std::ostream& boost_test_print_type(std::ostream& ostr, ArgumentType const& right);  
```  
  
and you propose to change it to:  
  
```c++  
template <typename ArgumentType, typename TT = default_tag>  
std::ostream& boost_test_print_type(std::ostream& ostr, ArgumentType const& right, TT = {});  
```  
  
Yes, I think it should work.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2018-10-02 09:00:57 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-426201134  

On the second thought though, maybe it will not suffice. In order for this to work, the framework (the macros) will have to call the function always with three arguments:  
  
```c++  
 boost_test_print_type(theStream(), arg, TheFixtureTag());  
```  
  
Overloads currently provided by users, which are two-argument will stop working.  
  
So, you will have to add an another level of customization. Some new ADL-discovered function, which by default forwards to the old (2-argument) `boost_test_print_type` .

---

## Comment 5

> Username: bilbothebaggins  
> Created at: 2020-06-19 20:29:55 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-646849325  

I would like to observe here that the [current docs](https://www.boost.org/doc/libs/1_73_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/testing_tool_output_disable.html) do not clarify whether `operator<<` or `boost_test_print_type` takes precedence - or does one?  
  
Also, I think the most straightforward and somewhat flexible solution would be to leave the rules for `boost_test_print_type` as is, meaning it should be found via the type namespace.  
... And just _add_ an extra function `boost::test::print_type` as proposed in the OP.  
  
I personally think the resolution order should be:  
1) If `boost_test_print_type` is found, use that - after all that has to be found via ADL in the types namespace.  
2) else if `boost::test::print_type` is found, use that  
3) only if no special function is found try to use `operator<<` for the type.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2020-06-19 22:50:40 UTC  
> Updated at: 2020-06-19 22:50:56 UTC  
> Url: https://github.com/boostorg/test/issues/131#issuecomment-646887472  

The [current default](https://github.com/boostorg/test/blob/develop/include/boost/test/tools/detail/print_helper.hpp#L55) implementation of `boost_test_print_type` uses the type's `operator<<`.  
  
I need to give a second thought/shot to `boost::test::print_type`: the API is already quite confusing for users.
