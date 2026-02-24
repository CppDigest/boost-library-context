# #12 - Ignore files and folders listed in .gitignore and meta/inspect-ignore [Open]

> Username: HDembinski  
> Created at: 2019-08-22 09:04:06 UTC  
> Updated at: 2019-08-22 09:04:06 UTC  
> Url: https://github.com/boostorg/inspect/issues/12  

The inspect tool gives a lot of false positives when I run it locally on my computer. These would disappear if it would ignore what is in .gitignore.  
  
Once this feature is added, we could generally switch to a solution where the inspect tool scans the meta folder and reads a `inspect-ignore` file. This would have the same format as .gitignore, so the same code in the inspect tool can be used to read both. This would be a better solution than using boost-no-inspect files and "boost-no-inspect" comments.
