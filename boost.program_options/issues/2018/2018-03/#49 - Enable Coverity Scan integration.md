# #49 - Enable Coverity Scan integration [Open]

> Username: jeking3  
> Created at: 2018-03-21 16:35:00 UTC  
> Updated at: 2018-04-29 12:13:37 UTC  
> Url: https://github.com/boostorg/program_options/issues/49  

Now that the ci PR was merged, we can enable Coverity Scan integration.  As admin of the repository you will need to go to scan.coverity.com and sign in with your github account, and do "Add Project" -> "Register my GitHub Project" -> select the boostorg/program_options project to add.  After that, you need to take the project token on the project settings page and put it into your Travis CI settings for the boostorg/program_options builds, and make them private (there's a toggle):  
  
COVERITY_SCAN_TOKEN (set to the token from coverity scan)  
COVERITY_SCAN_NOTIFICATION_EMAIL (set to your email so you get notified on completed builds)  
  
At some point I might put together some better instructions on how to do this with pictures...  
  
Also recommend setting up a weekly cron on develop and master in travis CI.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-04-29 12:13:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/49#issuecomment-385247074  

By the way, Coverity Scan is running again.
