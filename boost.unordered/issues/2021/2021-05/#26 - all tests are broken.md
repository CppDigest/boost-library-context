# #26 - all tests are broken [Closed]

> Username: tobias-loew  
> Created at: 2021-05-05 21:41:20 UTC  
> Updated at: 2022-01-05 22:56:49 UTC  
> Closed at: 2022-01-05 22:56:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/26  

Currently all the tests are broken. To be clear: it's not the the library code that's broken, but the tests itself. There are multiples issues:  
- somehow the downloaded boost-snapshot doesn't get unpacked (python complains that the downloaded file is not a bz2 file)  
- appveyor tries to invoke msvc 10 and 11, which seem to be no longer present ('cl' is not a recognized command)  
- travis fails when trying to install 'cpp-coveralls' with pip

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-09-11 14:15:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/26#issuecomment-917414788  

Hi tobias!  
  
Recently, the project was updated to use GitHub Actions and it seems like AppVeyor is now cleaned up as well.  
  
Does this resolve the issue or is there anything else we should be aware of?

---

## Comment 2

> Username: tobias-loew  
> Created at: 2021-09-12 07:07:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/26#issuecomment-917572595  

Hi Leonine,  
Thanks for fixing the appveyor tests, but please also have a look at my pr #27 where I used the new boost appveyor script and also implemented tests with GitHub actions and drone.  
This work was done in collaboration with @Flamefire and @sdarwin  
-- Tobias
