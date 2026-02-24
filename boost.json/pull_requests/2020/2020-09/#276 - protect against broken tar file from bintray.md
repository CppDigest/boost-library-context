# #276 protect against broken tar file from bintray [Closed]

> Username: pauldreik  
> Created at: 2020-09-05 14:27:13 UTC  
> Updated at: 2020-09-05 14:28:37 UTC  
> Closed at: 2020-09-05 14:28:37 UTC  
> Url: https://github.com/boostorg/json/pull/276  

The tar file on bintray is broken for some reason, perhaps due to an interrupted github action.  
  
This tries to make the fuzzing script more robust against that, so the corpus is rebuilt again.  
  
 - create the tar file under a temporary name locally, before moving it to the proper name  
 - in case the tar file is bad when unpacked, proceed anyway

---
