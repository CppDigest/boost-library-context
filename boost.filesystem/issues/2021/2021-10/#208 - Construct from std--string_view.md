# #208 - Construct from std::string_view [Closed]

> Username: Aleksmia  
> Created at: 2021-10-05 12:41:32 UTC  
> Updated at: 2022-08-31 10:10:15 UTC  
> Closed at: 2022-08-31 10:10:15 UTC  
> Url: https://github.com/boostorg/filesystem/issues/208  

The code below is impossible because boost::filesystem doesn't support construction from std::string_view:  
  
```c++  
namespace` fs = boost::filesystem;  
  
constexpr std::string_view fname = "file.txt";  
fs::path some_dir = "~/dir";  
  
fs::path full_path = some_dir / fname;  
```  
  
One of the possible solutions is the add `std::string_view` to `path_traits::is_pathable`  
  
What are the reasons not to support std::string_view like the `pathable` type?  
Probably it has more relevant solution than I suggested?  
  
PS  
I can prepare the PR for releasing this feature. May be it will be useful someone else.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-10-05 13:33:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/208#issuecomment-934420148  

> What are the reasons not to support `std::string_view` like the `pathable` type?  
  
Because noone got around to support it. Same with `boost::string_view`.  
  
I should mention that I don't really like how `path_traits` is implemented, especially that it brings in too many unnecessary dependencies. Adding more dependencies is not something I would like, the whole thing should be deprecated and redesigned to work for arbitrary sequences of characters that support `data`, `begin` and `end`. When that will happen, I cannot tell as there are other things pending in Boost.Filesystem.

---

## Comment 2

> Username: Aleksmia  
> Created at: 2021-10-06 10:08:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/208#issuecomment-935897860  

What do you think if I prepare PR with your solution as soon as I can? Maybe it would be faster and I'll help you with it?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-10-06 11:42:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/208#issuecomment-936072510  

PRs are welcome, of course, but I can't promise I will merge it. I haven't thought out how `path_traits` should look like myself. :)
