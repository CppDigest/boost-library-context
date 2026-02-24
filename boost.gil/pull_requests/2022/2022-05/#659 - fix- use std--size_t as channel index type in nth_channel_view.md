# #659 fix: use std::size_t as channel index type in nth_channel_view [Open]

> Username: striezel  
> Created at: 2022-05-04 16:20:09 UTC  
> Updated at: 2024-08-20 13:02:27 UTC  
> Url: https://github.com/boostorg/gil/pull/659  

### Description  
  
This unifies the channel index type in `nth_channel_view` and  
`image_view::num_channels` - the latter one already uses `std::size_t`.  
  
### References  
  
Fixes #373.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-04 17:02:27 UTC  
> Updated_at: 2024-05-05 17:34:04 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1117591325  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/659?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 81.10%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8821258`)](https://app.codecov.io/gh/boostorg/gil/pull/659?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
> :exclamation: Current head 8821258 differs from pull request most recent head 0f93276. Consider uploading reports for the commit 0f93276 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #659      +/-   ##  
===========================================  
- Coverage    82.12%   81.10%   -1.03%       
===========================================  
  Files          117      117                
  Lines         5355     5171     -184       
===========================================  
- Hits          4398     4194     -204       
- Misses         957      977      +20       
```  
  
</details>

---

## Comment 2

> Username: striezel  
> Created_at: 2022-05-31 15:43:13 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1142303423  

Changes have been rebased against the current `develop` branch.  
Furthermore, `std::size_t` is replaced by `boost::gil::index_t` in the affected files. At the moment `boost::gil::index_t` is an alias for `std::ptrdiff_t`, as suggested in https://github.com/boostorg/gil/issues/373#issuecomment-1117941296 and following comments.  
  
However, the basic idea was to unify the channel index type, any while I could change `image_view::num_channels` to return `boost::gil::index_t`, its implementation currently uses `gil::num_channels` which uses [`boost::mp11::mp_size`](https://www.boost.org/doc/libs/1_79_0/libs/mp11/doc/html/mp11.html#mp_sizel) which is an alias for [`boost::mp11::mp_size_t`](https://www.boost.org/doc/libs/1_79_0/libs/mp11/doc/html/mp11.html#mp_size_tn) which - you may have already guessed it by the name - finally uses `std::size_t`. So if we want to get rid of the signed/unsigned mixup (which I believe was the original intention behind #373), we either have to convince `boost::mp11::mp_size` to switch to `std::ptrdiff_t` (seems unlikely to me) or change `boost::gil::index_t` to be an alias for `std::size_t`.  
  
@mloskot:  
Any advice on how to proceed from here?

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-05-31 22:18:54 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1142691815  

How about re-implementing `num_channels` to something along these lines https://godbolt.org/z/3b4Pz8EKE ?  
  
```c++  
#include <type_traits>  
#include <boost/mp11.hpp>  
using namespace boost::mp11;  
  
struct R {};  
struct G {};  
struct B {};  
using rgb_t = mp_list<R, G, B>; // sample color space  
using index_t = std::ptrdiff_t;  
  
template <typename L>  
struct num_channels  
{  
    using type = typename mp_size<L>::type;  
    static constexpr index_t value = mp_size<L>::value;  
};  
  
static_assert(num_channels<rgb_t>::value == 3, "");  
```

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-06-29 16:48:10 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1170230469  

@striezel I've just noticed this PR received updates. What is the status of it? Do you want me to squeeze it for Boost 1.80?  Today is a deadline for major changes.

---

## Comment 5

> Username: striezel  
> Created_at: 2022-06-30 19:57:14 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1171619760  

@mloskot:  
> Do you want me to squeeze it for Boost 1.80? Today is a deadline for major changes.  
  
__No.__  
The deadline passed yesterday, so I guess it's not really an option to squeeze it into Boost 1.80 anymore. But even _if_ it were still possible, I am not really happy with the current state of this branch / pull request. So for the moment it is better to leave this PR out of the next release.  
  
The two issues which I see are:  
* It has merge conflicts that need to be resolved.  
  I did not have the time to resolve them the day before yesterday, so I just rebased against a more recent commit that still has no merge conflicts to avoid falling too far behind. But this definitely needs a rebase against the most current commit on the develop branch, and I will probably do this in the next days. It's just not in time for Boost 1.80 anymore, so there is no need to rush it and squeeze it in somehow.  
* Now that `boost::gil::index_t` is the way to go, some more places need to change from `std::size_t` or `int` (or whatever they are using) to `boost::gil::index_t`. This includes basically any use of `nth_channel_view` and `num_channels` within GIL, including tests.  
  
I don't see that I will be able to do this within the next one or two days, so there really is no need to rush this PR into Boost 1.80. It will be in Boost 1.81 then, I guess.

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-06-30 20:32:12 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1171648745  

> I don't see that I will be able to do this within the next one or two days, so there really is no need to rush this PR into Boost 1.80. It will be in Boost 1.81 then, I guess.  
  
Sure. No rush, no pressure, no worry. I'm very thankful for all your contributions.

---

## Comment 7

> Username: striezel  
> Created_at: 2022-09-01 17:45:17 UTC  
> Url: https://github.com/boostorg/gil/pull/659#issuecomment-1234592316  

> The two issues which I see are:  
>   
> * It has merge conflicts that need to be resolved.  
>   I did not have the time to resolve them the day before yesterday, so I just rebased against a more recent commit that still has no merge conflicts to avoid falling too far behind. But this definitely needs a rebase against the most current commit on the develop branch, and I will probably do this in the next days. It's just not in time for Boost 1.80 anymore, so there is no need to rush it and squeeze it in somehow.  
>   
> * Now that `boost::gil::index_t` is the way to go, some more places need to change from `std::size_t` or `int` (or whatever they are using) to `boost::gil::index_t`. This includes basically any use of `nth_channel_view` and `num_channels` within GIL, including tests.  
  
Rebase is done (force push from d745375efcaf8605ab30089cdec5b846c8e32daa to 5fa0eb8a439383631a3d0c87c9c4cedbee3556ac).  
  
Next: Find other places where `std::size_t` or `int` need to be replaced by `boost::gil::index_t`.

---
