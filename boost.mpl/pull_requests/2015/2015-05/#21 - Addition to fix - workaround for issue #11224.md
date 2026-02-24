# #21 Addition to fix / workaround for issue #11224. [Merged]

> Username: DenizThatMenace  
> Created at: 2015-05-19 10:16:03 UTC  
> Updated at: 2015-05-20 06:33:21 UTC  
> Merged at: 2015-05-20 06:33:21 UTC  
> Closed at: 2015-05-20 06:33:21 UTC  
> Url: https://github.com/boostorg/mpl/pull/21  

- Added default-value for cmdline-option which expects the Boost  
  source-directory. It points to the Boost source-directory in which the  
  script "boost_mpl_preprocess.py" is located.  
- Adjusted "README.txt" file to reflect these changes.  
  
Signed-off-by: Deniz Bahadir dbahadir@benocs.com

---

## Comment 1

> Username: DenizThatMenace  
> Created_at: 2015-05-19 10:18:16 UTC  
> Url: https://github.com/boostorg/mpl/pull/21#issuecomment-103427964  

As you, Edward, suggested in your last comment on pull-request #20, I extended the script "boost_mpl_preprocess.py" to use the Boost directory in which the script resides as the default.  
  
Please have a look at it and feel free to merge. :-)

---
