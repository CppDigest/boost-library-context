# #87 Brainstorm .clang-format proposal [Closed]

> Username: mloskot  
> Created at: 2018-04-28 22:05:05 UTC  
> Updated at: 2020-03-25 23:42:48 UTC  
> Closed at: 2020-03-25 23:38:35 UTC  
> Url: https://github.com/boostorg/gil/pull/87  

_IMPORTANT: This is NOT ready to merge, this may NEVER be merged. It aims to serve as subject for discussion if and how `clang-format` may become useful for us._  
  
### Description  
  
Following ~[90](https://lists.boost.org/boost-gil/2018/04/0028.php)~ [100 character column limit](https://github.com/boostorg/gil/commit/5024f6ffdddecf4c0432892bf77fc5cf2ae1c1f8), I decided to try `clang-format` and start development of annotated `.clang-format` configuration file that may be useful for Boost.GIL  
  
Please, **consider this as a proposal to brainstorm**, try and refine `.clang-format` for Boost.GIL.  
  
Goals:  
- Provide `.clang-format` with reasonable defaults for Boost.GIL as a basic formatting guideline and helper.  
- Support edit - select (class, function) - format workflow  with consistent settings across editors.  
- Help for manual formatting: run `clang-format` first, then  adjust formatting in those (few) palces where necessary.  
- Use `clang-format` from its latest release.  
  
No goals:  
- No automatic reformat on file save or pre-commit is proposed.  
- No bulk reformat is proposed.  
- No CI build running clang-format verifying code style.  
  
### Environment  
  
- clang-forma 5.0 or later

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-04-29 00:54:22 UTC  
> Url: https://github.com/boostorg/gil/pull/87#issuecomment-385216626  

Can you add a couple of problematic files which would benefit? Just so I can see a difference.  
  
Also, are these tools good enough for complicated templated code?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-04-30 15:00:52 UTC  
> Updated_at: 2018-04-30 15:11:44 UTC  
> Url: https://github.com/boostorg/gil/pull/87#issuecomment-385425529  

@chhenning  
> Can you add a couple of problematic files which would benefit? Just so I can see a difference.  
  
No, sorry, I can't. I didn't have time to provide you guys with comprehensive picture that you can just have a look at.  
  
I just aim to provided anyone interested in trying out `clang-format` with basic playground:  
- I prepared `.clang-format` with annotations so it is easier to read and modify without constantly looking at https://clang.llvm.org/docs/ClangFormatStyleOptions.html  
- I tried to filter out options not available in clang-format **5.0**  
  
You will have to run it yourself on a whole file or selection/parts of file only that you are interested to see the `clang-format`-ing effect for. Then, I assume, you may want to tweak the `.clang-format` configuration, run again. Once you find your preferred formatting for certain aspects of Boost.GIL code, then you come back with comments and suggest modification to this PR.  
  
So, this PR should be labelled not for lazy ones ;)  
  
> Also, are these tools good enough for complicated templated code?  
  
What is good enough, what do you consider good enough, what is a complicated templated code. I mean, you are asking difficult questions ;)   
  
In my personal opinion, clang-format has limitations as any code formatting tool, but clang-format is a tool that has the best capacity of all open source tools currently available in the wild.  
  
Finally, please keep in mind this PR is tagged with **WIP** and **Brainstorm**  for reason. I'm not even convinced myself that `clang-format` can cover Boost.GIL code formatting requirements. So, let's try...

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-07-26 21:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/87#issuecomment-515606793  

I have just asked the StackOverflow for advise on desire formatting using `clang-format`  
  
[Can clang-format split template parameters list in multiple lines?](https://stackoverflow.com/q/57227251/151641)

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-07-31 12:10:01 UTC  
> Url: https://github.com/boostorg/gil/pull/87#issuecomment-516821237  

I answered a StackOverflow question on [spacing and breaking trailing return type with clang-format](https://stackoverflow.com/a/57279663/151641) which is also related to desired formatting discussed here.

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-03-25 23:42:18 UTC  
> Updated_at: 2020-03-25 23:42:48 UTC  
> Url: https://github.com/boostorg/gil/pull/87#issuecomment-604146546  

23702f9879e81bb3b08971eb153b199ea56bb0af adds good-enough `.clang-format` to `example/clang-format/`  
  
This is an example which offers _good enough_ configuration and  
may be _useful_ when writing code for GIL, but is not required though.  
After using it myself for fair bit of time now, it proved useful, even if code still requires some manual tweaks. This is **not** a complete configuration!

---
