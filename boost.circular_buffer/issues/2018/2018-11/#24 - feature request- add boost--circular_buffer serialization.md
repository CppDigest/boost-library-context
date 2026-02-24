# #24 - feature request: add boost::circular_buffer serialization [Open]

> Username: yuvalif  
> Created at: 2018-11-30 08:45:20 UTC  
> Updated at: 2019-01-31 07:26:26 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/24  

For possible implementation and benchmarking please see [here](https://gist.github.com/yuvalif/aa3691c62959b1a9d7dbf7adceac0c78).

---

## Comment 1

> Username: robertramey  
> Created at: 2018-12-18 02:26:11 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/24#issuecomment-448073407  

In order to get this in, I'd recommend the following.  This library is maintained, but I doubt the maintainer can spare the time for a non-trivial enhancement such as this.  So, if this is important to you, I recommend that you:  
  
a) fork the library  
b) add your implementation to the code  
c) update the documentation  
d) add a test of serialization  
  
create a pull request.  
  
What's in it for you?  
  
a) you get the enhancement to the library that you need.  
b) you get can put your name on the copyright of the serialization code and the test and a note in the documentation.  
c) so you can proudly wear the title of official boost library maintainer - which can look good on your resume,  
d) the eternal gratitude of those who come after you.

---

## Comment 2

> Username: yuvalif  
> Created at: 2018-12-18 06:29:21 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/24#issuecomment-448111640  

@robertramey sounds like a plan :-)

---

## Comment 3

> Username: yuvalif  
> Created at: 2019-01-31 07:26:26 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/24#issuecomment-459243580  

This is implemented in PR #25
