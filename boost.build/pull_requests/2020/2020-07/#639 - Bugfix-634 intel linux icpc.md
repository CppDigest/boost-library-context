# #639 Bugfix/634 intel linux icpc [Merged]

> Username: aminiussi  
> Created at: 2020-07-15 13:28:48 UTC  
> Updated at: 2021-10-02 21:01:39 UTC  
> Merged at: 2020-09-01 04:35:00 UTC  
> Closed at: 2020-09-01 04:35:00 UTC  
> Url: https://github.com/boostorg/build/pull/639  

Follow up to #637

---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2020-07-15 21:50:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-449369027  

📁 src/engine/build.sh

```diff
 128 |+     elif test -r /opt/intel/inteloneapi/setvars.sh && test_cxx11 intel-linux ; then
 129 |+         B2_TOOLSET=intel-linux
 130 |+         B2_TOOLSET_ROOT=/opt/intel/inteloneapi
```

> Username: ax3l  
> Created_at: 2020-07-15 21:50:02 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455382398  

Looking at the logic below, I think we should set this to:  
```diff  
-        B2_TOOLSET_ROOT=/opt/intel/inteloneapi  
+        B2_TOOLSET_ROOT=/opt/intel/inteloneapi/compiler/latest/linux/  
```  
UPDATE: please do *not* apply this. see below.

> Username: ax3l  
> Created_at: 2020-07-15 21:57:44 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455388919  

Unfortunately, the `iccvars.sh` script in `bin/` seems to be a dead link on my local copy of oneAPI pulled via the apt repos: https://software.intel.com/content/www/us/en/develop/articles/oneapi-repo-instructions.html  
  
We should instead source `/opt/intel/inteloneapi/setvars.sh` below with this one.

> Username: aminiussi  
> Created_at: 2020-07-15 21:58:36 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455389285  

I do not have that distribution right now. So if anyone with that distribution can confirm I'll commit the suggestion.

> Username: ax3l  
> Created_at: 2020-07-15 22:00:20 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455390055  

I'll post a suggestion, give me a sec :)

> Username: ax3l  
> Created_at: 2020-07-15 22:03:00 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455391167  

@aminiussi can you please mark this one as "resolved"? (don't apply this please) :)


---

## Review 2 [Commented]

> Username: ax3l  
> Created_at: 2020-07-15 22:02:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-449375105  

📁 src/engine/build.sh

```diff
 275 |         fi
 276 |         if test -r ${B2_TOOLSET_ROOT}bin/iccvars.sh ; then
```

> Username: ax3l  
> Created_at: 2020-07-15 22:02:04 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455390805  

This script replaces the old `iccvars.sh`:  
```diff  
+        if test -r ${B2_TOOLSET_ROOT}/setvars.sh ; then  
+            . ${B2_TOOLSET_ROOT}/setvars.sh  
+        elif test -r ${B2_TOOLSET_ROOT}bin/iccvars.sh ; then  
```  
  
`iccvars.sh` still is around but as a broken link, so we should test for the new script first and avoid sourcing the second (broken) one if found.  
  
I am not sure we need the below mentioned `LD_RUN_PATH` hacks, those can be added later on when needed.

> Username: ax3l  
> Created_at: 2020-07-15 22:36:19 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455404364  

@aminiussi can we apply this one please?


---

## Review 3 [Commented]

> Username: ax3l  
> Created_at: 2020-07-15 23:19:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-449406902  

📁 src/engine/build.sh

```diff
 254 |         CXX_VERSION_OPT=${CXX_VERSION_OPT:=--version}
 255 |         test_path ${CXX} >/dev/null 2>&1
 256 |         if test $? ; then
```

> Username: ax3l  
> Created_at: 2020-07-15 23:19:53 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455419385  

Spotted one more bug in the previous implementation:  
```diff  
+        if test_path ${CXX} ; then  
```


---

## Review 4 [Commented]

> Username: ax3l  
> Created_at: 2020-07-15 23:25:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-449408863  

📁 src/engine/build.sh

```diff
 273 |-         if test -r ${B2_TOOLSET_ROOT}bin/iccvars.sh ; then
 276 |+         if test -r ${B2_TOOLSET_ROOT}/setvars.sh ; then
 277 |+             . ${B2_TOOLSET_ROOT}/setvars.sh
```

> Username: ax3l  
> Created_at: 2020-07-15 23:25:43 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455421236  

great, the last problem is that this script uses bash-ism...  
  
oh dear, ... not sure if we want to promote the whole suite here (see line 1) to `bash` for that purpose... I will complain upstream with intel, everything else is potentially okay and can already go in.  
  
Another change I cannot mark:  
14 lines above, the line 263  
```bash  
            if test -r /opt/intel/cc/9.0/bin/iccvars.sh ; then  
```  
must be replaced with this, please:  
```bash  
            if test -r /opt/intel/inteloneapi/setvars.sh ; then  
                B2_TOOLSET_ROOT=/opt/intel/inteloneapi  
            elif test -r /opt/intel/cc/9.0/bin/iccvars.sh ; then  
```  
  
Besides the bash-ism, this will now work for users:  
```  
. /opt/intel/inteloneapi/setvars.sh  
./bootstrap.sh --with-toolset="intel-linux"  
```

> Username: aminiussi  
> Created_at: 2020-07-15 23:41:09 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455426297  

Just after:  
```  
            echo "No intel compiler in current path"  
            echo "Look in a few old place for legacy reason"  
```  
Shouldn't we change the comment then, as oneapi is not that old ?

> Username: ax3l  
> Created_at: 2020-07-15 23:48:41 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r455428600  

I think the "legacy reasons" are that the script always tried to find the paths, not that the compiler it looks for is old.


---

## Comment 5

> Username: aminiussi  
> Created_at: 2020-07-17 07:42:18 UTC  
> Url: https://github.com/boostorg/build/pull/639#issuecomment-659932158  

So I think everything is there. Let me know if there is anything missing.

---

## Review 6 [Commented]

> Username: ax3l  
> Created_at: 2020-07-17 16:44:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-450806975  

📁 src/engine/build.sh

```diff
 261 |-             if test -r /opt/intel/cc/9.0/bin/iccvars.sh ; then
 262 |+             echo "Look in a few common places just in case"
 263 |+ 	    if test -r /opt/intel/inteloneapi/setvars.sh ; then
```

> Username: ax3l  
> Created_at: 2020-07-17 16:44:09 UTC  
> Updated_at: 2020-09-01 00:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/639#discussion_r456555194  

Just a minor formatting problem:  
```diff  
-	    if test -r /opt/intel/inteloneapi/setvars.sh ; then  
+            if test -r /opt/intel/inteloneapi/setvars.sh ; then  
```


---

## Comment 7

> Username: ax3l  
> Created_at: 2020-07-17 17:21:04 UTC  
> Updated_at: 2020-07-17 17:24:06 UTC  
> Url: https://github.com/boostorg/build/pull/639#issuecomment-660237170  

@aminiussi thank you! I annotated a last formatting quirk and with the mentioned work-around of activating icc beforehand I can execute this now with ICC from oneAPI in beta07.  
```bash  
. /opt/intel/inteloneapi/setvars.sh  
./bootstrap.sh --with-toolset="intel-linux" --without-libraries=log  
./b2  
# ...  
# The Boost C++ Libraries were successfully built!  
```  
There are individual libraries like Boost.Log that do not build, but that's not part of this PR enabling icc again in general :)

---

## Review 8 [Approved]

> Username: ax3l  
> Created_at: 2020-07-24 07:45:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/build/pull/639#pullrequestreview-454691425  

LGTM, thanks a lot!  
  
@grafikrobot looks like CI had some network issues on Windows runners, otherwise this looks great :)

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2020-07-24 17:06:54 UTC  
> Url: https://github.com/boostorg/build/pull/639#issuecomment-663639314  

It all looks good.. But I have one final small request.. Can you add appropriate copyright attribution statements?

---

## Comment 10

> Username: aminiussi  
> Created_at: 2020-07-24 19:28:41 UTC  
> Url: https://github.com/boostorg/build/pull/639#issuecomment-663697046  

> It all looks good.. But I have one final small request.. Can you add appropriate copyright attribution statements?  
  
Sure, can you be more specific ? I don't know the story of that section very much.

---

## Comment 11

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:38 UTC  
> Url: https://github.com/boostorg/build/pull/639#issuecomment-932819937  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
