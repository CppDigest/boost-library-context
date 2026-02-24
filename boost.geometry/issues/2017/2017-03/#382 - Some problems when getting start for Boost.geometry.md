# #382 - Some problems when getting start for Boost.geometry [Closed]

> Username: Rylynnn  
> Created at: 2017-03-05 23:28:14 UTC  
> Updated at: 2017-03-06 00:41:43 UTC  
> Closed at: 2017-03-06 00:37:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/382  

Hi all:  
I'm a student participated in GSoC about Boost.geometry. When I installed Boost.geometry with [wiki](https://github.com/boostorg/geometry/wiki/Contribution-Tutorial) there is something wrong.  
I used the following command lines to install boost, so the Bost.Geometry may installed on my local repository,   
```  
git clone --recursive https://github.com/boostorg/boost.git  
cd boost  
git checkout develop # or whatever branch you want to use  
./bootstrap.sh  
./b2 headers  
```  
But when I run the following,  
```  
cd modular-boost/libs/geometry  
```  
the files can not find, and when I run the following:  
```  
cd libs/geometry  
git checkout develop  
git branch -vv  
git pull  
```  
It run.And after I was added the that Boost directory to the PATH, the following run.  
```  
b2 test  
```  
Do i got something wrong? Or there are some changes in Boost doc?

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-03-05 23:59:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/382#issuecomment-284274583  

Our tutorial seems to be out of sync with https://github.com/boostorg/boost/wiki/Getting-Started. It's shown there how to clone Boost without specifying directory which Boost is cloned into. In this case git uses the name of the repository which is `boost`. Previously `modular-boost` directory was specified explicitly like that:  
  
    git clone --recursive https://github.com/boostorg/boost.git modular-boost  
  
Either way is ok, you may use whatever directory name you like. If the tests run you're doing everything correctly.  
  
Btw, I've seen your emails, I'll respond to one of them tomorrow. ;)

---

## Comment 2

> Username: awulkiew  
> Created at: 2017-03-06 00:37:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/382#issuecomment-284277308  

Tutorial fixed.

---

## Comment 3

> Username: Rylynnn  
> Created at: 2017-03-06 00:41:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/382#issuecomment-284277653  

Thanks a lot for you answer!  
I'll waiting for you respond!  
Have a good day!:)
