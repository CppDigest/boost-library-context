# #628 Ensure all examples build without error [Merged]

> Username: yogsothoth  
> Created at: 2021-10-29 20:49:05 UTC  
> Updated at: 2021-11-10 17:21:34 UTC  
> Merged at: 2021-11-10 17:21:02 UTC  
> Closed at: 2021-11-10 17:21:02 UTC  
> Url: https://github.com/boostorg/gil/pull/628  

### Description  
Add all examples to example/Jamfile  
Add necessary 'using' for libpng  
Bring small fixes to examples' source files (remove unused variables)  
  
Environment:  
- clang 11.0.1  
- FreeBSD 13.0-RELEASE-p4  
- x86_64  
  
### References  
See [#436](https://github.com/boostorg/gil/issues/436)  
  
### Tasklist  
- [X] Add examples' source files to example/Jamfile  
- [X] Fix examples so they compile without warning using default toolset  
- [X] Add documentation to each example as comments in source files  
- [X] Add license text in each .cpp and .hpp in example/  
- [x] Remove documentation of examples from Readme.md   
- [x] Add Readme.md files for each example, indicating any specifics and requirements  
- [X] Add test case(s) (no new test case)  
- [ ] Ensure all CI builds pass  
- [X] Clean up example/Jamfile once CI builds pass (remove unnecessary libtiff)  
- [ ] Review and approve

---

## Comment 1

> Username: yogsothoth  
> Created_at: 2021-10-29 20:54:54 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-955042341  

I have a few questions:  
- Should we update the documentation to reference the missing examples?  
- Should we leave only the compilation targets (not the exe) in the final Jamfile?  
- I've had issues with linking libpng that I'm not sure how to fix. It's likely my own lack of knowledge here showing: I could not get b2 to pass `-lpng` to the compilation command, only `-ljpeg` would show. I ended up forcing it on the command line for the sake of checking the build with `linkflags=-lpng`. Could someone point me to some docs or examples to help me with this?

---

## Comment 2

> Username: mloskot  
> Created_at: 2021-10-30 08:58:29 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-955176400  

Hi, first thanks for your contribution, it's helpful.  
  
> Should we update the documentation to reference the missing examples?  
  
I don't think we should care about describing the examples in the documentation.  
In fact, I'd clear the examples from the documentation.  
Instead, examples should be described in comment block at the top of `.cpp` files (i.e. what an example is about, what feature it presents).  
There also should be `README.md` files which describe any particularities about the examples (i.e. build requirements, specific data required, etc.).  
Then, the documentation should only link to the documentation folder (i.e. on GitHub).  
  
> Should we leave only the compilation targets (not the exe) in the final Jamfile?  
  
I'm not sure I understand.   
We certainly want to let users to build (compile+link) examples using the `b2` and the `Jamfile`-s we provide.  
  
> I've had issues with linking libpng that I'm not sure how to fix.   
  
You'd have to be more specific what you are doing, what is the output, errors, etc.  
Feel free to open new issue for that problem.  
  
Here is (new home of) B2 with its documentation:  
https://www.bfgroup.xyz/b2/manual/main/index.html

---

## Comment 3

> Username: yogsothoth  
> Created_at: 2021-11-01 08:19:10 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-956027494  

> I don't think we should care about describing the examples in the documentation. In fact, I'd clear the examples from the documentation. Instead, examples should be described in comment block at the top of `.cpp` files (i.e. what an example is about, what feature it presents). There also should be `README.md` files which describe any particularities about the examples (i.e. build requirements, specific data required, etc.). Then, the documentation should only link to the documentation folder (i.e. on GitHub).  
  
Alright, I will include that in the tasklist then.  
  
> I'm not sure I understand. We certainly want to let users to build (compile+link) examples using the `b2` and the `Jamfile`-s we provide.  
  
Apologies, I wasn't being super clear: should the default target in the top Jamfile also include producing the executable files for the examples now, or do we leave it as it is today (i.e. `build-project test`)?  
  
> > I've had issues with linking libpng that I'm not sure how to fix.  
>   
> You'd have to be more specific what you are doing, what is the output, errors, etc. Feel free to open new issue for that problem.  
  
Indeed, I just didn't want to turn this thread into a support ticket. I will open a separate ticket.  
  
> Here is (new home of) B2 with its documentation: https://www.bfgroup.xyz/b2/manual/main/index.html  
  
I read it a few times and couldn't find the section that would help me with my problem so far... I'm clearly missing something.

---

## Comment 4

> Username: yogsothoth  
> Created_at: 2021-11-02 09:58:40 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-957284153  

Regarding the issue with linking: it was a problem with the project-cache.jam not being updated. Deleting this file and rebuilding fixed it.

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-11-02 18:35:19 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-958025679  

Yes, the GIL's top-level Jamfile can also build the examples.  
  
AFAICT, all Boost's CI services do `b2 libs/<name>/test` so they won't spend any (unnecessary) time on building examples.

---

## Comment 6

> Username: yogsothoth  
> Created_at: 2021-11-05 16:56:32 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-962058553  

I just updated the PR with some explanations for the examples.  
I also remarked that some of them don't include any mention of any license. Shall I take the opportunity to ensure they all do?

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-11-05 17:47:30 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-962094268  

Good catch, yes, all source files should have the licence/copyright header comment, like this:  
  
https://github.com/boostorg/gil/blob/537f8ae82313167bf5696b5daec265eb3bf6e8c2/example/dynamic_image.cpp#L1-L7

---

## Review 8 [Changes requested]

> Username: mloskot  
> Created_at: 2021-11-08 18:32:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/628#pullrequestreview-800468860  

📁 example/Jamfile

```diff
  59 |+     	    /libpng//libpng
  60 |+ 	]
  61 |+ 	;
```

> Username: mloskot  
> Created_at: 2021-11-08 18:27:11 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744986822  

I'd suggest to keep some level (typically, 4 spaces) of indentation of body of the `for` loop.


📁 example/convolve2d.cpp

```diff
   1 |+ // 
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2021-11-08 18:28:49 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744987940  

This should read   
  
```  
Copyright 2019 Miral Shah <miralshah2211@gmail.com>  
```  
  
it is has been created by @miralshah365


📁 example/hessian.cpp

```diff
   1 |+ // 
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2021-11-08 18:29:39 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744988539  

This should read  
  
```  
Copyright 2019 Olzhas Zhumabek <anonymous.from.applecity@gmail.com>  
```  
  
as it was created by @simmplecoder


📁 example/histogram_equalization.cpp

```diff
   1 |+ // 
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2021-11-08 18:30:28 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744989148  

This should read  
  
```  
Copyright 2020 Debabrata Mandal <mandaldebabrata123@gmail.com>  
```  
  
as it was created by @codejaeger


📁 example/hvstack.hpp

```diff
   1 |+ // 
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2021-11-08 18:32:03 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744990290  

This should read  
  
```  
// Copyright 2021 Olzhas Zhumabek <anonymous.from.applecity@gmail.com>  
```  
  
as it was created by @simmplecoder


📁 example/sobel_scharr.cpp

```diff
   1 |+ // 
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2021-11-08 18:32:45 UTC  
> Updated_at: 2021-11-08 18:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/628#discussion_r744990771  

This should read  
  
```  
// Copyright 2019 Olzhas Zhumabek <anonymous.from.applecity@gmail.com>  
```  
  
as it was created by @simmplecoder


---

## Comment 9

> Username: yogsothoth  
> Created_at: 2021-11-09 12:43:33 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-964117251  

Thank you for your remarks!  
  
I've updated the attributions and the Jamfile indent.  
I have prepared Readme files with a synopsis, and some indications of what the examples require, in a different commit; I will update the PR.

---

## Comment 10

> Username: yogsothoth  
> Created_at: 2021-11-10 09:04:00 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-964918168  

I noticed example/convolve2d.cpp was a bit off compared to the other examples: one output file had a `png` extension but a `jpeg_tag{}`, it ended with a `cin()`, contained redundant, commented out code, etc.  
  
I cleaned it up.  
  
This is the last of the changes I can see we can bring here: if you find all is well, then I think we can add the target `example` to the root Jamfile.

---

## Review 11 [Approved]

> Username: mloskot  
> Created_at: 2021-11-10 15:02:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/628#pullrequestreview-802709551  

Great work! It all looks good to me. Thanks!

---

## Comment 12

> Username: yogsothoth  
> Created_at: 2021-11-10 16:25:47 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-965511166  

Thank you for your time!  
  
I just added the target `example` to the root Jamfile; marking this as Ready for review.

---

## Comment 13

> Username: mloskot  
> Created_at: 2021-11-10 17:21:34 UTC  
> Url: https://github.com/boostorg/gil/pull/628#issuecomment-965565992  

Thank you for your contribution

---
