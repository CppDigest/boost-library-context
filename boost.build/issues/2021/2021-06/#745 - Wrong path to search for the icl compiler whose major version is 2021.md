# #745 - Wrong path to search for the icl compiler whose major version is 2021 [Closed]

> Username: Cassielcc  
> Created at: 2021-06-09 06:07:49 UTC  
> Updated at: 2021-11-12 01:57:18 UTC  
> Closed at: 2021-11-12 01:57:17 UTC  
> Url: https://github.com/boostorg/build/issues/745  

Hi ,  
  
I noticed boost-1.76.0 has supported 2021 version of icl compiler, but the path that b2 search for this compiler is wrong. I print the variable $(Setup),  as below picture shows，this path is wrong.  
<img width="707" alt="Capture" src="https://user-images.githubusercontent.com/26761344/121301750-3da60800-c92b-11eb-85eb-1d95484d1d30.PNG">  
  
Actually, this setvars.bat is in the folder " C:\Program Files (x86)\Intel\oneAPI ".

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-11-12 01:57:16 UTC  
> Url: https://github.com/boostorg/build/issues/745#issuecomment-966755907  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
