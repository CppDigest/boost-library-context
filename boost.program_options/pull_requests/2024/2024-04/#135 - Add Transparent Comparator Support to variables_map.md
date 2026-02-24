# #135 Add Transparent Comparator Support to variables_map [Open]

> Username: joesdiner  
> Created at: 2024-04-19 14:49:14 UTC  
> Updated at: 2024-07-01 17:28:46 UTC  
> Url: https://github.com/boostorg/program_options/pull/135  

First boost contribution, so not certain if everything is the "boost" way to do things. I also couldn't get program_options to build as a standalone repo, so I couldn't run all the other tests against this change. The changes worked in an external application though and the unit tests I added. That said,  
  
Add a [transparent comparator](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3465.pdf) to `boost::program_options::variables_map` if you are using c++14 or later. This allows you to look up items in the map with objects comparable to a `std::string` (`string_view`, c-string literal, etc), without a temporary `std::string` object being implicitly created.  
  
I was originally going to add a `Compare` template parameter to `variables_map` that gets passed along to the parent `std::map` class to make this behavior opt-in (as it is on a bare `std::map`), but along with proving to be difficult to implement with many changes required, that also would have been a breaking API change for users as (almost?) all existing instances of `variables_map` would have to be updated to at least `variables_map<>`. This would also be an ugly syntax to have to use.  
  
If I understand the "Impact on existing code" caveats from the transparent comparator paper, I don't think making this the default behavior would break realistic uses of `variables_map`. I would hope no one was using an object implicitly convertible to a `std::string` to access into `variables_map`. If they were, and the object isn't comparable to a `std::string`, this would be a compile error, that should be readily resolved. If the object was comparable to a `std::string` in the same manor that it converted to a `std::string` things would works as before but slightly more efficiently. Only if they were using an object that implicitly converted to a `std::string` AND comparable to a `std::string`, but in a way that differs than the conversion, could a bug/change in existing code be introduced without any feedback/warning from the compiler.  
  
If the risk in this last issue (described as a "pathological situation" in the paper) is deemed to high though, perhaps the transparent behavior should be opt in via a macro instead of opt out. Another alternative would be to a sibling class to `variables_map` that is the same except for using a transparent comparator, but this would require a refactor and/or increase maintenance. Or if someone knows a way to add a template parameter to `variables_map` as I originally explored without having to add angle brackets all over the place, that could be the best solution.  
  
I also think a `template<class K>operator[](K&& x) const` overload for `abstract_variables_map` could be useful as well to reduce potential unneeded object creation there, but since I don't use that operator that isn't part of this PR.  
  
Closes #134

---
