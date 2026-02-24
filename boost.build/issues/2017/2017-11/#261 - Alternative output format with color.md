# #261 - Alternative output format with color [Open]

> Username: jpcima  
> Created at: 2017-11-12 12:07:54 UTC  
> Updated at: 2021-05-29 18:22:34 UTC  
> Url: https://github.com/boostorg/build/issues/261  

Hi. Can we discuss the possibility of improving the format of boost.build's console output?  
I have created my own [patch](https://github.com/jpcima/build/commit/3706e07b53c523deef2e30df986b6ba3bd629856) in order to to make the output of my builds more visually pleasing.  
By default, the build output occupies a lot of screen space and is not easily distinguished from the output of the compiler. I took some screen captures for [comparison](https://gist.github.com/jpcima/1e35747f78c5d7e0642c5fb4e1e35eeb).  
The code is for Windows and ANSI terminals and does not disrupt pipe output. I listen to your comments and suggestions.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-17 21:49:46 UTC  
> Url: https://github.com/boostorg/build/issues/261#issuecomment-352288362  

There should be an option to control this explicitly.  Also, it would be better to use descriptive names like TC_ACTION_NAME instead of naming a specific color.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-27 09:31:49 UTC  
> Url: https://github.com/boostorg/build/issues/261#issuecomment-578662911  

There was a brief discussion on such feature here https://svn.boost.org/trac10/ticket/3508 followed-up on the mailing list, see https://lists.boost.org/boost-build/2012/01/25582.php  
  
Wouldn't the colourful messages implemented as part of the `ECHO`  be more B2 canonical?   
What is the right way to appraoch this

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:04 UTC  
> Url: https://github.com/boostorg/build/issues/261#issuecomment-850877322  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
