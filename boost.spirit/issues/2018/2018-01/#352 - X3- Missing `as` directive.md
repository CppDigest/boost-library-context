# #352 - X3: Missing `as` directive [Closed]

> Username: akimd  
> Created at: 2018-01-16 05:27:23 UTC  
> Updated at: 2018-12-09 17:04:49 UTC  
> Closed at: 2018-01-16 13:24:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/352  

In my grammar there are keywords whose semantic value is their string.  I need the opposite of `omit`, and if I understand correctly [the documentation](https://ciere.com/cppnow15/x3_docs/spirit/quick_reference/directive.html), I'm looking for `as_string`, or `as_string`.  Neither exist, there's just [a commented include](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/directive.hpp#L10) .  
  
Thanks!

---

## Comment 1

> Username: akimd  
> Created at: 2018-01-16 07:48:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-357878816  

I finally understood that `x3::string` does what I need.  I suppose `as_string` should be removed from the documentation?

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-01-16 13:24:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-357958089  

It is already. See #244.

---

## Comment 3

> Username: akimd  
> Created at: 2018-01-17 05:58:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-358205853  

Doesn't this show that `as` would be a useful addition?  
https://stackoverflow.com/a/33817135/1353549

---

## Comment 4

> Username: djowel  
> Created at: 2018-01-17 06:18:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-358208612  

I'm OK either way. I'll leave it up to @Kojoley to decide if it's worth having.

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-02-18 00:42:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-366483100  

The example actually shows need not in `as` parser, but in a simple collapse protection (less verbose, no need to specify a type).  
  
I am 50/50 for having `as`.  If you open a PR adding it I will merge it.

---

## Comment 6

> Username: Kojoley  
> Created at: 2018-02-25 19:46:26 UTC  
> Updated at: 2018-02-25 23:12:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-368338439  

> The example actually shows need not in `as` parser, but in a simple collapse protection (less verbose, no need to specify a type).  
  
I was talking about this:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/vector.hpp>  
#include <boost/fusion/include/io.hpp>  
#include <iostream>  
  
namespace boost { namespace spirit { namespace x3  
{  
    template <typename Subject>  
    struct protect_directive : Subject  
    {  
        typedef Subject base_type;  
  
        protect_directive(Subject const& subject)  
          : base_type(subject) {}  
    };  
  
    struct protect_gen  
    {  
        template <typename Subject>  
        protect_directive<typename extension::as_parser<Subject>::value_type>  
        operator[](Subject const& subject) const  
        {  
            return { as_parser(subject) };  
        }  
    };  
  
    auto const protect = protect_gen{};  
}}}  
  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
    template <typename Subject, typename Context>  
    struct attribute_of<x3::protect_directive<Subject>, Context>  
        : attribute_of<Subject, Context> {};  
}}}}  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
    const std::string s = "abcd";  
    std::string::const_iterator it = s.begin();  
    boost::fusion::vector<  
        boost::fusion::vector<char, char>  
      , boost::fusion::vector<char, char>  
    > r; // this emulates some complex structure adapted by fusion  
    if (x3::phrase_parse(it, s.end(),  
              x3::protect[x3::char_ >> x3::char_] >> x3::protect[x3::char_ >> x3::char_]  
            , x3::space, r)) {  
        std::cout << s << ": " << r << std::endl;  
        return 0;  
    }  
  
    std::cout << "failed" << std::endl;  
    return 1;  
}  
```  
  
The SO example will look like this `int_ >> ':' >> protect[int_ >> *(',' >> int_)]`.  
  
@djowel what you think about such directive?  
  
Possible names for it:  
  - unit  
  - protect  
  - guard

---

## Comment 7

> Username: djowel  
> Created at: 2018-02-25 22:25:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-368350936  

I like it! I think "protect" best reflects what it does, but "unit" sounds good too. Yes, I need that directive every now and then. What I do when I need it is place the 'protected' stuff in a rule, which inherently 'protects' collapse.  Will you also write the doc for it?  
  
Also, I'm convinced now that we should add "as". Perhaps with "as" added, we don't need "protect"?

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-02-25 23:00:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-368353428  

I think `embed` is also a good name.  
  
> Will you also write the doc for it?  
  
Sure.  
  
> I'm convinced now that we should add "as".  
  
I don't see usages for it. May be you have a good example?  
  
> Perhaps with "as" added, we don't need "protect"?  
  
I think `as` in this example is only a less verbose workaround to `rule`.  
  
Comparison:  
========  
  
mtd | parameters  
--- | ---  
rule | ID, Attribute  
as | Attribute  
unit| None  
  
  
rule  
----  
```cpp  
auto record_value = rule<struct r_record_value, Record::values_type>{} = int_ >> *(',' >> int_);  
auto record = int_ >> ':' >> record_value;  
```  
  
as  
--  
```cpp  
auto record = int_ >> ':' >> as<Record::values_type>[int_ >> *(',' >> int_)];  
```  
  
unit  
----  
```cpp  
auto record = int_ >> ':' >> unit[int_ >> *(',' >> int_)];  
```  
  
For me `unit` is definitely the best way to do it.

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-02-25 23:17:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-368354636  

I have updated the directive code above, inheriting from underlying parser makes it simple.

---

## Comment 10

> Username: djowel  
> Created at: 2018-02-25 23:19:01 UTC  
> Updated at: 2018-02-25 23:51:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-368354726  

I think we need both. "as" can be a good (non-verbose) substitute for  non-recursive rules as it supplies the attribute type. I like "unit". Let's go for that.

---

## Comment 11

> Username: akimd  
> Created at: 2018-12-09 17:04:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/352#issuecomment-445554005  

So, were there any updates since then?
