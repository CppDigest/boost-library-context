# #220 - Conan recipe. [Closed]

> Username: grafikrobot  
> Created at: 2017-07-27 20:41:07 UTC  
> Updated at: 2021-06-26 04:11:38 UTC  
> Closed at: 2021-06-26 04:11:38 UTC  
> Url: https://github.com/boostorg/build/issues/220  

It would beneficial to implement a conan recipe so that other conan scripts can depend on it and not have to duplicate the logic of building b2.

---

## Comment 1

> Username: solvingj  
> Created at: 2017-07-27 20:49:18 UTC  
> Url: https://github.com/boostorg/build/issues/220#issuecomment-318482260  

Here's some initial information that might be helpful:   
  
Lasote's boost recipe probably has most (all?) the logic necessary for bootstrapping b2, it might just need to be extracted to a separate recipe.   
  
Here's a link to his build.py which handles some things:   
https://github.com/lasote/conan-boost/blob/release/1.64.0/build.py  
  
The bootstrapping of b2 appears to start on line 130 here:   
https://github.com/lasote/conan-boost/blob/release/1.64.0/conanfile.py
