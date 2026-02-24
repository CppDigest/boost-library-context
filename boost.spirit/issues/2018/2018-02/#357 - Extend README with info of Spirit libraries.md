# #357 - Extend README with info of Spirit libraries [Closed]

> Username: eyalroz  
> Created at: 2018-02-08 13:09:05 UTC  
> Updated at: 2018-02-19 22:23:10 UTC  
> Closed at: 2018-02-19 22:23:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/357  

The Boost documentation for spirit (e.g. in Boost v1.66) gives the version as 2.5.x ; yet the master branch has a lot of files with x3 in their names. On the other hand, there are branches whose names begin with x3, suggesting it might not "really" be part of the main or development branch. If that's not all, there isn't a clear indication on other sites where one is supposed to find the X3 code.  
  
I believe README.md should clarify this point.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-02-15 16:39:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-365985382  

@djowel can I delete `x3-devel` and `x3-scalability` branches? They does not have any commit that is not in the master branch.

---

## Comment 2

> Username: djowel  
> Created at: 2018-02-15 21:18:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-366064688  

> @djowel can I delete x3-devel and x3-scalability branches? They does not have any commit that is not in the master branch.  
  
Sure! Go ahead.

---

## Comment 3

> Username: eyalroz  
> Created at: 2018-02-18 10:30:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-366506442  

@Kojoley : So, closing the branches with X3 in their names reduces the confusion a bit, but the basic problem still remains:  
  
* The boost docs linked to from the repo page here on GitHub say the current version is 2.5.4  
* The main branch has some folders named X3  
* README.md doesn't say anything about v2 vs v3.  
  
Please consider adding a paragraph explaining the coexistence for X3 and non-X3 code.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-02-18 11:46:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-366510238  

1. I do not know how often docs are rebuild and redeployed, I think every day, so just wait a little bit and they will appear at http://www.boost.org/doc/libs/develop/libs/spirit/doc/html/index.html.  
2. I do not know what you are wanting to say. All 3 version of Spirit are existing in Boost side by side and can be used. None of them are deprecated and planned for removal in near future. Boost libraries are reviewed before appearing in the release, so until something is not explicitly said to be experimental it is stable.  
3. And about v1 too, because it states `Spirit is a set of C++ libraries for parsing and output generation`. Most of people will not look for `README.md` of particular library in the Boost release distribution and it is not included in nix distros. Please consider opening a PR.

---

## Comment 5

> Username: eyalroz  
> Created at: 2018-02-18 12:15:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-366511809  

Let me try and explain. Think about this from the perspective of two users: One user who knows nothing about X3, and has just heard that "Spirit is a set of C++ libraries for parsing and output generation". The other has also heard that there might be some newer version of Spirit codenamed X3.  
  
The first user arrives either at the official documentation on the Boost site, or arrives here on the GitHub repo (where s/he's essentially just forwarded there). This user will only know about Spirit 2.x since nobody tells him/her there might be another option.  
  
The second user - if they see the official docs, they think "hmm, maybe X3 is only in development, or it's slated for a later release; let's check out the GitHub repo". Then they get here, and can still not understand whether they can, and whether they should, use X3.

---

## Comment 6

> Username: Kojoley  
> Created at: 2018-02-18 16:53:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/357#issuecomment-366529512  

No offence, but your imaginary user will give up on Spirit after a first compiler error message.  
If you have something to add and not satisfied with #362, please propose changes by writing a patch (open a PR).
