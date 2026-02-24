# #122 Add LICENSE and README [Merged]

> Username: Flamefire  
> Created at: 2022-06-09 08:39:59 UTC  
> Updated at: 2022-06-10 08:32:33 UTC  
> Merged at: 2022-06-09 20:50:20 UTC  
> Closed at: 2022-06-09 20:50:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/122  

Show project description and CI badges on the repo site.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-06-09 18:13:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/122#issuecomment-1151445893  

@cmazakas This would also be useful. Especially when this is merged to develop and develop to master.  
A preview is available via the ["rich diff view"](https://github.com/boostorg/unordered/pull/122/commits/f4ddf18124777ef5f1e5dd8d0d743e82e9dc2c5b?short_path=b335630#diff-b335630551682c19a781afebcf4d07bf978fb1f8ac04c6bf87428ed5106870f5)

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-06-10 08:32:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/122#issuecomment-1152115444  

@cmazakas Thanks for merging. See how now Github displays "BSL-1.0 license" on the right side of the repo overview.  
  
I think now it's time to merge develop to master. Maybe first have a look at https://github.com/boostorg/unordered/blob/develop/.codecov.yml and https://codecov.io/gh/boostorg/unordered/branch/develop to see if you may want changes       
E.g. some prefer to exclude the test folder, while I personally find it useful as coverage of the test folder should always be 100% and any value below means some unexpected results and/or unused code which can be removed. That should (ideally) be visible by missing coverage of the source/headers but I find that easier to spot in the tests first.       
This here is mine: https://github.com/boostorg/nowide/blob/develop/.codecov.yml  
  
See especially the ignoring of 2 test-util headers, in your case that could be `test/helpers/invariants.hpp` and `test/helpers/generators.hpp` and the changed comment, as the codecov bot contributes to PRs. See mine: https://github.com/boostorg/nowide/pull/158#issuecomment-1146619263  
  
Oh and maybe remove the `Sample codecov configuration file. Edit as required` from the header, my bad :D

---
