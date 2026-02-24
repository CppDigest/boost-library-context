# #486 - rpm spec [Closed]

> Username: ciez  
> Created at: 2021-03-03 16:44:11 UTC  
> Updated at: 2021-03-04 09:27:22 UTC  
> Closed at: 2021-03-04 09:27:22 UTC  
> Url: https://github.com/boostorg/boost/issues/486  

### Some background  
  
rpm packages are built in a sandboxed directory e.g.  
~/rpmbuild/BUILD/boost_1_75_0  
  
and dry run the install in e.g.  
~/rpmbuild/BUILDROOT/boost-1.75.0-1.el7_9.x86_64/usr/local  
  
Files are installed in the prefix directory when the rpm package is installed.  
  
### Problem  
`./b2 --prefix=/usr/local --stagedir=%buildroot stage`  
in the %install section  
fails the packaging process with  
  
> Found '~/rpmbuild/BUILDROOT/boost-1.75.0-1.el7_9.x86_64' in installed files; aborting  
  
### Questions  
  
1. can `b2 stage|install`  
attempt to install files to a relative path such as   
`~/rpmbuild/BUILDROOT/boost-1.75.0-1.el7_9.x86_64/usr/local`  
without failing?  
  
2. if not, can you recommend a workaround - perhaps a script which copies all the necessary files to an arbitrary relative path such as   
`~/rpmbuild/BUILDROOT/boost-1.75.0-1.el7_9.x86_64/usr/local`?  
  
Cheers

---

## Comment 1

> Username: ciez  
> Created at: 2021-03-04 09:27:22 UTC  
> Url: https://github.com/boostorg/boost/issues/486#issuecomment-790468283  

found an [example spec](https://src.fedoraproject.org/rpms/boost169/blob/epel7/f/boost169.spec)  
  
will take it from there
