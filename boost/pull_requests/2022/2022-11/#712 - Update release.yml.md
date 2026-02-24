# #712 Update release.yml [Closed]

> Username: Flamefire  
> Created at: 2022-11-16 16:48:51 UTC  
> Updated at: 2022-11-16 20:42:18 UTC  
> Closed at: 2022-11-16 19:52:08 UTC  
> Url: https://github.com/boostorg/boost/pull/712  

- Avoid Copy of all files  
- Prune all git and CI (meta) data files and folders  
- Convert line endings to Windows/Dos for the zip file  
  
Stripped down example run: https://github.com/Flamefire/locale/actions/runs/3481281759/jobs/5822170011

---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2022-11-16 16:58:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/712#pullrequestreview-1182975665  

📁 .github/workflows/release.yml

```diff
  43 |+         run: |
  44 |+           sudo apt-get -o Acquire::Retries=5 install -y dos2unix
  45 |+           find ${{ github.ref_name }} -type f -exec unix2dos {} \;
```

> Username: Lastique  
> Created_at: 2022-11-16 16:56:33 UTC  
> Updated_at: 2022-11-16 16:58:09 UTC  
> Url: https://github.com/boostorg/boost/pull/712#discussion_r1024266310  

I think this processes excess files. `.sh` files must retain UNIX line endings even on Windows, and you should not process any binary files, e.g. images in docs.  
  
I still think the best way to do it is to build Windows archives on Windows. git will do the conversion on checkout for you.

> Username: mojca  
> Created_at: 2022-11-16 17:53:49 UTC  
> Updated_at: 2022-11-16 17:53:50 UTC  
> Url: https://github.com/boostorg/boost/pull/712#discussion_r1024326548  

This certainly sounds way too dangerous.

> Username: Flamefire  
> Created_at: 2022-11-16 18:10:06 UTC  
> Updated_at: 2022-11-16 18:12:10 UTC  
> Url: https://github.com/boostorg/boost/pull/712#discussion_r1024344828  

unix2dos in recent version doesn't modify binary files as I've learned. But true on sh files, so added a windows job which makes it a bit more complicated and I hope the release uploader correctly finds the files and combines them into that one release  
  
I also wanted to avoid spawning an additional runner which has to do the clone and clean again. The recursive clone alone takes ~3m on Linux and ~8m on Windows. But I guess that is low enough.

---

📁 .github/workflows/release.yml

```diff
  22 |+           find ${{ github.ref_name }} -name ".git*" -prune -exec rm -rf {} +
  23 |+           # Remove CI configs
  24 |+           find ${{ github.ref_name }} \( \
```

> Username: Lastique  
> Created_at: 2022-11-16 16:57:10 UTC  
> Updated_at: 2022-11-16 16:58:09 UTC  
> Url: https://github.com/boostorg/boost/pull/712#discussion_r1024266983  

I'd add `-type f` as well.

> Username: Flamefire  
> Created_at: 2022-11-16 18:10:16 UTC  
> Updated_at: 2022-11-16 18:10:17 UTC  
> Url: https://github.com/boostorg/boost/pull/712#discussion_r1024345014  

Done


---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-11-16 18:27:14 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317480461  

Test run with a [simplified config](https://github.com/Flamefire/locale/blob/test/.github/workflows/ci.yml) (no upload) is at https://github.com/Flamefire/locale/actions/runs/3482065247

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-11-16 18:27:29 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317481157  

I'm not quite sure what will happen if the two jobs try to create the same release simultaneously.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2022-11-16 18:41:15 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317501226  

I'm not sure either, haven't used that action yet and the issue list doesn't look to promising.  
Previously I used actions/create-release & actions/upload-release-asset and the latter seems to work for uploading multiple assets.  
  
We could split it into 2/3 jobs: 1 creating the release and 2 publishing the assets (1 each), see e.g. [Publish and upload](https://github.com/Return-To-The-Roots/s25client/blob/0537322eb59cd418697ceeb35f21564150f0bdf4/.github/workflows/release.yml#L61-L84) and [Concurrent upload](https://github.com/Return-To-The-Roots/libsiedler2/blob/eea3744814d347baaff2923e6b0572b6f9de08ae/.github/workflows/release.yml#L125-L134)

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-11-16 18:49:26 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317510230  

> Previously I used actions/create-release & actions/upload-release-asset  
  
Those were my first choice but both are deprecated.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-11-16 19:24:48 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317553976  

I wonder why `7z` works for you without supplying a command, `7z a`. It fails for me.

---

## Comment 7

> Username: pdimov  
> Created_at: 2022-11-16 19:52:08 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317586658  

I applied the parts from your PR that I was sure we wanted (this doesn't mean that I'm necessarily strongly opposed to cleaning up most of the CI stuff or things like .gitmodules or .gitattributes, just that I'm not convinced we should do it.)  
  
Please create the future PRs against the develop branch. The default here is master - I'm not quite sure why - but the PRs need to go to develop.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2022-11-16 20:24:53 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317623498  

> I wonder why 7z works for you without supplying a command, 7z a. It fails for me.  
  
It doesn't. Missed that somehow while copying the stuff from my test file  
  
BTW: Instead of repeating the code you can still use the matrix approach from this PR and `maxparallel: 1`: https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs#defining-the-maximum-number-of-concurrent-jobs

---

## Comment 9

> Username: pdimov  
> Created_at: 2022-11-16 20:30:20 UTC  
> Url: https://github.com/boostorg/boost/pull/712#issuecomment-1317629957  

I don't mind repeating the code here, it's clearer this way and not that much of a maintenance burden as it fits on a screen. :-)

---
