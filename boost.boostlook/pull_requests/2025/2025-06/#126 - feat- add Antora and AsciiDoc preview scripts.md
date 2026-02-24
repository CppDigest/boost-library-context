# #126 feat: add Antora and AsciiDoc preview scripts [Open]

> Username: julioest  
> Created at: 2025-06-10 01:18:29 UTC  
> Updated at: 2025-07-08 14:08:24 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126  

### Summary  
Streamlines development workflow by consolidating documentation and improving script naming clarity.  
  
References #118   
  
### Changes  
- **Preview scripts:**  
  - Rename `boostlook_preview.rb` → `boostlook_asciidoc_preview.rb`  
  - Add `boostlook_antora_preview.rb`  
  
- **Consolidate documentation:**  
  - Move all development workflows to `DEVELOPMENT.md`  
  - Simplify `README.md` to project overview only  
  
### Benefits  
- ✅ Single source of truth for development setup  
- ✅ Clear script names indicate purpose  
- ✅ Eliminates duplicate documentation  
- ✅ Better developer onboarding experience  
  
### Testing  
- [x] Verified script renames work correctly  
- [x] Documentation links and references updated  
- [x] Both preview workflows documented

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-10 01:27:43 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-2957435052  

An automated preview of the documentation is available at [https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-06-25 23:22:41 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3006509240  

An automated preview of the documentation is available at [https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 3

> Username: sdarwin  
> Created_at: 2025-06-26 14:24:45 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3008686368  

These issues with ./boostlook_asciidoc_preview.rb  
  
Some warnings/errors appeared, mentioned in Slack.  Maybe not a concern.    
  
- must be run from the folder tools/boostlook/, but doesn't inform the user about this.   
- requires b2 to be compiled, and copied to /usr/local/bin.    
- requires certain ruby gems.   
  
The above steps could be included within the script itself in a text `Instructions` section near the top.    
  
./boostlook_antora_preview.rb  
  
minor warnings.  
  
```  
/usr/bin/xdg-open: 882: www-browser: not found  
/usr/bin/xdg-open: 882: links2: not found  
/usr/bin/xdg-open: 882: elinks: not found  
/usr/bin/xdg-open: 882: links: not found  
/usr/bin/xdg-open: 882: lynx: not found  
/usr/bin/xdg-open: 882: w3m: not found  
```  
  
again, perhaps add text instructions.     
Otherwise very nice.

---

## Comment 4

> Username: julioest  
> Created_at: 2025-06-26 18:22:14 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3009427974  

@sdarwin Thanks for reviewing & the feedback!

---

## Review 5 [Approved]

> Username: karimarie67  
> Created_at: 2025-06-26 19:20:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/126#pullrequestreview-2963295237  

Tested with Julio for both sets of preview scripts.  
  
1. We ran into issues with asciidoctor but antorra worked as expected  
2. The answer to the asciidoctor problem was to move the user-config.jam into my /Users/kskinner directory. We then had to add the following to that file. Once we did that, all worked as expected.  
  
Use the full path to the asciidoctor executable found by 'which'  
using asciidoctor : /usr/local/bin/asciidoctor ;  
  
3. The updated documentation (development.md and readme.md) incorporates steps that you need to do to install and use the preview tool. I think the problem I had was self-induced. That said, we can add to the troubleshooting section with how to address.  
  
Ready to go to develop/staging.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2025-06-26 21:30:58 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3010191459  

Notice what Kari wrote:  
  
Create or modify the file `user-config.jam` in $HOME.    
  
Use the full path to the asciidoctor executable found by 'which'  
  
`using asciidoctor : /usr/local/bin/asciidoctor ;`  
  
That should be in the doc.  It was only after this step that I got a preview to launch in a browser.        Before, it said:        
  
```  
error: no generators were found for type 'HTML'  
Build failed  
Failed to generate doc/html/specimen.html  
```

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-07-03 19:14:35 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3033353628  

An automated preview of the documentation is available at [https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 8

> Username: julioest  
> Created_at: 2025-07-03 19:49:35 UTC  
> Updated_at: 2025-07-03 19:51:00 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3033432861  

@karimarie67 @sdarwin How do we feel about adding this? This is in latest commit  
  
### Preview with AsciiDoc Requirements  
  
-   **Asciidoctor Gem**: Install via `gem install asciidoctor`.  
-   **Boost.Build (b2)**: Ensure `b2` is installed and available in your system's `PATH`. You can verify this by running `which b2` or `b2 --version`.  
-   **Boost.Build Configuration (`user-config.jam`)**: This is a critical step. You must tell Boost.Build where to find your `asciidoctor` executable.  
    1.  Find the full path to `asciidoctor` by running:  
        ```sh  
        which asciidoctor  
        ```  
        The output will be a path like `/usr/local/bin/asciidoctor`.  
  
    2.  Create or edit the file `user-config.jam` in your home directory (`$HOME/user-config.jam` on Linux/macOS).  
  
    3.  Add the following line to the file, using the path from step 1:  
        ```jam  
        # Tell Boost.Build where to find the asciidoctor executable  
        using asciidoctor : /usr/local/bin/asciidoctor ;  
        ```  
  
## Troubleshooting  
  
### Preview with AsciiDoc Issues  
  
**Error: `error: no generators were found for type 'HTML'`**  
  
- **Cause**: This is the most common issue. It means Boost.Build (`b2`) cannot find the `asciidoctor` executable.  
- **Solution**: You must configure your `$HOME/user-config.jam` file. Please follow the instructions in the **"Boost.Build Configuration (`user-config.jam`)"** section under Prerequisites.  
  
**Error: `command not found: b2`**  
  
- **Cause**: The Boost.Build `b2` executable is not in your system's `PATH`.  
- **Solution**: Install Boost.Build and ensure its location is added to your `PATH`. Verify with `which b2`.  
  
**Script must be run from the project root**  
  
- **Cause**: The script expects to be run from the main `boostlook/` directory.  
- **Solution**: Make sure you are in the project's root directory before running `ruby boostlook_asciidoc_preview.rb`.  
  
**Warnings like `www-browser: not found` or `xdg-open: ... not found`**  
  
- **Cause**: These warnings may appear on some Linux systems if the script cannot find a default command-line web browser.  
- **Solution**: These are usually harmless and can be ignored if a graphical browser still opens. To resolve them, you can install a common utility like `xdg-utils` (`sudo apt-get install xdg-utils` on Debian/Ubuntu).

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2025-07-03 19:52:44 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3033442225  

An automated preview of the documentation is available at [https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://126.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 10

> Username: sdarwin  
> Created_at: 2025-07-03 21:28:08 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3033720700  

> @karimarie67 @sdarwin How do we feel about adding this?   
  
@karimarie67 , @julioest , from your testing, do previews function correctly from a stand-alone boostlook folder, or must they be located in a certain folder.  
  
Example 1.  cd $HOME/boostlook/  .   Run previews.   
  
Example 2. cd $HOME/github/boost/tools/boostlook/   . Run previews.  
  
Do both of those work for you?

---

## Comment 11

> Username: julioest  
> Created_at: 2025-07-07 15:48:05 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3045686650  

@sdarwin Yes, works for me

---

## Comment 12

> Username: sdarwin  
> Created_at: 2025-07-07 16:09:34 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3045750402  

For me, example 2 works.   Example 1 shows this:     
  
```  
root@067a5bf4d28c:/opt/github/julioest/boostlook# ./boostlook_asciidoc_preview.rb   
Unable to load B2  
-----------------  
No '' was found by searching for:  
  /usr/local/bin/.b2/build-system.jam  
  /usr/local/share/b2/build-system.jam  
  /usr/local/share/b2/src/build-system.jam  
  /usr/local/build-system.jam  
  /usr/local/bin/src/build-system.jam  
  /usr/local/bin/tools/build/src/build-system.jam  
  /usr/local/bin/src/build-system.jam  
  /usr/local/src/build-system.jam  
  /usr/src/build-system.jam  
  /src/build-system.jam  
  
Please consult the documentation at 'https://www.bfgroup.xyz/b2/'.  
  
Build failed  
Failed to generate doc/html/specimen.html  
```  
  
If you go through the setup, starting at the very beginning, in a fresh docker container...  it might not be able to display the preview because a browser is missing.   But otherwise, at least, it should get past the "b2" errors, and then maybe get to  the browser errors.   If you hit b2 errors it replicates the problem.   Could you try it?   What results did @karimarie67 get?  Or, maybe I missed a setup step that you and Kari have done correctly.

---

## Comment 13

> Username: sdarwin  
> Created_at: 2025-07-08 14:08:24 UTC  
> Url: https://github.com/boostorg/boostlook/pull/126#issuecomment-3049125003  

I think that I discovered the problem.   There are two ways to put `b2` on the PATH.  
1. Copy it to /usr/local/bin/b2 (or any other well-known $PATH location). This is a common way to install software.    
2. Leave `b2` where it is, and modify $PATH so that it points there, such as `export PATH=/opt/github/boostorg/boost-root:$PATH`    
  
I was doing the first one, but that's wrong, it seems to only accept the second choice.       
  
So maybe update the instructions, since it's not enough to say "b2 is on the PATH."      It must be "modify your PATH variable to include the current location of `b2`, such as `export PATH=/opt/github/boostorg/boost-root:$PATH`   or a similar command.   Don't copy b2 into /usr/bin or /usr/local/bin."

---
