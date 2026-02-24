# #78 - Modular Boost C++ Libraries Request [Closed]

> Username: grafikrobot  
> Created at: 2024-03-09 19:32:12 UTC  
> Updated at: 2024-03-17 14:49:20 UTC  
> Closed at: 2024-03-17 14:49:20 UTC  
> Url: https://github.com/boostorg/mpl/issues/78  

We are in the process of making B2 build changes to all of the B2 build files  
to support "modular" consumption of the Boost Libraries by users. See this list  
post for some details: https://lists.boost.org/Archives/boost/2024/01/255704.php  
  
The process requires making a variety of changes to make each Boost library  
independent of the super-project structure. But the changes do not remove the  
super-project structure or the comprehensive Boost release. The changes make  
solely make it possible, optionally, for users, like package manages, to easily  
consume libraries individually.  
  
Generally the changes include:  
  
* Adding a libroot/build.jam.  
* Porting any functionality from libroot/jamfile to libroot/build.jam.  
* Moving boost-install declaration from libroot/build/jamfile is applicable.  
* Adjusting other B2 build files in the library, like test/jamfile, as needed.  
* Possible changes to C++ source files to remove includes relative to the  
  super-project boostroot location.  
  
Some examples of such changes:  
  
* https://github.com/boostorg/interval/pull/38  
* https://github.com/boostorg/ublas/pull/189  
* https://github.com/boostorg/local_function/pull/10  
* https://github.com/boostorg/preprocessor/pull/57  
* https://github.com/boostorg/callable_traits/pull/194  
* https://github.com/boostorg/compatibility/pull/4  
* https://github.com/boostorg/hof/pull/228  
* https://github.com/boostorg/preprocessor/pull/56  
  
We are asking how you would like us to handle the changes. We would prefer if  
you allow the owners of the Boost.org GitHub project to make changes to B2  
build files, as needed, to accomplish the changes. But understand  
that you may want to manage the proposed changes yourself.  
  
We previously sent emails to all known maintainers to fill out a form with their  
preference. We are contacting you in this issue as we have not gotten a response  
to that email. You can see the ongoing responses for that form and the responses  
to these issues here https://github.com/users/grafikrobot/projects/1/views/6  
  
We are now asking if you can reply directly to this issue to indicate your  
preference of handling the changes. Please supply a response to this question  
and close the issue (so that we can verify you are a maintainer).  
  
How would you like the build changes to be processed?  
  
1. Pull request, reviewed and merged by a BOOSTORG OWNER.  
2. Pull request, reviewed and merged by YOU.  
3. Other. (please specify details in the reply)  
  
Also please indicate any special instructions you want us to consider. Or other  
information you want us to be aware of.  
  
Thanks you, René

---

## Comment 1

> Username: mclow  
> Created at: 2024-03-15 03:08:31 UTC  
> Url: https://github.com/boostorg/mpl/issues/78#issuecomment-1998860366  

1.Pull request, reviewed and merged by a BOOSTORG OWNER.
