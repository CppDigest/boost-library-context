# #238 - [Feature request] Automatic aggregate serialization [Open]

> Username: denzor200  
> Created at: 2021-08-02 23:30:32 UTC  
> Updated at: 2021-08-08 02:33:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/238  

Hi. I tried to create simple method `serialize_aggregate` using boost.pfr library (new reflection library from Antony Polukhin)  
```  
template<class Archive, class Aggregate>  
void serialize_aggregate(Archive& ar, Aggregate& ag)  
{  
    boost::pfr::for_each_field(ag, [&ar](auto&& field) {  
        ar & field;  
    });  
}  
```  
  
This method allows me to describe serializable entities more compact:  
```  
struct gps_position  
{  
    int degrees;  
    int minutes;  
    float seconds;  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int /* file_version */) {  
        serialize_aggregate(ar, *this);  
    }  
};  
std::ostream& operator<<(std::ostream& os, const gps_position& gp)  
{  
    return os << ' ' << gp.degrees << (unsigned char)186 << gp.minutes << '\'' << gp.seconds << '"';  
}  
```  
Why not support for this method on the library side?

---

## Comment 1

> Username: denzor200  
> Created at: 2021-08-02 23:31:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-891400671  

unfortunately, i have no idea how can a handle `version` arg with this new style((

---

## Comment 2

> Username: correaa  
> Created at: 2021-08-02 23:48:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-891405889  

Great idea. One problem is that serialization for certain Archives in Boost.Serialization (e.g. XML) requires an extra level of instrospection to get the field name or, more simply, at least have a way to generate names automatically if not provided  
(Automatic tag generation in my opinion is something that the library should provide by default.)  
  
Second, I wouldn't bother with versioning aggregates. My perspective is that aggregates shouldn't be subject to versioning because in that case you have to start defaulting values which is contrary to the idea of an aggregate. (That is the price of using aggregates).

---

## Comment 3

> Username: denzor200  
> Created at: 2021-08-03 08:26:18 UTC  
> Updated at: 2021-08-03 08:26:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-891645083  

> One problem is that serialization for certain Archives in Boost.Serialization (e.g. XML) requires an extra level of instrospection to get the field name or, more simply, at least have a way to generate names automatically if not provided  
(Automatic tag generation in my opinion is something that the library should provide by default.)  
  
```  
struct gps_position  
{  
    field<int, name="degrees"> degrees;  
    field<int, name="minutes"> minutes;  
    field<float, name="seconds"> seconds;  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int /* file_version */) {  
        serialize_named_aggregate(ar, *this); ///< TODO: implement serialize_named_aggregate  
    }  
};  
std::ostream& operator<<(std::ostream& os, const gps_position& gp)  
{  
    return os << ' ' << gp.degrees << (unsigned char)186 << gp.minutes << '\'' << gp.seconds << '"';  
}  
```  
  
We need to:  
1. `field<T, A>` template class, this class is just a wrapper to `T` with `std::reference_wrapper`-like(??) iface.  
2. `name` object with overloaded `operator=(std::string_view)`, and this overloaded operator must be constexpr

---

## Comment 4

> Username: robertramey  
> Created at: 2021-08-03 17:24:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-892025944  

After taking a short look at pfr documentation, Wouldn't something like this be as simple as possible?   
  
`template<class Archive, class Aggregate>`  
  
`void boost::serialization::serialize(Archive& ar, Aggregate& ag){`  
`    boost::pfr::for_each_field(ag, [&ar](auto& field) {`  
`        ar & field;`  
`    }`  
');`  
  
  
PS - how did you get the well formatted code into the GitHub comment?

---

## Comment 5

> Username: denzor200  
> Created at: 2021-08-04 09:04:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-892493282  

> Wouldn't something like this be as simple as possible?  
  
Yes, rvalue reference in my example is redundant. Lvalue enough.  
  
> how did you get the well formatted code into the GitHub comment?  
1. Paste your code  
2. Select your pasted code  
3. Press "Insert code <ctrl+e>"  
4. Look at the well formatted code in the "Preview" section

---

## Comment 6

> Username: denzor200  
> Created at: 2021-08-06 21:21:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-894524792  

Finally, i successfully implemented `field<T, A>` and `name` for C++17. Interface for `field<T,A>` is not ideal, but this short example shows that structure with named fields are possible:  
http://godbolt.org/z/qz3TPo8Pb

---

## Comment 7

> Username: denzor200  
> Created at: 2021-08-06 21:22:40 UTC  
> Updated at: 2021-08-06 21:24:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-894525255  

```  
template<class Archive, class Aggregate>  
void serialize_named_aggregate(Archive& ar, Aggregate& ag)  
{  
    boost::pfr::for_each_field(ag, [&ar](auto& field) {  
        using TField = std::decay_t<decltype(field)>;  
        // The field_t<T,A>::name() method guarantees us that the returned std::string_view always refers to a zero-terminated string.  
        ar& boost::serialization::make_nvp(TField::name().data(), field.data());  
    });  
}  
```

---

## Comment 8

> Username: denzor200  
> Created at: 2021-08-06 21:24:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-894525921  

```  
#ifdef _STR  
#  error _STR already defined  
#endif  
#define _STR(S) BOOST_METAPARSE_STRING(S){}  
  
struct gps_position  
{  
    field<int, (name = _STR("degrees"))> degrees;  
    field<int, (name = _STR("minutes"))> minutes;  
    field<float, (name = _STR("seconds"))> seconds;  
  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int /* file_version */) {  
        serialize_named_aggregate(ar, *this);  
    }  
};  
  
// TODO: use BOOST_PFR_FUNCTIONS_FOR  
std::ostream& operator<<(std::ostream& os, const gps_position& gp)  
{  
    return os << ' ' << gp.degrees.data() << (unsigned char)186 << gp.minutes.data() << '\'' << gp.seconds.data() << '"';  
}  
```

---

## Comment 9

> Username: robertramey  
> Created at: 2021-08-07 21:22:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-894708211  

Looking at this a little more, I've concluded that it's kind of tricky to do right.  In any case, I think it would be a much better addition to the PFR library than the serialization library.

---

## Comment 10

> Username: correaa  
> Created at: 2021-08-08 02:26:35 UTC  
> Updated at: 2021-08-08 02:33:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/238#issuecomment-894732897  

> Looking at this a little more, I've concluded that it's kind of tricky to do right. In any case, I think it would be a much better addition to the PFR library than the serialization library.  
  
What exactly do you think it is hard to get right? i) Serializing aggregates in general or ii) generating the named-value pairs for specific archives that need them.  
  
I think that serializing aggregates is well defined an this approach should work, although will make Boost Serialization depend on Boost PFR (which is c++14 only I think, maybe in a couple of versions of the languages we will not even depend PFR to do this.)  
  
I think the problem of naming fields is more serious.  
In any case, using PFR or not (in some way), a key enabling feature in Boost Serialization would be to relax the requirement that XML archives (and therefore generic archives in general) use named-value pairs and that the tag names are generated automatically when needed.  
  
I brought up this issue a couple of times before.  
The need for named-value pairs right now generates an spurious and early dependency on Boost (or Boost Serialization) even before one decides to use Boost Serialization at all with a class).  
Note that one could write serialization code generically without including any Boost library.  
However when one needs to make this generic code work (in a possible future) with XML-like archives, one needs to depend on including a Boost header (nvp).  
  
There are two solutions to this problem 1) make named value pairs optional (tags with names are nice to have but not a requirement). 2) make XML-like archives take a generic named value pair-like class, rather than an specific hard coded type.   
Solution 1) would make this class `field` unnecessary and can work with any version of C++.  
Solution 2) would need some sfinae-magic that is very hard to get right in C++98 but trivial in C++11 as we discussed earlier. 1) and 2) are not exclusive.
