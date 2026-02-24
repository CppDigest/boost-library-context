# #118 - Possible NPE caused by incorrect pointer check [Closed]

> Username: LostVector  
> Created at: 2020-01-28 02:06:17 UTC  
> Updated at: 2020-11-30 20:03:20 UTC  
> Closed at: 2020-11-30 20:03:19 UTC  
> Url: https://github.com/boostorg/process/issues/118  

My static analyzer is complaining about a possible null pointer exception at https://github.com/boostorg/process/blame/develop/include/boost/process/detail/posix/executor.hpp#L283.  
  
This code was added in commit https://github.com/boostorg/process/commit/1b476b0430d6cb30a4b1027074f15d658153de2e.  
  
I believe this NPE is caused by the if statement at https://github.com/boostorg/process/blame/develop/include/boost/process/detail/posix/executor.hpp#L280.  It seems like it should be checking against *e, not e.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-05-21 11:17:54 UTC  
> Url: https://github.com/boostorg/process/issues/118#issuecomment-632030222  

I think this has been addresses by 1b476b0430d6cb30a4b1027074f15d658153de2e, still testing though.

---

## Comment 2

> Username: LostVector  
> Created at: 2020-05-21 20:37:02 UTC  
> Updated at: 2020-05-21 20:37:56 UTC  
> Url: https://github.com/boostorg/process/issues/118#issuecomment-632332070  

Don't think this fixes it, the code is just moved but not changed.  
  
Again, the line in question should be checking against *e, not e.  e itself will never be a nullptr because it's a pointer into the environment string, and the clear intent here is to only enter the conditional if we didn't reach the end of the environment string in the previous loop incrementing e.

---

## Comment 3

> Username: LostVector  
> Created at: 2020-11-30 20:03:19 UTC  
> Url: https://github.com/boostorg/process/issues/118#issuecomment-736011484  

This seems to have been addressed by https://github.com/boostorg/process/commit/2f32c95341ea1ff134ce8d1180ef46ef309aefeb.  Presumably others ran into this issue and someone fixed it. Closing.
