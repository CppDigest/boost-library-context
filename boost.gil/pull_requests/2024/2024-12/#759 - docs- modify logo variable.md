# #759 docs: modify logo variable [Merged]

> Username: sdarwin  
> Created at: 2024-12-09 22:53:04 UTC  
> Updated at: 2024-12-10 12:08:45 UTC  
> Merged at: 2024-12-09 22:55:30 UTC  
> Closed at: 2024-12-09 22:55:30 UTC  
> Url: https://github.com/boostorg/gil/pull/759  

When building the docs with Sphinx 8.1.3 there is an error about 'logo'.    
  
Where is the variable originally defined?    
  
The modification in this PR seems to solve the problem.

---

## Comment 1

> Username: mloskot  
> Created_at: 2024-12-09 22:55:12 UTC  
> Url: https://github.com/boostorg/gil/pull/759#issuecomment-2529710554  

> Where is the variable originally defined?  
  
Possibly a bug!  
  
Thank you!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-12-09 23:02:40 UTC  
> Url: https://github.com/boostorg/gil/pull/759#issuecomment-2529721512  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/759?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 82.46%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2f470fb`)](https://app.codecov.io/gh/boostorg/gil/commit/2f470fb5697a22b9559f22ce3c33f23edbdcd999?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 14 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #759      +/-   ##  
===========================================  
+ Coverage    82.12%   82.46%   +0.34%       
===========================================  
  Files          117      117                
  Lines         5355     5727     +372       
===========================================  
+ Hits          4398     4723     +325       
- Misses         957     1004      +47       
```  
  
</details>

---

## Comment 3

> Username: sdarwin  
> Created_at: 2024-12-10 12:08:43 UTC  
> Url: https://github.com/boostorg/gil/pull/759#issuecomment-2531459126  

Thanks. RemovedInSphinx90Warning warnings (not errors) also appeared. I have not researched them.  
  
writing output... [  2%] design/basics  
/opt/venvboostdocs/lib/python3.12/site-packages/sphinx/builders/html/__init__.py:1076: RemovedInSphinx90Warning: The str interface for _JavaScript objects is deprecated. Use js.filename instead.  
  if resource and '://' in otheruri:  
/opt/venvboostdocs/lib/python3.12/site-packages/sphinx/util/osutil.py:48: RemovedInSphinx90Warning: The str interface for _JavaScript objects is deprecated. Use js.filename instead.  
  if to.startswith(SEP):  
/opt/venvboostdocs/lib/python3.12/site-packages/sphinx/util/osutil.py:51: RemovedInSphinx90Warning: The str interface for _JavaScript objects is deprecated. Use js.filename instead.  
  t2 = to.split('#')[0].split(SEP)

---
