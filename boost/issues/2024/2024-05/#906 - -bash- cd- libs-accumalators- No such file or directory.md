# #906 - "bash: cd: libs/accumalators: No such file or directory" [Open]

> Username: SwuduSusuwu  
> Created at: 2024-05-19 16:39:42 UTC  
> Updated at: 2024-05-19 16:42:42 UTC  
> Url: https://github.com/boostorg/boost/issues/906  

[https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview](https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview) has:  
```  
git clone git@github.com:boostorg/boost.git # Check out only the super-project (without --recursive)  
cd boost  
git submodule update --init # Check out all the modules  
cd libs/accumalators  
```  
Results:  
```  
Cloning into 'boost'...  
remote: Enumerating objects: 281830, done.  
remote: Counting objects: 100% (677/677), done.  
remote: Compressing objects: 100% (333/333), done.  
remote: Total 281830 (delta 437), reused 578 (delta 344), pack-reused 281153  
Receiving objects: 100% (281830/281830), 64.93 MiB | 352.00 KiB/s, done.  
Resolving deltas: 100% (181123/181123), done.  
Submodule 'accumulators' ([git@github.com](mailto:git@github.com):boostorg/accumulators.git) registered for path 'libs/accumulators'  
Submodule 'algorithm' ([git@github.com](mailto:git@github.com):boostorg/algorithm.git) registered for path 'libs/algorithm'  
Submodule 'align' ([git@github.com](mailto:git@github.com):boostorg/align.git) registered for path 'libs/align'  
Submodule 'any' ([git@github.com](mailto:git@github.com):boostorg/any.git) registered for path 'libs/any'  
Submodule 'array' ([git@github.com](mailto:git@github.com):boostorg/array.git) registered for path 'libs/array'  
Submodule 'asio' ([git@github.com](mailto:git@github.com):boostorg/asio.git) registered for path 'libs/asio'  
...  
Cloning into '/data/data/com.termux/files/home/boost/libs/accumulators'...  
Cloning into '/data/data/com.termux/files/home/boost/libs/algorithm'...  
...  
Submodule path 'libs/accumulators': checked out '9d9e5dae2202660f57e2dc91efb620aa001525b3'  
...  
bash: cd: libs/accumalators: No such file or directory  
~/boost $  
```  
Suggest: `s/accumalators/accumulators/`  
Test was from [Termux](https://play.google.com/store/apps/details?id=com.termux).
