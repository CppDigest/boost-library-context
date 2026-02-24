# #28 Add instructions to build the docs [Closed]

> Username: sdarwin  
> Created at: 2021-02-18 21:50:22 UTC  
> Updated at: 2021-11-23 23:36:46 UTC  
> Closed at: 2021-11-23 23:36:46 UTC  
> Url: https://github.com/boostorg/url/pull/28  

Scripts to build the documentation.  
  
@zajo please try these and let me know if you run into any problems.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-02-18 21:59:14 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-781662138  

An automated preview of the documentation is available at [https://28.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://28.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-03-06 02:28:17 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-791843995  

@zajo did you try this?

---

## Comment 3

> Username: zajo  
> Created_at: 2021-03-07 23:29:45 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-792374497  

Going to now.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-03-11 14:40:55 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-796785030  

- the scripts now detect if they are being run inside boost-root or not. Either configuration will be able to build the docs.  
- the commit yesterday about 'XSLT import' fixes a build error.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-03-13 01:03:26 UTC  
> Updated_at: 2021-03-13 01:03:39 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-797837863  

Don't those shell script files need a "git chmod +x" equivalent in order to be executed locally after fetching?  
https://stackoverflow.com/questions/40978921/how-to-add-chmod-permissions-to-file-in-git

---

## Comment 6

> Username: sdarwin  
> Created_at: 2021-03-13 01:34:43 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-797843420  

Git stores whether a file is executable or not. When checking out these files, it looks like they have permissions of 755, which means executable for the user.  
  
```  
-rwxr-xr-x 1 root root 2625 Mar 12 19:24 linuxdocs.sh  
-rwxr-xr-x 1 root root 2718 Mar 12 19:24 osxdocs.sh  
```

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-03-13 03:43:34 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-797859953  

Ah.... GitHub **used** to show you the file mode bits in the diff view, but it seems they moved it to be visible only under View File:  
![image](https://user-images.githubusercontent.com/1503976/111017956-39d60a00-836b-11eb-844e-083a9066588d.png)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2021-05-13 20:05:33 UTC  
> Updated_at: 2021-05-13 20:29:05 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-840801694  

- included a Microsoft Windows version  
- tested all operating system scripts (linux, osx, windows)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2021-10-30 16:00:39 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-955384468  

Is this pull request still relevant or is it stale?

---

## Comment 10

> Username: sdarwin  
> Created_at: 2021-10-30 16:28:57 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-955430995  

I believe this is still relevant and not stale.     
  
It "adds instructions to build the docs", which shouldn't affect too much or break CI. Could be merged.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2021-10-30 16:37:21 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-955445217  

Yeah but that thing with the Windows script looks really try-hard. I just type "b2 doc" and it builds the docs.

---

## Comment 12

> Username: sdarwin  
> Created_at: 2021-10-30 16:59:38 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-955482205  

> looks really try-hard.   
  
The script is assuming the user starts with nothing except the url repository.   Thus, it's  
- installing saxon  
- installing doxygen  
- determining if the boost superproject is present.  If not, checking it out, copying the repo into the boost directory.  
- building b2  
  
It installs all dependencies, like a Drone job would.   
  
If you have those things set up in advance, you could skip using the script, and run "b2".  
  
Or, not sure if you are specifically referring to the b2 command-line options.   
  
The b2 command-line options were based on earlier examples, going back to Travis CI.   If they can be removed, that's a good idea to simplify.

---

## Comment 13

> Username: sdarwin  
> Created_at: 2021-11-02 12:36:04 UTC  
> Url: https://github.com/boostorg/url/pull/28#issuecomment-957466008  

@vinniefalco wrote:  
> I just type "b2 doc"  
  
Set the scripts to run "b2 doc" without command-line flags.

---
