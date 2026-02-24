# #181 - Request stlab.adobe.com/gil/ removal [Open]

> Username: mloskot  
> Created at: 2018-12-10 00:47:28 UTC  
> Updated at: 2019-08-07 20:03:47 UTC  
> Url: https://github.com/boostorg/gil/issues/181  

The Boost.GIL documentation has been reworked, the video lecture captured (#112) and everything now lives at https://www.boost.org/libs/gil/  
  
We should contact Lubomir or whoever is reachable at Adobe asking to remove the old and outdated GIL docs dangling at http://stlab.adobe.com/gil/. It may be doing a bad publicity job for GIL. By the way, the whole site at http://stlab.adobe.com/gil/ seems dead anyway. The Adove STLab moved to http://stlab.cc/ and there are no traces of GIL.  
  
Perhaps we should ask @sean-parent for help with removal of the old site.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-12-16 23:26:29 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-447686436  

FYI, I've taken the liberty to drop @sean-parent a message using the e-mail address on top of https://github.com/stlab page.

---

## Comment 2

> Username: sean-parent  
> Created at: 2019-05-02 21:50:07 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-488845132  

I have finally managed to get access to the old stlab.adobe.com/gil site. I'm trying to grab an archive of the entire stlab.adobe.com site at the moment. I no longer have the ability to build the web site (it was built with doxygen and a collection of tools/scripts). So the question is, how should it be modified? Just nuke the /gil/ directory (there are other gil references elsewhere on the site and this would cause dead links...) - stick a header of some kind on all the pages pointing to the new site (might be able to do this to the stlab site as well).  
  
Ideas? Preferably ones that are easy to execute - because the site was automatically built there are roughly 10 thousand pages.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-05-02 22:18:14 UTC  
> Updated at: 2019-05-02 22:18:47 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-488852319  

@sean-parent I think simplest yet reasonable would be to redirect directly to GIL on Boost site at https://boost.org/libs/gil  (please, ignore the currently broken redirection to the docs for 1.70):  
  
```  
<?php  
  header('Location: https://boost.org/libs/gil', true, 301);  
  exit();  
?>  
```  
  
Thank you very much for your assistance.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-05-03 11:47:37 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-489069263  

@stefanseefeld What's your opinion on that?

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2019-05-07 11:38:01 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-490043245  

@mloskot I think we should establish http://boostorg.github.io/gil  to be the canonical project homepage.

---

## Comment 6

> Username: mloskot  
> Created at: 2019-05-07 12:55:21 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-490067401  

@stefanseefeld Sure, works for me!  
  
@sean-parent Could you please redirect then to the `http://boostorg.github.io/gil`?

---

## Comment 7

> Username: mloskot  
> Created at: 2019-06-01 21:55:57 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-497980426  

Follow-up to https://github.com/boostorg/gil/issues/181#issuecomment-488852319: GIL docs deployment has been fixed.  @sean-parent If will be working on the redirect, please point to this URL http://boostorg.github.io/gil

---

## Comment 8

> Username: mloskot  
> Created at: 2019-07-05 20:45:00 UTC  
> Url: https://github.com/boostorg/gil/issues/181#issuecomment-508858758  

@sean-parent Excuse me bothering you, but is there any news on that redirect?
