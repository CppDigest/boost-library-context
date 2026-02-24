# #73 Added VMD library [Closed]

> Username: eldiener  
> Created at: 2015-04-01 04:11:32 UTC  
> Updated at: 2015-04-03 20:23:36 UTC  
> Closed at: 2015-04-03 20:23:36 UTC  
> Url: https://github.com/boostorg/boost/pull/73  



---

## Comment 1

> Username: eldiener  
> Created_at: 2015-04-02 23:45:33 UTC  
> Url: https://github.com/boostorg/boost/pull/73#issuecomment-89081557  

Since I made a pull request to add the VMD library to boost on the 'develop' branch and supposedly the Travis CI build succeeded there it makes little sense to me that adding the VMD library on the 'master' branch causes Travis CI to fail. Is there any indication about why that has happened ?

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-04-03 08:30:22 UTC  
> Url: https://github.com/boostorg/boost/pull/73#issuecomment-89219088  

If you click on 'Details' to the right of the failure message you can see the error. Looks like the build isn't correctly set up, so it's probably nothing to do with you.  
  
But you shouldn't be creating a pull request for master until everything is fine on develop (tests. documentation, etc.). Same as we used to do on subversion.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-04-03 20:20:16 UTC  
> Url: https://github.com/boostorg/boost/pull/73#issuecomment-89410067  

I can close this pull request until everything is fine on develop. However isn't this going to lead to the same situation of which I complained regarding the 'sync' library, where there was only a 'develop' branch and anyone wishing to do a 'git submodule foreach --recursive git checkout master' would be stopped by a lack of a 'master' branch for VMD ?

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-04-03 20:23:32 UTC  
> Url: https://github.com/boostorg/boost/pull/73#issuecomment-89410507  

I see my mistake and will close this pull request.

---
