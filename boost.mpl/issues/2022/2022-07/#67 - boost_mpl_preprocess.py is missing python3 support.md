# #67 - boost_mpl_preprocess.py is missing python3 support [Closed]

> Username: thomas955  
> Created at: 2022-07-20 22:35:57 UTC  
> Updated at: 2025-07-03 12:55:02 UTC  
> Closed at: 2025-07-03 12:55:02 UTC  
> Url: https://github.com/boostorg/mpl/issues/67  

This and the depending scripts are not working with Ubuntu 22.04 anymore.  
My default sym-link for `python` at least is pointing to `Python 3.10.4`  
  
Already tested with my fix which I will provide in a PR:  
  
```  
python --version  
Python 3.10.4  
```  
  
I tested my patches with python 2.7.x as well and they seem to work with Ubuntu 20.04, but this is not the python version that we should use anymore.  
Feel free to comment. :)

---

## Comment 1

> Username: thomas955  
> Created at: 2022-07-21 00:24:34 UTC  
> Url: https://github.com/boostorg/mpl/issues/67#issuecomment-1190900246  

@Bagira80 Please have a look on this, too. And a lot of thx for your help.
