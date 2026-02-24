# #253 - Suspect file in Boost 1.78.0 [Closed]

> Username: Thunder1982  
> Created at: 2022-02-14 09:30:37 UTC  
> Updated at: 2023-09-28 06:59:19 UTC  
> Closed at: 2022-02-15 11:03:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/253  

Hello,  
  
during build of boost 1.78.0 and repackaging the artefact as rpm, we've noticed that a strange file is part of the release:  
`$INSTDIR/include/boost/serialization/collection_size_type copy.hpp`  
This file leads to errors during rpm build, since the file name contains a whitespace.  
The file seems to be the copy of `collection_size_type.hpp`.  
  
Is this file important or could it be deleted?  
  
Thanks

---

## Comment 1

> Username: robertramey  
> Created at: 2022-02-14 13:31:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1039091455  

This shouldn't be in there.  I believe it has been removed from the release.  You can remove it. I'll check that it is no longer in there.

---

## Comment 2

> Username: Thunder1982  
> Created at: 2022-02-15 11:03:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1040138952  

Thank you for clarification.

---

## Comment 3

> Username: matthew-dews  
> Created at: 2023-05-11 17:51:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1544436343  

This file is still present in the 1.82 release.  
  
Comparing `collection_size_type copy.hpp` and `collection_size_type.hpp` it looks like the copy is newer based on the copyright, but it's not 100% clear to me which is the right one to keep. Should the copy still be the one that is deleted, or should its contents be moved to the `collection_size_type.hpp`?

---

## Comment 4

> Username: robertramey  
> Created at: 2023-05-11 17:56:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1544442466  

I'm removing collection_size_type copy.hpp .  It is not uncommon for me when I edit a file to make it "better" I make a copy of the original.  This time I dropped the ball.  just eliminate the copy version.  For some reason, I always find git more complex than I expect and it's not uncommon for me to make errors in this area.  Hopefully, I'll be able to check in soon a version which incorporates all overdue adjustments and updates.  Thanks for your patience and help with this.

---

## Comment 5

> Username: matthew-dews  
> Created at: 2023-05-11 18:00:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1544447717  

It happens, thanks for clarifying!

---

## Comment 6

> Username: vissarion  
> Created at: 2023-09-28 06:59:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/253#issuecomment-1738588117  

The file `collection_size_type copy.hpp` is still there in 1.83 release. I propose to open this issue and close it once we verify that the file is no longer there.
