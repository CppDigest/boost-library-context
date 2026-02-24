# #118 - Stop auto registring of archives [Open]

> Username: rahulagarwal33  
> Created at: 2018-09-18 07:22:23 UTC  
> Updated at: 2018-10-04 08:56:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/118  

When some one tries to create an archive class derived from the existing classes like xml_iarchive_impl etc.. the corresponding concrete class eg xml_iarchive automatically registered through BOOST_SERIALIZATION_REGISTER_ARCHIVE macro which should be an unwanted behavior because if the derived class has some aditional members then it wont be found in other archive classes.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-03 19:45:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/118#issuecomment-426774045  

would need and example here

---

## Comment 2

> Username: rahulagarwal33  
> Created at: 2018-10-04 08:51:32 UTC  
> Updated at: 2018-10-04 08:56:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/118#issuecomment-426937866  

Hello Robert,  
the example file is attached  
[testarchive.txt](https://github.com/boostorg/serialization/files/2445607/testarchive.txt)  
  
Please have a look at A::serialize function for the issue  
  
Thanks
