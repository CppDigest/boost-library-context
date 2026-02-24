# #13 - Add reflection on attributes [Open]

> Username: sdebionne  
> Created at: 2021-05-06 09:22:26 UTC  
> Updated at: 2022-07-08 09:02:26 UTC  
> Url: https://github.com/boostorg/describe/issues/13  

Following up discussion on [cpplang](https://cpplang.slack.com/archives/C27KZLB0X/p1620262809119900), it would be interesting to have a way to extend *member descriptors* with custom information (think units, text description). User-defined [attributes](https://en.cppreference.com/w/cpp/language/attributes) seems the right tool to express this custom information:  
  
```c++  
[[units(cm), description("this is sparta")]] int x;  
```  
  
The emulation of the reflection would look something like:  
  
```c++  
BOOST_DESCRIBE_MEMATTR(x, units(cm), description("this is sparta")) int x;  
```  
  
We would have `describe_member_attributes<D>` returns `L<A1, A2, …​, An>`, where `D` is a member descriptor.  
  
and `Ai` is an *attribute descriptor* of the form  
  
```c++  
struct Ai  
{  
        static constexpr char const * name = "units";  
        static constexpr char const * value = "cm";   /* string attribute only for now */  
};  
```  
  
As pointed out by @pdimov, there is a proposal about attribute reflection that introduces [typed attributes](  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1887r1.pdf).

---

## Comment 1

> Username: pdimov  
> Created at: 2021-05-11 14:12:20 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-838546536  

There seems to be a significant pushback in the committee against the idea of reflection on attributes. The objection is that attributes today can be silently ignored by the compiler, and this feature is deemed extremely important for some reason. The suggestion of those who object is that these annotations need to use a keyword, rather than the attribute syntax.

---

## Comment 2

> Username: sdebionne  
> Created at: 2021-05-25 11:22:13 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-847785471  

I dont understand why having attributes (today) silently ignored by the compiler make them unsuitable. Why not make them eventually silently ignored but mandatorily reflected? Why another keyword? That's questions for the committee...  
In the meantime, external annotations and #15 will do the trick.

---

## Comment 3

> Username: denzor200  
> Created at: 2021-09-11 12:10:04 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-917396692  

Hello everyone! I recently discovered an interesting way to describe custom attributes in C++17. I propose to consider it.  
```  
#ifdef _STR  
#  error _STR already defined  
#endif  
#define _STR(S) BOOST_METAPARSE_STRING(S){}  
  
boost::describe::field<int, (units=cm), (description=_STR("this is sparta"))> x;  
```  
  
This short example shows that it is possible to implement it. At least - for one attribute :-)  
https://godbolt.org/z/ad1ojzch7

---

## Comment 4

> Username: sdebionne  
> Created at: 2021-09-23 13:00:20 UTC  
> Updated at: 2021-09-24 07:25:30 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-925794063  

I would prefer a solution that does not require to use a wrapper type (eg `field`), something less intrusive. Here is what I came up with, loosely inspired from the Describe library.   
  
The annotations (members do not need to have the same type/number of annotations):  
  
```c++  
struct position  
{  
    int distance;  
    float timestamp;  
};  
BOOST_DESCRIBE_STRUCT(position, (), (distance, timestamp))  
  
BOOST_ANNOTATE_MEMBER(position, distance,  
    (unit, "meter"),  
    (doc, "The distance travelled from the start line"))  
  
BOOST_ANNOTATE_MEMBER(position, timestamp,  
    (unit, "second"),  
    (doc, "The EPOCH timstamp since the beginning of the run"))  
```  
  
And the usage:  
  
```  
// For each member descriptors  
using Md = boost::describe::describe_members<T, boost::describe::mod_any_access>  
boost::mp11::mp_for_each<Md>([&](auto D) {  
    // Get annotation for the member descriptor  
    using A = boost::describe::annotate_member<T, decltype(D)>;  
  
    std::cout << "\nAnnotations:\n";  
    boost::mp11::mp_for_each<A>([&](auto a) {  
        std::cout << "." << a.name << " = " << a.value << std::endl;  
    });  
});  
```  
  
https://godbolt.org/z/7PbobnMxW  
  
@pdimov Do you think something like this is worth adding to the library?

---

## Comment 5

> Username: denzor200  
> Created at: 2022-07-08 04:10:50 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-1178525904  

@sdebionne Is there really a need for a separate `BOOST_ANNOTATE_MEMBER`? Wouldn't it be better to package it all in one `BOOST_DESCRIBE_STRUCT`? Like this:  
```  
  
struct position  
{  
    int distance;  
    float timestamp;  
};  
BOOST_DESCRIBE_STRUCT(position, (), (  
    (distance,  
        (unit, "meter"),  
        (doc, "The distance travelled from the start line")  
    ),  
    (timestamp,  
        (unit, "second"),  
        (doc, "The EPOCH timstamp since the beginning of the run")  
    )  
))  
  
```

---

## Comment 6

> Username: sdebionne  
> Created at: 2022-07-08 07:26:47 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-1178650295  

The rationale was mostly to keep annotation as an extension of Boost.Describe. But I like your suggestion too.  
  
I have a project that uses Annotation to generate a Json Schema from a described and annotated data structure. Here is the latest iteration of the annotation bits:  
  
[annotations.hpp.txt](https://github.com/boostorg/describe/files/9069807/annotations.hpp.txt)  
  
If there is interest in this, I can move forward and make a PR with tests and doc...

---

## Comment 7

> Username: denzor200  
> Created at: 2022-07-08 08:31:28 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-1178709605  

If i understand you right, your solution can be used like this:  
```  
BOOST_ANNOTATE_MEMBER(position, distance,  
    (unit, "meter"))  
BOOST_ANNOTATE_MEMBER(position, distance,  
    (doc, "The distance travelled from the start line"))  
  
// member position::distance has two attributes - unit and doc  
```  
This is perfect together with my suggestion and don't contradict it.

---

## Comment 8

> Username: denzor200  
> Created at: 2022-07-08 08:34:17 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-1178712244  

> If there is interest in this, I can move forward and make a PR with tests and doc...  
  
It would be interesting for me, but i not a reviewer here:)

---

## Comment 9

> Username: sdebionne  
> Created at: 2022-07-08 09:02:26 UTC  
> Url: https://github.com/boostorg/describe/issues/13#issuecomment-1178739345  

Side note, the member annotations can also be functions:  
  
```c++  
struct acquisition  
{  
    int nb_frames = 1;    
   ...  
}  
  
BOOST_ANNOTATE_MEMBER(acquisition, nb_frames,  
    (desc, "number of frames"),  
    (doc, "The number of frames to acquire (0 = continuous acquisition)"),  
    (validate, [](int nb_frames) { return nb_frames >= 0;}))  
```  
  
Optional annotation can be handled pretty easily. Here we have a `validate` function annotation and a generic validate free function would use the annotation if available:  
  
```c++  
using Validate = BOOST_DESCRIBE_MAKE_NAME(validate);  
  
template <typename L, typename T,  
          std::enable_if_t<boost::describe::has_annotation_by_name<L, Validate>::value, bool> = true>  
bool validate(T const& t)  
{  
    // With validation  
    auto validate = boost::describe::annotation_by_name_v<L, Validate>;  
    return validate(t);  
}  
  
template <typename L, typename T,  
          std::enable_if_t<!boost::describe::has_annotation_by_name<L, Validate>::value, bool> = false>  
bool validate(T const& t)  
{  
    // Without validation  
    return true;  
}  
```
