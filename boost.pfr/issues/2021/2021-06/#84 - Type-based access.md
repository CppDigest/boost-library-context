# #84 - Type-based access [Closed]

> Username: denzor200  
> Created at: 2021-06-20 09:09:21 UTC  
> Updated at: 2025-06-20 14:34:01 UTC  
> Closed at: 2025-06-20 14:33:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/84  

How about this sugar?  
```  
struct sample {  
  int first;  
  const char* second;  
  double third;  
};  
  
auto t = sample {1, "Foo", 3.14};  
  
// index-based access  
std::cout << "(" << boost::pfr::get<0>(t) << ", " << boost::pfr::get<1>(t)  
               << ", " << boost::pfr::get<2>(t) << ")\n"; // Outputs: (1, Foo, 3.14)  
// type-based access (Just like std::get after C++14)  
std::cout << "(" << boost::pfr::get<int>(t) << ", " << boost::pfr::get<const char*>(t)  
               << ", " << boost::pfr::get<double>(t) << ")\n"; // Outputs: (1, Foo, 3.14)  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-07-13 14:41:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-879148517  

Looks good! PRs are welcomed!

---

## Comment 2

> Username: rorymcstay  
> Created at: 2021-10-03 16:09:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-932980493  

What if there are two members of the same type?

---

## Comment 3

> Username: denzor200  
> Created at: 2021-10-03 20:30:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-933020443  

> What if there are two members of the same type?  
  
I suppose in this case there should be a compilation error, as happens with `std::get` and `std::tuple`.  
https://godbolt.org/z/zYKMzdv36

---

## Comment 4

> Username: awulkiew  
> Created at: 2023-03-02 21:22:16 UTC  
> Updated at: 2023-03-02 22:22:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-1452564054  

@apolukhin I'm thinking about a potential implementation, i.e. whether or not reference types stored in the `tuple` returned by `tie_as_tuple` could be analyzed or rather should the original ones defined in a struct be?  
  
I'm asking because after tie the information about the references and cv-qualifiers can be lost/added so it's unclear to me what types would have to be passed into `get`. Consider e.g. (compiling in C++17):  
```  
struct sample  
{  
    int first;  
    int& second;  
    double third;  
};  
int i = 1;  
const volatile sample s = {0, i, 2.0};  
  
decltype(auto) a = boost::pfr::get<0>(s); // const volatile int&  
decltype(auto) b = boost::pfr::get<1>(s); // int&  
decltype(auto) c = boost::pfr::get<2>(s); // const volatile double&  
```  
  
Btw, shouldn't the type of `b` be `const volatile int&` as well?  
  
Are reference members supported?  
The following functions seems to return inconsistent tuples:  
  
```  
auto t0 = boost::pfr::detail::tie_as_tuple(s); // int const volatile&, int&, double const volatile&  
auto t1 = boost::pfr::structure_tie(s);        // int const volatile&, int const&, double const volatile&  
auto t2 = boost::pfr::structure_to_tuple(s);   // int, int, double  
```  
  
So if the implementation was based on `tie_as_tuple` with removed references and cv-qualifiers it'd work as if `t2` was passed to `get`.  
  
EDIT: I re-read the documentation and it seems that neither references nor const members are supported. Is this also true for `volatile` fields?  
So AFAIU it should be enough to base the implementation on the type returned by `tie_as_tuple`.

---

## Comment 5

> Username: denzor200  
> Created at: 2023-05-28 16:23:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-1566190967  

@awulkiew   
  
> Is this also true for volatile fields?  
  
It wasn't documented but I think this is also true for `volatile` because there is no supporting code to allow `volatile`(otherwise it code would be also for `const` and thus woundn't be such restrictions).  
  
@apolukhin I think we should update the docs(add the note about `volatile` restriction).   
  
Or maybe we can disable all const-volatile fields restrictions, at least for C++17. I started to discuss about it here: https://github.com/boostorg/pfr/issues/105

---

## Comment 6

> Username: apolukhin  
> Created at: 2025-06-20 14:33:32 UTC  
> Updated at: 2025-06-20 14:34:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/84#issuecomment-2991850731  

Fixed in https://github.com/boostorg/pfr/commit/ab509a5b32c9c529e42746bcb5a0f5b5da5f5e70  
  
The sample compiles https://godbolt.org/z/z5j39asvh
