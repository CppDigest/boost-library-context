# #18 - Update bintray credentials (was: add fuzzing) [Closed]

> Username: pauldreik  
> Created at: 2019-12-03 18:43:23 UTC  
> Updated at: 2020-08-31 18:51:21 UTC  
> Closed at: 2020-08-30 23:39:09 UTC  
> Url: https://github.com/boostorg/json/issues/18  

This is a ticket to keep track of adding fuzz testing. I promised to help out, so here I am!  
I wanted to highlight the plan so everyone is happy with the direction.  
  
The plan is to  
 - add a fuzz target (and build support, if necessary)  
 - helper scripts to get an initial seed corpus  
 - add a github action which runs the fuzzing for a short time (30 seconds or so) to make sure easy to find bugs are detected already at the pull request stage  
  
# Building  
I had problem building the library - I suspect others wanting to try it out also may run into problems. Is there documentation somewhere that I missed? I expected the usual git clone, submodule recursive update, cmake to work out of the box but there seems to be a dependency on boost beast.  
  
# Fuzz target  
There is already a fuzzer in #13 . @msimonsson are you ok that I incorporate your fuzzer code from that ticket under the boost license? I assume this is OK, but I am not a lawyer (well perhaps a C++ language lawyer wannabe :-).  
  
# Github Action  
I have used this for the simdjson project recently, worked fine. I am not sure if it is possible to browse through it unless being a member, but here are some links:  
 - the config: https://github.com/lemire/simdjson/blob/master/.github/workflows/fuzzers.yml  
 - the jobs: https://github.com/lemire/simdjson/actions  
 - partial motivation: https://github.com/lemire/simdjson/issues/370  
  
For efficiency, it is good if the corpus can be stored somewhere between the runs. Otherwise it has to bootstrap each time which is inefficient. I use bintray for simdjson - @vinniefalco where would you be ok to store the corpus, do you perhaps already have a bintray account? In the meanwhile, I will use my own.  
  
# Implementation  
I develop this in my clone for now.  
See the Readme over here: https://github.com/pauldreik/json/tree/paul/fuzz/fuzzing

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-03 18:53:36 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-561305853  

I made a bintray account https://bintray.com/vinniefalco.  
  
The CMakeLists.txt is really a private file used only to generate the Visual Studio project. The easiest way to get an executable for fuzzing is to define `BOOST_JSON_HEADER_ONLY`, then you won't need a library to link against. However, this assumes that Boost is available.  
  
If you want to have it work without Boost (for example to make the GitHub action take less time by not cloning the Boost repository) then you can define `BOOST_JSON_STANDALONE`. I think this will be the best choice but note that by doing so, the program must be compiled for C++17 since it will use `std::string_view` (instead of `boost::string_view`).  
  
Thanks!

---

## Comment 2

> Username: msimonsson  
> Created at: 2019-12-03 19:26:19 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-561319115  

> There is already a fuzzer in #13 . @msimonsson are you ok that I incorporate your fuzzer code from that ticket under the boost license? I assume this is OK, but I am not a lawyer (well perhaps a C++ language lawyer wannabe :-).  
  
Absolutely, 90 % of the code is from:  
https://github.com/vinniefalco/json/blob/develop/example/validate.cpp

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 15:35:21 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-672948069  

Is there anything actionable here?

---

## Comment 4

> Username: pauldreik  
> Created at: 2020-08-14 13:37:43 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-674078994  

I think the fuzzing was held up by a bug in floating point parsing? I  
could not proceed with the fuzzers because it hit UB and stopped.  
Perhaps that bug is gone now?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-14 15:49:57 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-674139299  

Very likely yes

---

## Comment 6

> Username: pauldreik  
> Created at: 2020-08-17 10:38:45 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-674804537  

I refreshed the fuzzer with the code changes on develop. The old bugs are not there anymore, I ran the parser over night without problems. That means it is time to get fuzzing into a github action!

---

## Comment 7

> Username: pauldreik  
> Created at: 2020-08-22 15:53:00 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-678657081  

The only thing that remains to do before closing this issue is to update the bintray credentials.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-08-28 18:22:02 UTC  
> Updated at: 2020-08-28 18:31:54 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683037073  

@pauldreik Let me tell you, the bintray interface is really hard to understand. I'm not sure if I got it right, can you please have a look? See #226   
How can I tell if it is uploading correctly?

---

## Comment 9

> Username: pauldreik  
> Created at: 2020-08-30 17:40:33 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683448709  

#226 looks sufficient. Did you also already set the bintray api secret here?  
I think what remains is that you need to add a package on the bintray UI. See the comment here: https://github.com/CPPAlliance/json/pull/165#issue-469316920  
  
To see that it works, have a look under the "upload corpus to bintray" section at the log for the github action.[ As of now](https://github.com/CPPAlliance/json/runs/1047610731?check_suite_focus=true), it says "{"message":"This resource requires authentication"}"  
which when successfully configured IIRC should say  
"{"message":"success"}

---

## Comment 10

> Username: pauldreik  
> Created at: 2020-08-30 19:24:26 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683459927  

Looks better, but I think I had an extra "corpus/" by mistake when I set it up which causes problems now...   
So please add the modification below to get rid of the extra "corpus/"  
```diff  
diff --git a/.github/workflows/run_fuzzer.yml b/.github/workflows/run_fuzzer.yml  
index 5b13cb4..f3c2f2d 100644  
--- a/.github/workflows/run_fuzzer.yml  
+++ b/.github/workflows/run_fuzzer.yml  
@@ -18,7 +18,7 @@ jobs:  
     steps:         
     - uses: actions/checkout@v2  
     - name: download the old corpus  
-      run: "curl -O --location -J https://bintray.com/$BINTRAYUSER/boost.json/download_file?file_path=corpus%2Fcorpus.tar"  
+      run: "curl -O --location -J https://bintray.com/$BINTRAYUSER/boost.json/download_file?file_path=corpus.tar"  
       working-directory: fuzzing/  
     - name: build and run  
       run: ./fuzz.sh  
@@ -28,8 +28,8 @@ jobs:  
       run: |  
         echo uploading each artifact twice, otherwise it will not be published  
         tar cf - cmin > corpus.tar  
-        curl -T corpus.tar -u$BINTRAYUSER:${{ secrets.bintrayApiKey }} https://api.bintray.com/content/$BINTRAYUSER/boost.json/corpus/0/corpus/corpus.tar";publish=1;override=1"  
-        curl -T corpus.tar -u$BINTRAYUSER:${{ secrets.bintrayApiKey }} https://api.bintray.com/content/$BINTRAYUSER/boost.json/corpus/0/corpus/corpus.tar";publish=1;override=1"  
+        curl -T corpus.tar -u$BINTRAYUSER:${{ secrets.bintrayApiKey }} https://api.bintray.com/content/$BINTRAYUSER/boost.json/corpus/0/corpus.tar";publish=1;override=1"  
+        curl -T corpus.tar -u$BINTRAYUSER:${{ secrets.bintrayApiKey }} https://api.bintray.com/content/$BINTRAYUSER/boost.json/corpus/0/corpus.tar";publish=1;override=1"  
     - name: Save the corpus as a github artifact  
       uses: actions/upload-artifact@v2  
       with:  
  
```

---

## Comment 11

> Username: pauldreik  
> Created at: 2020-08-31 04:48:02 UTC  
> Updated at: 2020-08-31 04:56:16 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683546489  

Better, but it still does not look like it works... Could you please try the curl command locally, pasting in your api key (note the space at the beginning to avoid having it stored in your bash history):  
```sh  
 curl -T corpus.tar -ucppalliance:XXXXXXXXXX https://api.bintray.com/content/cppalliance/boost.json/corpus/0/corpus.tar";publish=1;override=1"  
```  
When I do this with my name and key, I get {"message":"success"} on the output.  
  
If that does not work, perhaps you need to manually publish the package at bintray first through the web interface? I had some trial and error to get this to work.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-08-31 14:56:04 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683831506  

This happens:  
```  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
100 12.5M  100    51  100 12.5M     10  2624k  0:00:05  0:00:04  0:00:01 2694k{"message":"This resource requires authentication"}  
```  
  
bintray is a hassle...

---

## Comment 13

> Username: pauldreik  
> Created at: 2020-08-31 18:24:06 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683949009  

Well, if you can't upload to your bintray account with your user and key, I do not know what to do? I get the same message as you if I leave out the -uUSER:PASS entirely or if I use an invalid apikey. If I only give the username, curl asks for a password so it can't be that.  
  
I think it is time that you ask bintray support why it won't work, we should have identical setups and mine works and yours doesn't. Perhaps they can see in their logs why you are rejected.  
  
I heard about something called [github actions cache](https://docs.github.com/en/actions/configuring-and-managing-workflows/caching-dependencies-to-speed-up-workflows#comparing-artifacts-and-dependency-caching) which perhaps would work better, but I have never used it. Storing the corpus seems to violate their use case description, but it might work better than bintray since no authentication is needed from within the job.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-08-31 18:31:58 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683953183  

I believe it works now, have a look at it and the latest commits in _develop_ ?

---

## Comment 15

> Username: pauldreik  
> Created at: 2020-08-31 18:45:57 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683960817  

Yay! I did not know you had a different organization/username! How did you find out what was wrong?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-08-31 18:51:21 UTC  
> Url: https://github.com/boostorg/json/issues/18#issuecomment-683963836  

Trial and error, and help from @sdarwin . your curl command was helpful.
