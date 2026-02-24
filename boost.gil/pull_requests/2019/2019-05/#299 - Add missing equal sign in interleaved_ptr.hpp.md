# #299 Add missing equal sign in interleaved_ptr.hpp [Merged]

> Username: simmplecoder  
> Created at: 2019-05-07 18:05:06 UTC  
> Updated at: 2019-05-07 19:44:46 UTC  
> Merged at: 2019-05-07 19:44:45 UTC  
> Closed at: 2019-05-07 19:44:46 UTC  
> Url: https://github.com/boostorg/gil/pull/299  

### Description  
  
Fixed a syntax error in examples, missing = sign.  
  
### Environment  
  
- Compiler version: VC++  19.16.27030.1  
- Build settings: use_conan=true, others default  
- GIL Version (Git ref or `<boost/version.hpp>`): https://github.com/boostorg/gil/commit/10f1efff5bbb86bc79341601e341fc9fa86cfdf5  
  
### References  
  
Mentioned in gitter  
  
### Tasklist  
  
- [X] Fix the bug  
- [X] Add test case(s) (build attempt is a test, so no additional tests required)  
- [x] Ensure all CI builds pass (examples are not used in CI yet)  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-05-07 18:13:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/299#pullrequestreview-234676631  

@simmplecoder LGTM, thanks!  
  
I'd have one request and I hope @stefanseefeld will agree:  
  
Please, try to stick to Git recommendations regarding how you write your commit messages:  
- commit subject (<=80 characters long)  
- blank line  
- commit description (wrap lines at ~80 character line limit)  
because this is a well-established convention, de-facto standard, adopted by the vast community of Git users.  
  
Git treats the first line of a commit message in _special way_.  
It will be displayed in the `git shortlog` output and in the GitHub summaries.  
See [why good commit messages matter](https://chris.beams.io/posts/git-commit/).

📁 example/interleaved_ptr.hpp

```diff
  44 | private:
  45 |-     using parent_t boost::iterator_facade
  45 |+     using parent_t = boost::iterator_facade
```

> Username: mloskot  
> Created_at: 2019-05-07 18:07:51 UTC  
> Updated_at: 2019-05-07 18:13:47 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281757791  

Good catch!


📁 .gitignore

```diff
  26 |+ 
  27 |+ # Conan
  28 |+ conaninfo.txt
```

> Username: mloskot  
> Created_at: 2019-05-07 18:13:35 UTC  
> Updated_at: 2019-05-07 18:13:48 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281760919  

I'm curious what kind of use case caused this file to be placed in the `libs/gil`?  
  
I'm not an advanced user of Conan, but https://docs.conan.io/en/latest/reference/commands/consumer/info.html says  
  
> -if INSTALL_FOLDER, --install-folder INSTALL_FOLDER  
>                         local folder containing the conaninfo.txt and  
>                         conanbuildinfo.txt files (from a previous conan  
>                         install execution)

> Username: simmplecoder  
> Created_at: 2019-05-07 18:15:46 UTC  
> Updated_at: 2019-05-07 18:15:47 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281763283  

I believe it is caused by me trying to manually try conan install, which makes the change erroneous

> Username: simmplecoder  
> Created_at: 2019-05-07 18:18:10 UTC  
> Updated_at: 2019-05-07 18:18:11 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281765979  

let me try to clean clone it and run build again. I will report back about any changes

> Username: mloskot  
> Created_at: 2019-05-07 18:31:16 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281773917  

Don't worry about that. It does not hurt to ignore it. Leave it as is.  
  
You may notice, that despite of sharing several comments, I actually approved your PR without any request for changes. If I expected you to change something, you'd see my review status as not approved, but "changes requested" or something like that. You can find more about the review process on GitHub here https://help.github.com/en/articles/reviewing-proposed-changes-in-a-pull-request

> Username: mloskot  
> Created_at: 2019-05-07 18:31:34 UTC  
> Url: https://github.com/boostorg/gil/pull/299#discussion_r281774043  

I'm resolving this conversation :-)


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2019-05-07 18:21:36 UTC  
> Url: https://github.com/boostorg/gil/pull/299#issuecomment-490193284  

Roger. I will follow those in the future, thanks for teaching me essential basics!

---
