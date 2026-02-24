# #904 - Troubles with Install Boost.Build [Closed]

> Username: Bolehlaf  
> Created at: 2024-05-15 06:09:12 UTC  
> Updated at: 2024-10-04 14:44:24 UTC  
> Closed at: 2024-10-04 14:44:24 UTC  
> Url: https://github.com/boostorg/boost/issues/904  

Hello,   
we are trying to run this python library: https://pypi.org/project/ur-rtde/ and it listed Boost as its dependency. According to guide, we were supposed to install boost build. We were following the documentation: https://www.boost.org/doc/libs/1_85_0/more/getting_started/windows.html#install-boost-build  
Unfortunately, when we ran step 3: Run b2 install --prefix=our/path, in the result was not /bin folder, so we couldn't add it to PATH environment variables. We deleted this folder and tried to run this command again, but there was even less files and again without /bin folder.  
Do you have any suggestions, what could we do wrong?

---

## Comment 1

> Username: nghia1303  
> Created at: 2024-10-03 02:38:43 UTC  
> Url: https://github.com/boostorg/boost/issues/904#issuecomment-2390382957  

@Bolehlaf I have the same issue, have you found the solution yet? I've struggled for days

---

## Comment 2

> Username: Bolehlaf  
> Created at: 2024-10-04 06:32:28 UTC  
> Url: https://github.com/boostorg/boost/issues/904#issuecomment-2392926043  

@nghia1303 I'm not sure, how we managed to resolve this, but if I remember correctly, out final workaround wast that we installed whole Boost library, not just boost build. Try it that way and if it doesn't work, let me know. I will try to recreate the problem and maybe I will recall our solution 😅

---

## Comment 3

> Username: nghia1303  
> Created at: 2024-10-04 07:17:13 UTC  
> Url: https://github.com/boostorg/boost/issues/904#issuecomment-2392992130  

@Bolehlaf Thanks for reply, I fixed my issue, mine was because of the square brackets in the path leads to boost lib, so b2 cannot process the path correctly. I changed the boost library location and it works.

---

## Comment 4

> Username: mclow  
> Created at: 2024-10-04 14:37:59 UTC  
> Url: https://github.com/boostorg/boost/issues/904#issuecomment-2393865472  

So I should mark this issue as resolved?

---

## Comment 5

> Username: nghia1303  
> Created at: 2024-10-04 14:41:46 UTC  
> Url: https://github.com/boostorg/boost/issues/904#issuecomment-2393873644  

@mclow yeah, i think
