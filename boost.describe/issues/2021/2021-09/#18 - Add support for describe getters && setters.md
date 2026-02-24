# #18 - Add support for describe getters && setters [Open]

> Username: denzor200  
> Created at: 2021-09-11 12:48:25 UTC  
> Updated at: 2022-10-11 12:56:24 UTC  
> Url: https://github.com/boostorg/describe/issues/18  

I suggest adding support for getters and setters to the library. I see this new functionality like this:  
```  
#include <boost/describe.hpp>  
#include <iostream>  
  
struct employee  
{  
    void                  set_name    (std::string name)         ;  
    void                  set_age     (int age)                  ;  
    const std::string&    get_name    ()                   const ;  
    int                   get_age     ()                   const ;  
};  
  
BOOST_DESCRIBE_STRUCT(  
    employee, (), (  
        ("name", get_name, set_name),  
        ("age", get_age,  set_age)))  
  
  
int main()  
{  
    using boost::describe::describe_members;  
    using boost::describe::mod_any_access;  
	  
    employee emp;  
    emp.set_name("John");  
    emp.set_age(20);  
      
    boost::mp11::mp_for_each<describe_members<decltype(emp), mod_any_access>>([&](auto D){  
        auto getter = D.getter; // D also have "setter" field, but a "pointer" field is unavailable  
        std::cout << D.name << " = " << (emp.*getter)() << std::endl;  
    });   
	  
    // Will print:  
    //    name = John  
    //    age = 20  
      
    return 0;  
}  
  
```

---

## Comment 1

> Username: denzor200  
> Created at: 2022-07-08 04:38:59 UTC  
> Url: https://github.com/boostorg/describe/issues/18#issuecomment-1178541707  

I found a better way to do this:  
```  
struct employee  
{  
    void                  set_name    (std::string name)         ;  
    void                  set_age     (int age)                  ;  
    const std::string&    get_name    ()                   const ;  
    int                   get_age     ()                   const ;  
    struct employee_data& data        ()                         ;  
};  
  
BOOST_DESCRIBE_STRUCT(employee, (), (  
    (set_name, (setter, "name")),  
    (set_age,  (setter, "age")),  
    (get_name, (getter, "name")),  
    (get_age,  (getter, "age")),  
    (data),    (accessor, "data")  
))  
  
int main()  
{  
    using boost::describe::describe_members;  
    using boost::describe::mod_getters;  
    using boost::describe::mod_accessors;  
    using boost::describe::is_setter;  
    using boost::describe::get_encapsulated_field_name;  
  
    employee emp;  
    emp.set_name("John");  
    emp.set_age(20);  
    emp.data() = employee_data();  
     
    boost::mp11::mp_for_each<describe_members<decltype(emp), mod_getters | mod_accessors>>([&](auto D){  
        const auto pointer = D.pointer;  
        std::cout << get_encapsulated_field_name(D) << " = " << (emp.*pointer)() << std::endl;  
    });   
  
    // Will print:  
    //    name = John  
    //    age = 20  
    //    data = employee_data  
  
    return 0;  
}  
```  
  
Also, its compatible with https://github.com/boostorg/describe/issues/13. We can start by making https://github.com/boostorg/describe/issues/13 and then separately declare the `setter`, `getter` and `accessor` as service attributes.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-02 04:55:22 UTC  
> Url: https://github.com/boostorg/describe/issues/18#issuecomment-1264551276  

How's that different from a function on the language level?

---

## Comment 3

> Username: denzor200  
> Created at: 2022-10-11 07:02:14 UTC  
> Url: https://github.com/boostorg/describe/issues/18#issuecomment-1274179395  

> How's that different from a function on the language level?  
  
I'm not getting your question. Please reword or submit a little better example.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-10-11 12:28:21 UTC  
> Url: https://github.com/boostorg/describe/issues/18#issuecomment-1274608323  

Getters and setter don't exist in C++, this isn't C# or python. You're trying to describe functions, which you already can do with boost.describe.

---

## Comment 5

> Username: denzor200  
> Created at: 2022-10-11 12:54:12 UTC  
> Updated at: 2022-10-11 12:56:24 UTC  
> Url: https://github.com/boostorg/describe/issues/18#issuecomment-1274644099  

> Getters and setter don't exist in C++, this isn't C# or python. You're trying to describe functions, which you already can do with boost.describe.  
  
I'm trying to suggest alternative for [BOOST_HANA_ADAPT_ADT](https://www.boost.org/doc/libs/master/libs/hana/doc/html/group__group-_struct.html#ga141761435a7826b3cbe646b4f59eaf0a). I haven't thought about C# or Python getters and setters.  
  
It's useful for https://github.com/boostorg/describe/pull/32
