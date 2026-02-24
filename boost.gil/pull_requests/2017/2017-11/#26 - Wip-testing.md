# #26 Wip/testing [Closed]

> Username: stefanseefeld  
> Created at: 2017-11-11 23:31:21 UTC  
> Updated at: 2017-11-18 07:20:30 UTC  
> Closed at: 2017-11-18 07:20:02 UTC  
> Url: https://github.com/boostorg/gil/pull/26  

Refactor GIL tests to no longer require boost::test.  
Tests are split up (again) into separate executables, which has a number of benefits, such as improved compile time, easier maintenance, etc.  
(I'm also adding support for faber, to allow for stand-alone testing, i.e. without requiring the full boost meta-repo to be checked out.)  
  
(Note: This is work in progress. No need to review or merge right away. Next step is to refactor the IO tests.)

---
