# #55 - Several algorithms not mentioned on the front page of docs [Closed]

> Username: tonyelewis  
> Created at: 2018-11-01 21:09:40 UTC  
> Updated at: 2020-04-08 03:47:37 UTC  
> Closed at: 2020-04-08 03:47:37 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55  

Several algorithms provided by the library aren't mentioned on the front page of the docs and hence are likely to go unnoticed by many users.  
  
I'm happy to submit a PR for this. Please shout if I should do that.

---

## Comment 1

> Username: tonyelewis  
> Created at: 2018-11-01 21:14:20 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-435189606  

Some examples:  
* `is_increasing(...)`  
* `is_strictly_increasing(...)`  
* `is_decreasing(...)`  
* `is_strictly_decreasing(...)`

---

## Comment 2

> Username: mclow  
> Created at: 2018-11-01 23:15:23 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-435218168  

I'm happy to review a PR for improving the docs.

---

## Comment 3

> Username: tonyelewis  
> Created at: 2018-11-01 23:18:25 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-435218758  

Thanks. I hope to be able to look at this tomorrow.

---

## Comment 4

> Username: tonyelewis  
> Created at: 2018-11-02 15:55:13 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-435426279  

There are 2 steps for me to complete.  
  
Step 2 is for me to actually update the `.qbk` files and check the resulting. This looks substantially less intuitive than I'd anticipated&hellip;:confused:&hellip;  
  
In the meantime, here's the progress I've made re Step 1&hellip;: identifying what I want to add&hellip;  
  
## C++11 Algorithms  
  
* `partition_copy`              &rarr; `boost/algorithm/cxx11/partition_copy.hpp`  
* `copy_if`                     &rarr; `boost/algorithm/cxx11/copy_if.hpp`  
* `copy_n`                      &rarr; `boost/algorithm/cxx11/copy_n.hpp`  
* `iota`                        &rarr; `boost/algorithm/cxx11/iota.hpp`  
  
## C++17 Algorithms  
  
* `for_each_n`                  &rarr; `boost/algorithm/cxx17/for_each_n.hpp`  
  
## Other Algorithms  
  
* `none_of_equal`               &rarr; `none_of.qbk`  
* `one_of_equal`                &rarr; `one_of.qbk`  
* `is_decreasing`               &rarr; `ordered-hpp.qbk`  
* `is_increasing`               &rarr; `ordered-hpp.qbk`  
* `is_strictly_decreasing`      &rarr; `ordered-hpp.qbk`  
* `is_strictly_increasing`      &rarr; `ordered-hpp.qbk`  
* <sub><sup>(`clamp`)</sup></sub>  
* `clamp_range`                 &rarr; `clamp-hpp.qbk`  
* <sub><sup>(`find_not`)</sup></sub>  
* <sub><sup>(`find_backward`)</sup></sub>  
* `find_not_backward`           &rarr; `find_backward.qbk`  
* `find_if_backward`            &rarr; `find_backward.qbk`  
* `find_if_not`                 &rarr; `find_not.qbk`  
* `find_if_not_backward`        &rarr; `find_backward.qbk`  
* <sub><sup>(`gather`)</sup></sub>  
* <sub><sup>(`hex`)</sup></sub>  
* `unhex`                       &rarr; `hex.qbk`  
* `hex_lower`                   &rarr; `boost/algorithm/hex.hpp`  
* <sub><sup>(`is_palindrome`)</sup></sub>  
* <sub><sup>(`is_partitioned_until`)</sup></sub>  
* <sub><sup>(`apply_permutation`)</sup></sub>  
* `apply_reverse_permutation`   &rarr; `apply_permutation.qbk`  
* `copy_until`                  &rarr; `boost/algorithm/cxx11/copy_if.hpp`  
* `copy_while`                  &rarr; `boost/algorithm/cxx11/copy_if.hpp`  
* `iota_n`                      &rarr; `boost/algorithm/cxx11/iota.hpp`  
* `power`                       &rarr; `boost/algorithm/algorithm.hpp`  
  
## Not-yet-documented C++17 Algorithms  
  
* `exclusive_scan`              in `boost/algorithm/cxx17/exclusive_scan.hpp`  
* `inclusive_scan`              in `boost/algorithm/cxx17/inclusive_scan.hpp`  
* `reduce`                      in `boost/algorithm/cxx17/reduce.hpp`  
* `transform_exclusive_scan`    in `boost/algorithm/cxx17/transform_exclusive_scan.hpp`  
* `transform_inclusive_scan`    in `boost/algorithm/cxx17/transform_inclusive_scan.hpp`  
* `transform_reduce`            in `boost/algorithm/cxx17/transform_reduce.hpp`  
  
## Not-yet-documented Other Algorithms  
  
* `minmax`                      in `boost/algorithm/minmax.hpp`  
* `first_max_element`           in `boost/algorithm/minmax_element.hpp`  
* `first_min_element`           in `boost/algorithm/minmax_element.hpp`  
* `first_min_first_max_element` in `boost/algorithm/minmax_element.hpp`  
* `first_min_last_max_element`  in `boost/algorithm/minmax_element.hpp`  
* `last_max_element`            in `boost/algorithm/minmax_element.hpp`  
* `last_min_element`            in `boost/algorithm/minmax_element.hpp`  
* `last_min_first_max_element`  in `boost/algorithm/minmax_element.hpp`  
* `last_min_last_max_element`   in `boost/algorithm/minmax_element.hpp`  
* `minmax_element`              in `boost/algorithm/minmax_element.hpp`  
* `partition_subrange`          in `boost/algorithm/sort_subrange.hpp`  
* `sort_subrange`               in `boost/algorithm/sort_subrange.hpp`

---

## Comment 5

> Username: mclow  
> Created at: 2019-12-04 01:09:05 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-561428872  

Is this fixed now?

---

## Comment 6

> Username: tonyelewis  
> Created at: 2019-12-04 07:51:26 UTC  
> Url: https://github.com/boostorg/algorithm/issues/55#issuecomment-561520514  

Yep - thanks very much. I hope this helps more users get exposed to more of this library.  
  
I'm happy for you to close this issue.
