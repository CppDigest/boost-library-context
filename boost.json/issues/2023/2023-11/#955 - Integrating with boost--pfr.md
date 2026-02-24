# #955 - Integrating with boost::pfr? [Open]

> Username: schaumb  
> Created at: 2023-11-22 19:02:12 UTC  
> Updated at: 2023-12-15 12:49:13 UTC  
> Url: https://github.com/boostorg/json/issues/955  

The recent `pfr` update introduces a new feature allowing the retrieval of aggregated object [member names](https://github.com/boostorg/pfr/pull/129).  
This enhancement enables the parsing and serialization of aggregate object types without the need for macros or defined functions, directly accessing the members and their names.  
  
(For potential concerns or limitations, please refer to the [caveats](https://github.com/boostorg/pfr/issues/100).)  
  
Your thoughts on this development would be appreciated.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-11-23 08:22:07 UTC  
> Url: https://github.com/boostorg/json/issues/955#issuecomment-1823966143  

If I unterstand the issue correctly, this is about conversions. From the cursory look, such feature can be a part ofBoost.PFR. PFR doesn't even have to add a physical dependency on JSON (see https://www.boost.org/doc/libs/release/libs/json/doc/html/json/conversion/avoiding_physical_dependency.html).

---

## Comment 2

> Username: denzor200  
> Created at: 2023-11-25 23:15:48 UTC  
> Url: https://github.com/boostorg/json/issues/955#issuecomment-1826440071  

> such feature can be a part ofBoost.PFR  
  
Sounds really strange :) Boost.Json already have Boost.Desribe intergation, why? Doesn't it was better to make such feature a part of Describe? And then go futher and make xml, yaml, sql and anything else a part of PFR/Describe? Of course not, since they both are reflection libraries and they not obliged to be swiss knife for every reflection usecase.  
  
BTW Boost.PFR is a great candidate to be used as fallback for type for which conversion is not found. And shoud we need it as fallback, it's possible to implement only on the Boost.JSON side.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-11-28 08:16:03 UTC  
> Url: https://github.com/boostorg/json/issues/955#issuecomment-1829310070  

By the same logic, Boost.JSON can't implement conversion logic for every reflection library and composite type in existence. How about this: if someone makes a JSON conversion implementation for PFR, I will integrate it as an example.

---

## Comment 4

> Username: schaumb  
> Created at: 2023-12-15 12:49:12 UTC  
> Url: https://github.com/boostorg/json/issues/955#issuecomment-1857833632  

If anyone is interested in a JSON lib that has default field reflection, check [glaze](https://github.com/stephenberry/glaze#example). They use the same technique as boost PFR.   
  
I think boost::json needs to support reflection without specifying anything information about structs.
