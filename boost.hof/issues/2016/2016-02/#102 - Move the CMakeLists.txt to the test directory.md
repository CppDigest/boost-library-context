# #102 - Move the CMakeLists.txt to the test directory [Closed]

> Username: viboes  
> Created at: 2016-02-29 23:33:23 UTC  
> Updated at: 2016-03-06 23:58:45 UTC  
> Closed at: 2016-03-01 17:53:44 UTC  
> Url: https://github.com/boostorg/hof/issues/102  

Edwuard Dienner wrote  
  
> Why is the CMakeLists.txt not in the 'test' subdirectory, since it appears from your explanation that the file is used for the tests ?

---

## Comment 1

> Username: viboes  
> Created at: 2016-02-29 23:47:31 UTC  
> Url: https://github.com/boostorg/hof/issues/102#issuecomment-190453512  

And add one in examples.  
  
Maybe you want to preserve on project root CmakeList.txt including the two directories.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-02-29 23:54:52 UTC  
> Url: https://github.com/boostorg/hof/issues/102#issuecomment-190454833  

Why? The three lines to glob the tests and/or example don't need to be put into a separate cmake file.  
  
> Maybe you want to preserve on project root CmakeList.txt including the two directories.  
  
Of course the top-level cmake is necessary if you want to install the library.

---

## Comment 3

> Username: viboes  
> Created at: 2016-03-01 17:53:44 UTC  
> Url: https://github.com/boostorg/hof/issues/102#issuecomment-190831740  

No problem. I believed that it was better this way, but you are free to do as you consider is the best for you.
