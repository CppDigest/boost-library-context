# #3 - Wiki documentation page for "working with doc" is missing steps. [Open]

> Username: grantrostig  
> Created at: 2019-04-30 02:16:08 UTC  
> Updated at: 2020-05-19 19:04:00 UTC  
> Url: https://github.com/boostorg/more/issues/3  

Wiki for "working with doc" is missing steps.   
  
Getting Started: Working With Boost Documentation  
https://github.com/boostorg/boost/wiki/Getting-Started:-Working-With-Boost-Documentation  
  
For the Ubuntu specific instructions:  
  
Which directory should be one's "current working directory"?  Can one can presume it  $BOOST_ROOT?  It probably should be stated explicitly.  
  
If one is to use $BOOST_ROOT, then the instruction to:  
>> "Add to user-config.jam:"   
  
is not possible, since there is no such file in $BOOST_ROOT in 1.70.  
  
This would make the less confident user unsure of what to do next, or to doubt the correctness of the instructions, or to doubt the directory one is supposed to be in.  
  
This doubt gets greater when reading the next paragraph about "Pre-build quickbook".  
  
On other hand, if one just created the file, there are no instructions on what command to run next. So a user is completely stuck at that point.  
  
Then it starts talking about building PDFs (without stating that they would nor would not have built in the prior steps, but again doesn't say which user-config.jam file in which directory "bjam pdf" should be run.  
  
I personally don't know the answer to any of the above questions, so I cannot offer any insight on the correct answers.  I can only say that the document has not helped me make any progress at all. My goal was simply to get/find/build documentation for the libraries in PDF format, but the last version I found on the internet is 1.56, but even more unfortunate, it does not include boost.test.  
  
Thanks you to those that created the wiki document, but can you update it so that it makes sense to a new person to boost.  I did end up building boost, but there are no PDFs to be found.
