# #5 Specify version at the command line. [Merged]

> Username: danieljames  
> Created at: 2016-07-02 21:31:03 UTC  
> Updated at: 2016-07-09 11:53:04 UTC  
> Merged at: 2016-07-09 11:53:04 UTC  
> Closed at: 2016-07-09 11:53:04 UTC  
> Url: https://github.com/boostorg/inspect/pull/5  

I'm going to start running inspect again using a snapshot downloaded from bintray. This means it won't have any version control info available in the repo, so I can't replace the old subversion support with git support. But my script will know the version hash, so it can pass a string at the command line.  
  
This replaces the old CVS/subversion support with the new command line option. I've also changed the command line option processing so flags such as `-text` and `-brief` don't have to appear at the beginning, which makes it a little more usable.  
  
I'll merge this myself, just creating a pull request so that anyone who's interested can have a chance to comment beforehand.

---
