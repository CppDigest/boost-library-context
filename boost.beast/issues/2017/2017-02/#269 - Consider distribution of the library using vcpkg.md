# #269 - Consider distribution of the library using vcpkg [Closed]

> Username: jhruby  
> Created at: 2017-02-22 09:07:19 UTC  
> Updated at: 2017-03-27 18:56:16 UTC  
> Closed at: 2017-03-27 18:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/269  

Hi,  
It would be very cool to see your library distributed through vcpkg channel.   
  
Jan

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-22 12:53:27 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281661051  

Thanks for your interest in Beast! I tried going through the steps for vcpkg but it was a bit too complex for me. But if you'd like to package it up (or anyone else who wants to volunteer) I would be very appreciative!

---

## Comment 2

> Username: jhruby  
> Created at: 2017-02-23 11:55:34 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281973035  

I can look into that and package it. I would than need the url for certain stable version of the package. It should not point to master probably.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-23 11:59:46 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281973780  

Hmm... well, Beast is still in beta. There's a little more interface work to do before I can consider calling a 1.0 version. Specifically this: https://github.com/vinniefalco/Beast/issues/154

---

## Comment 4

> Username: jhruby  
> Created at: 2017-02-23 12:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281974197  

Well there are other betas in vcpkg. I don't see it as a problem.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-23 12:06:13 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281975002  

Oh! Okay, well when **b29** passes review I will create a tag for **v1.0.0-b29** and we can use that. Thanks!

---

## Comment 6

> Username: jhruby  
> Created at: 2017-02-23 12:10:21 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-281975776  

Awesome :-)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-03-02 13:13:38 UTC  
> Updated at: 2017-03-02 13:13:57 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-283649989  

I've created a new tag, its called **v1.0.0-b30** if you would like to try setting up the package! If you need to add files to the repository just submit a pull request. Thanks!  
  
The tagged branch:  
https://github.com/vinniefalco/Beast/commits/v1.0.0-b30

---

## Comment 8

> Username: jhruby  
> Created at: 2017-03-07 12:05:16 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-284703540  

Hi, I'll look into that.

---

## Comment 9

> Username: jhruby  
> Created at: 2017-03-13 08:29:15 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-286042939  

I just created a pull request for Microsoft.  
https://github.com/Microsoft/vcpkg/pull/781

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-03-21 20:03:22 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-288201155  

@jhruby You did such a wonderful job on this could you possibly be interested in doing the same for NuDB? Its quite similar to Beast in structure: https://github.com/vinniefalco/NuDB

---

## Comment 11

> Username: jhruby  
> Created at: 2017-03-22 11:25:18 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-288370286  

Sure I can do it :)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-03-22 12:30:35 UTC  
> Updated at: 2017-03-22 12:31:20 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-288384073  

Cool! I pushed a tag for `1.0.0` of the library  
  
Here's the relevant issue, thanks for volunteering  
https://github.com/vinniefalco/NuDB/issues/51

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-03-27 18:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/269#issuecomment-289550513  

This looks like it's done, thanks!
