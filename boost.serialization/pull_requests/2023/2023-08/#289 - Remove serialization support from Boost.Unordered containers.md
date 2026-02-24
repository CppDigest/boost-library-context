# #289 Remove serialization support from Boost.Unordered containers [Merged]

> Username: cmazakas  
> Created at: 2023-08-23 15:27:59 UTC  
> Updated at: 2023-10-14 00:32:23 UTC  
> Merged at: 2023-10-13 19:48:02 UTC  
> Closed at: 2023-10-13 19:48:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/289  

Unordered added a slew of new containers and while adding Serialization support for them, we noticed Serialization directly supports `boost::unordered_map` et al.   
  
We'd like to own our own means of serialization and to prevent breaking changes to Serialization and its users, we made this PR to maintain compatibility.  
  
We haven't updated Unordered's develop branch yet because this would break Serialization's CI. You can see the open pull request here:  
https://github.com/boostorg/unordered/pull/207

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-23 19:21:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1690513744  

Hmmm - I don't really understand this yet.  Is the new Boost.Unordered compatible with the current previous one?  If it's not one would need to know what to do about files serialized under the previous version.  Assuming that they are compatible, and you want to take over this functionality, I'd be all for it.  But I need to know that it won't break currently existing archives.  
  
Assuming that the above is satisfied.  The real fix is to remove the files currently in the serialization library which implement and test this functionality.  The current header would contain nothing but a #pragma message pointing the user to the fact that he should make adjustments in his program.  Then responsibility for testing would fall entirely to you - which is fine with me. You could also just re-use the current  headers boost/serialization/hash_set.hpp with your own code update and the tests would continue to run as they do now.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2023-08-23 21:14:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1690648341  

Hey Robert, thanks for taking the time look at the PR!  
  
> Hmmm - I don't really understand this yet. Is the new Boost.Unordered compatible with the current previous one? If it's not one would need to know what to do about files serialized under the previous version. Assuming that they are compatible, and you want to take over this functionality, I'd be all for it. But I need to know that it won't break currently existing archives.  
  
Luckily, Joaquin has you covered here:  
https://github.com/boostorg/unordered/blob/b0195297f096c0477242bce98f4dd6209cfd0a12/test/legacy_archives/generate_legacy_archives.cpp  
  
We have a committed set of legacy archives that we've included in our serialization tests here:  
https://github.com/boostorg/unordered/blob/b0195297f096c0477242bce98f4dd6209cfd0a12/test/unordered/serialization_tests.cpp#L185  
  
> Assuming that the above is satisfied. The real fix is to remove the files currently in the serialization library which implement and test this functionality. The current header would contain nothing but a #pragma message pointing the user to the fact that he should make adjustments in his program. Then responsibility for testing would fall entirely to you - which is fine with me.  
  
Great minds think alike, I was suggesting a pragma message to Joaquin earlier.  
  
What would you like the message to say? Something maybe just like:  
```cpp  
BOOST_PRAGMA_MEESAGE("This header is deprecated, Unordered supports Serialization directly now.");  
```

---

## Comment 3

> Username: cmazakas  
> Created_at: 2023-09-11 21:53:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1714636435  

@robertramey I think this PR should be good to go, Unordered's already merged in its support for Serialization which essentially pollutes your CI with needless test failures.

---

## Comment 4

> Username: robertramey  
> Created_at: 2023-09-12 15:14:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1715920790  

It looks to me that some of the removed files contain code which implements serialization for std hashed/unordered containers.  Am I right?  Are you sure you're not removing something that someone might be using?

---

## Comment 5

> Username: cmazakas  
> Created_at: 2023-09-13 18:29:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1718120743  

> It looks to me that some of the removed files contain code which implements serialization for std hashed/unordered containers. Am I right? Are you sure you're not removing something that someone might be using?  
  
That functionality should live in `include/boost/serialization/unordered_set.hpp`, no?  
  
Here I'm only updating: `include/boost/serialization/boost_unordered_set.hpp`.  
  
Unless I'm missing something obvious here, everything seems okay.

---

## Comment 6

> Username: robertramey  
> Created_at: 2023-09-13 18:52:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1718150548  

> That functionality should live in include/boost/serialization/unordered_set.hpp, no?  
  
That's my question.  Isn't the point of this to totally eliminate boost/serialization/unordered_set.hpp?  does the functionality to serialize std::unordered_set.hpp reside in your library?

---

## Comment 7

> Username: cmazakas  
> Created_at: 2023-09-14 14:29:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/289#issuecomment-1719566907  

> Isn't the point of this to totally eliminate boost/serialization/unordered_set.hpp? does the functionality to serialize std::unordered_set.hpp reside in your library?  
  
It does not. This PR is strictly about Boost.Unordered and _not_ the STL containers.  
  
Sorry, I should've introduced myself better. I'm the maintainer of Boost.Unordered and work alongside Joaquin to develop new containers for the library.  
  
Our goal here is to only remove support for `boost::unordered_*` containers while leaving your support for the STL containers intact.

---
