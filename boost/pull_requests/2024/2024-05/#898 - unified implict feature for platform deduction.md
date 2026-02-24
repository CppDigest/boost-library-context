# #898 unified implict feature for platform deduction [Merged]

> Username: grisumbras  
> Created at: 2024-05-01 14:54:26 UTC  
> Updated at: 2024-05-01 20:30:33 UTC  
> Merged at: 2024-05-01 20:30:33 UTC  
> Closed at: 2024-05-01 20:30:33 UTC  
> Url: https://github.com/boostorg/boost/pull/898  

This changes separate deduction of `address-model` and `architecture` with a single `x-deduced-platform` (the corresponding rule is `platform`. The purpose of this is to shorten the target path, while keeping it unique for different configurations.  
  
The logic of the conditional rule is this:  
1. If there's already `x-deduced-platform` in the property set, then return that (this is needed for repeated evaluation of conditionals).  
2. If eihter `address-model` or `architecture` could not be deduced, return nothing.  
3. Get  `address-model` and `architecture` properties from the property set, assign deduced values to any if it is empty.  
4. Construct the corresponding value of `x-deduced-platform`. If there's no corresponfing value (e.g. user requested an architecture we don't support for deduction), return nothing.  
5. Otherwise return the constructed value.  
  
The feature `x-deduced-platform` is composite. Due to that it subsumes properties that constitute it. Meaning, `<address-model>32 <x-deduced-platform>x86` corresponds to the path `x86` and not `x86/address-model-32`. Which is pretty neat.  
  
The `x-` in the name `x-deduced-platform` is so that it sorts last.

---

## Comment 1

> Username: grisumbras  
> Created_at: 2024-05-01 15:33:07 UTC  
> Url: https://github.com/boostorg/boost/pull/898#issuecomment-2088641260  

Changed the feature to have values in the form X_32 and X_64. The change to underscore was because there's apparently special logic for hyphens in values of implicit features (presumably, for toolsets).

---
