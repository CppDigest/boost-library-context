# #12 - LEAF binaries are too large, due to including iostream/locale [Closed]

> Username: brooksprumo  
> Created at: 2020-10-20 18:40:35 UTC  
> Updated at: 2020-10-21 17:23:19 UTC  
> Closed at: 2020-10-21 17:23:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/12  

Here's [a link to the Slack thread](https://cpplang.slack.com/archives/C27KZLB0X/p1603215969117200) where I first mentioned the issue.  
  
The gist is that error.hpp includes `sstream`, which then includes `iostream` and `locale`. These two headers result in a binary increase of ~210 KB. My total binary size available is ~512 KB, so this makes using LEAF not realistically possible.  
  
It looks like `e_unexpected_info::add()` uses `stringstream`. From our discussion, it was mentioned that it should be possible with the configuration macros to not include this, and thus not include `sstream`.

---

## Comment 1

> Username: zajo  
> Created at: 2020-10-21 02:55:31 UTC  
> Url: https://github.com/boostorg/leaf/issues/12#issuecomment-713261240  

Please confirm that the latest on the `develop` brach solves the problem. If there are other items that can be trimmed, let me know.

---

## Comment 2

> Username: brooksprumo  
> Created at: 2020-10-21 13:00:31 UTC  
> Url: https://github.com/boostorg/leaf/issues/12#issuecomment-713551434  

Building with `c364a95` works for me! Thank you!
