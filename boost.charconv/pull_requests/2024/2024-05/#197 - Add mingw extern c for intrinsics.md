# #197 Add mingw extern c for intrinsics [Merged]

> Username: mborland  
> Created at: 2024-05-10 05:56:41 UTC  
> Updated at: 2024-05-13 07:07:35 UTC  
> Merged at: 2024-05-13 07:07:32 UTC  
> Closed at: 2024-05-13 07:07:32 UTC  
> Url: https://github.com/boostorg/charconv/pull/197  

Closes: #196

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-10 06:38:18 UTC  
> Updated_at: 2024-05-13 06:57:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/197#issuecomment-2103964041  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/197?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.01%. Comparing base [(`11f03b7`)](https://app.codecov.io/gh/boostorg/charconv/commit/11f03b718181033b3d3ce95b958b7aa06e798d33?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e7e7f60`)](https://app.codecov.io/gh/boostorg/charconv/pull/197?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/197/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/197?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #197   +/-   ##  
========================================  
  Coverage    94.01%   94.01%             
========================================  
  Files           66       66             
  Lines         8439     8439             
========================================  
  Hits          7934     7934             
  Misses         505      505             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/197?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/197?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [11f03b7...e7e7f60](https://app.codecov.io/gh/boostorg/charconv/pull/197?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-05-10 07:15:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/197#issuecomment-2104040396  

@Flamefire What is the proper way to pass a flag to b2 with boost-ci? I added https://github.com/boostorg/charconv/pull/197/files#diff-b803fcb7f17ed9235f1e5cb1fcd2f5d3b2838429d4368ae4c57ce4436577f03fR749 but when the command is called it doesn't have the proceeding cxxflags: https://github.com/boostorg/charconv/actions/runs/9028608642/job/24809413066?pr=197#step:7:57

---

## Comment 3

> Username: Flamefire  
> Created_at: 2024-05-10 15:44:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/197#issuecomment-2104831158  

> @Flamefire What is the proper way to pass a flag to b2 with boost-ci?  
  
I had to look that up too as it isn't obvious: When writing the CI (shell) scripts we had to ensure compatibility with Boost repositories already in use without requiring all of them to update. Those used `B2_CXXFLAGS=cxxflags=-flag` which made the configs harder to read then necessary.   
  
The detection of updated configs is done by setting `B2_CI_VERSION=1` (which allows to do changes by increasing that at some point). So add `BOOST_CI_VERSION: 1` to https://github.com/boostorg/charconv/blob/02c6e699166bdc31bcfd21c49b9c6b4e22971eab/.github/workflows/ci.yml#L20-L23, see https://github.com/boostorg/boost-ci/blob/cad342d3aafbc1d4faa995ada544c4b66bcdf4ad/.github/workflows/ci.yml#L29

---

## Comment 4

> Username: mborland  
> Created_at: 2024-05-13 07:07:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/197#issuecomment-2106809470  

> > @Flamefire What is the proper way to pass a flag to b2 with boost-ci?  
>   
> I had to look that up too as it isn't obvious: When writing the CI (shell) scripts we had to ensure compatibility with Boost repositories already in use without requiring all of them to update. Those used `B2_CXXFLAGS=cxxflags=-flag` which made the configs harder to read then necessary.  
>   
> The detection of updated configs is done by setting `B2_CI_VERSION=1` (which allows to do changes by increasing that at some point). So add `BOOST_CI_VERSION: 1` to  
>   
> https://github.com/boostorg/charconv/blob/02c6e699166bdc31bcfd21c49b9c6b4e22971eab/.github/workflows/ci.yml#L20-L23  
>   
> , see https://github.com/boostorg/boost-ci/blob/cad342d3aafbc1d4faa995ada544c4b66bcdf4ad/.github/workflows/ci.yml#L29  
  
CI looks good with this addition. Thanks as always for the help.

---
