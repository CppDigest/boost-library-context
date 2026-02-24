# #339 - Include base class members in BOOST_HANA_ADAPT_STRUCT [Open]

> Username: ldionne  
> Created at: 2017-04-23 19:35:55 UTC  
> Updated at: 2024-03-27 08:17:32 UTC  
> Url: https://github.com/boostorg/hana/issues/339  

Feature request lifted from [this](http://stackoverflow.com/q/43303700/627587) StackOverflow question:  
  
> Is there a way to use the `BOOST_HANA_ADAPT_STRUCT` macro for a struct that inherits from a base struct without repeating the accessors of the base struct? Right now I have something similar to the following example:  
>  
> ```c++  
> namespace hana = boost::hana;  
> namespace ns {  
>     struct Person {  
>        std::string name;  
>        int age;  
>     };  
> }  
> BOOST_HANA_ADAPT_STRUCT(ns::Person,  
>     name,  
>     age  
> );  
>   
> namespace ns {  
>     struct Employer : Person {  
>        std::string company;  
>     };  
> }  
> BOOST_HANA_ADAPT_STRUCT(ns::Employer,  
>     name, // duplication  
>     age,  // duplication  
>     company  
> );  
> ```  
  
Like @ricejasonf says in the comments, I don't think it's possible to do this out of the box (without reflection). However, it would be possible to do it if the macro received the set of base classes or something like that. We would then check that each base class is indeed a base class, and we would concatenate the members of both (or something like that).

---

## Comment 1

> Username: ldionne  
> Created at: 2017-05-19 07:01:26 UTC  
> Url: https://github.com/boostorg/hana/issues/339#issuecomment-302625551  

I think something like  
  
```c++  
BOOST_HANA_ADAPT_STRUCT(ns::Employer,  
    ns::Person,  
    company  
);  
```  
  
would work. Internally, we would check whether `ns::Person` is a type (is that possible at all?), and if so, we would check whether `std::is_base_of<ns::Person, ns::Employer>{}` is true. That would remove the need to add a completely new macro that handles base classes. If checking whether an arbitrary identifier is a type is not possible, I think we need a new macro.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-05-19 07:04:53 UTC  
> Url: https://github.com/boostorg/hana/issues/339#issuecomment-302626131  

Other possible alternatives (thanks to @mmha):  
  
```c++  
BOOST_HANA_ADAPT_STRUCT((ns::Employer, ns::Person),  
    company  
);  
  
BOOST_HANA_ADAPT_STRUCT((ns::Employer)(ns::Person),  
    company  
);  
```

---

## Comment 3

> Username: malikkirchner  
> Created at: 2020-12-16 16:23:50 UTC  
> Url: https://github.com/boostorg/hana/issues/339#issuecomment-746575497  

This would be handy.

---

## Comment 4

> Username: polariszz  
> Created at: 2024-03-27 08:17:31 UTC  
> Url: https://github.com/boostorg/hana/issues/339#issuecomment-2022181581  

Is there anything update with this feature?
