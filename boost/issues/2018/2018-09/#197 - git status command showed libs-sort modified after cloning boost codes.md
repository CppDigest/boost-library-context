# #197 - git status command showed libs/sort modified after cloning boost codes [Closed]

> Username: liang3zy22  
> Created at: 2018-09-30 09:33:02 UTC  
> Updated at: 2018-10-02 01:31:56 UTC  
> Closed at: 2018-10-02 01:31:56 UTC  
> Url: https://github.com/boostorg/boost/issues/197  

Hi all,  
  
I followed the wiki to set up git and clone codes. After the command finished, then I run "git status" command. I got following content:  
```  
On branch master  
Your branch is up-to-date with 'origin/master'.  
Changes not staged for commit:  
  (use "git add <file>..." to update what will be committed)  
  (use "git checkout -- <file>..." to discard changes in working directory)  
  (commit or discard the untracked or modified content in submodules)  
  
	modified:   libs/sort (modified content)  
  
no changes added to commit (use "git add" and/or "git commit -a")  
  
```  
  
The detailed change is as below:  
  
```  
warning: CRLF will be replaced by LF in doc/sort.idx.  
The file will have its original line endings in your working directory.  
diff --git a/doc/sort.idx b/doc/sort.idx  
index 4492743..2453517 100644  
--- a/doc/sort.idx  
+++ b/doc/sort.idx  
@@ -1,37 +1,37 @@  
-# sort.idx for Boost.Sort/Spreadsort Quickbook, Doxygen and Auto-index.  
-  
-# Note needs more customization to be useful in practice! TODO?  
-  
-#!debug "\<\w*\>"  
-  
-# Sort Header files.  
-#!scan-path "/boost/sort/" ".*\.*pp" true  
-!scan-path "../include/boost/sort" ".*\.hpp" false  
-!scan-path "../include/boost/sort/spreadsort" ".*\.hpp" false  
-  
-# All example source files, assuming no sub-folders.  
-# sort example files.  
-!scan-path "../example" ".*\.cpp" true  
-  
-# Allow alternative spellings colour | color, and plurals etc.  
-color  \<\w*(colour|color)\w*\>  
-container \<contain\w*\>  
-data  
-deque \<deque\w*\>  
-example \<example\w*\>  
-font  \<font\w*\>  
warning: CRLF will be replaced by LF in doc/sort.idx.  
The file will have its original line endings in your working directory.  
diff --git a/doc/sort.idx b/doc/sort.idx  
index 4492743..2453517 100644  
--- a/doc/sort.idx  
+++ b/doc/sort.idx  
@@ -1,37 +1,37 @@  
-# sort.idx for Boost.Sort/Spreadsort Quickbook, Doxygen and Auto-index.  
-  
-# Note needs more customization to be useful in practice! TODO?  
-  
-#!debug "\<\w*\>"  
-  
-# Sort Header files.  
-#!scan-path "/boost/sort/" ".*\.*pp" true  
-!scan-path "../include/boost/sort" ".*\.hpp" false  
-!scan-path "../include/boost/sort/spreadsort" ".*\.hpp" false  
-  
-# All example source files, assuming no sub-folders.  
-# sort example files.  
-!scan-path "../example" ".*\.cpp" true  
-  
-# Allow alternative spellings colour | color, and plurals etc.  
-color  \<\w*(colour|color)\w*\>  
-container \<contain\w*\>  
-data  
-deque \<deque\w*\>  
-example \<example\w*\>  
-font  \<font\w*\>  
-greek  
-quartile \<quartile\w*\>  
-histogram  
-ioflags  
-origin  
-outlier  
-maximum \<max\w*\>  
-minimum \<min\w*\>  
-precision  
-range  \<\w*\range\w*\>  
-scaling \<\w*\scal\w*\>  
-tick \<\w*\tick\w*\>  
-title  
-Unicode \<unicode\w*\>  
-vector \<\w*\vector\w*\>  
+# sort.idx for Boost.Sort/Spreadsort Quickbook, Doxygen and Auto-index.  
+  
+# Note needs more customization to be useful in practice! TODO?  
+  
+#!debug "\<\w*\>"  
+  
+# Sort Header files.  
+#!scan-path "/boost/sort/" ".*\.*pp" true  
+!scan-path "../include/boost/sort" ".*\.hpp" false  
+!scan-path "../include/boost/sort/spreadsort" ".*\.hpp" false  
+  
+# All example source files, assuming no sub-folders.  
+# sort example files.  
+!scan-path "../example" ".*\.cpp" true  
  
```  
  
Did I missed some steps?  
  
Thanks  
  
Moon

---

## Comment 1

> Username: liang3zy22  
> Created at: 2018-10-02 01:31:56 UTC  
> Url: https://github.com/boostorg/boost/issues/197#issuecomment-426117046  

Just found that same issue was reported in sort submodule. So close this issue.  
Sorry for any inconvenience.  
  
Moon
