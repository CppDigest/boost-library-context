# #16 Add program_options example with a generic custom validator for enums [Open]

> Username: sdebionne  
> Created at: 2021-06-09 07:38:16 UTC  
> Updated at: 2022-03-07 16:33:53 UTC  
> Url: https://github.com/boostorg/describe/pull/16  

An example that shows how to support `enum`s as options with Boost.ProgamOptions.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-02-19 01:50:04 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1045534869  

Maybe update this to use enum_from_string and enum_to_string?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-02-19 01:51:50 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1045537325  

Also, the op<< overload for described enums probably belongs to operators.hpp?

---

## Comment 3

> Username: sdebionne  
> Created_at: 2022-03-01 09:20:23 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1055201955  

Indeed, this PR needs an update... I have more of these kind of examples (e.g. integration with fmt::format, pybind11...), do you think it's worth merging them to your repos?

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-03-01 15:58:36 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1055593211  

I already added fmtlib examples.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2022-03-07 10:53:06 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1060503332  

> Also, the op<< overload for described enums probably belongs to operators.hpp?  
  
AFAIU, `using boost::describe::operators::operator<<;` only works with described structs/classes not enum, no?

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-03-07 15:04:16 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1060784519  

Yes, but we can add an `operator<<` for enums there as well. Although that would make it impossible to use just one of the two as they are both called `boost::describe::operators::operator<<` so `using` will expose both.

---

## Comment 7

> Username: sdebionne  
> Created_at: 2022-03-07 15:37:18 UTC  
> Url: https://github.com/boostorg/describe/pull/16#issuecomment-1060821008  

I think an overload of `operator<<` for enums would be welcome (just added in the fixup). What if we put them in different namespaces aka `using boost::describe::enum_operators::operator<<; `?

---
