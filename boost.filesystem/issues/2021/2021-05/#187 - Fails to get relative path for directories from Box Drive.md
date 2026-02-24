# #187 - Fails to get relative path for directories from Box Drive [Closed]

> Username: daria-rogova  
> Created at: 2021-05-19 18:41:04 UTC  
> Updated at: 2021-06-06 19:59:04 UTC  
> Closed at: 2021-06-06 19:59:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/187  

**Description:**  
  
System: Windows,  
boost version: 1_0_76.  
  
  
**When work with Box drive directories (https://www.box.com/) filesystem::relative method fails:   
  
`auto relative = boost::filesystem::relative("C:\\Users\\User\\Box\\testbox", "C:\\Users\\User\\Box");`**  
  
  
**Steps to reproduce:**  
  
1. Install Box drive from:  https://www.box.com/ on Windows to C:\Users\User\Box.  
2. Create a folder C:\Users\User\Box\testbox.  
  
**Minimal program to reproduce: **  
  
	try {  
  
		auto relative = boost::filesystem::relative("C:\\Users\\User\\Box\\testbox", "C:\\Users\\User\\Box");  
	}  
	catch (const std::exception& e)  
	{  
		auto what = e.what();  **// ERROR: "boost::filesystem::relative: The system cannot find the file specified: "C:\\Users\\User\\Box\""**  
	}  
  
  
**Call stack investigation:**  
  
file operations.cpp, method details::canonical(const path& p, const path& base, system::error_code* ec)   
// p = {m_pathname=L"C:\\Users\\User\\Box\\testbox" } , base = the directory of the .exe  
  
line 952:  
`bool is_sym (is_symlink(detail::symlink_status(result, ec))); // result = {m_pathname=L"C:/Users\\User\\Box" } `  
  
is_sym is true, then line 558:  
`path link(detail::read_symlink(result, ec)); // link is ""`  
  
then in the line 961 the Box is removed as a filename, but Box is not a filename, it's a directory:  
`result.remove_filename(); // result = {m_pathname=L"C:/Users\\User" }`  
  
then on the lines 971-975 the source variable is assigned to non existent directory:  
  
        {  
          path new_source(result);  
          new_source /= link;  
          for (++itr; itr != source.end(); ++itr)  
            new_source /= *itr;  
          **source** = new_source;     //       source = {m_pathname=L"C:/Users\\User\\testbox" }  
        }  
 then the loop while (line 929) iterates through  non existent directory "C:/Users\\User\\testbox" and finally fails with the error "ERROR: "boost::filesystem::relative: The system cannot find the file specified: "C:\\Users\\User\\Box\".

---

## Comment 1

> Username: Lastique  
> Created at: 2021-05-19 21:48:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/187#issuecomment-844498261  

It seems, the problem is that whatever `C:\Users\User\Box` is, it identifies itself as a symlink that points to an empty path. This is an invalid symlink, since it must point to something, at the very least the current directory (`.`).  
  
The code does not trigger an error in `read_symlink` (which might be worth fixing, but it wouldn't help your case) and returns whatever path returned by OS - an empty path. Then `canonical` correctly attempts to replace the symlink with the path it points to. An empty path is a relative path, so that means it has to be appended to the parent path of the symlink. Which is why `remove_filename()` and the following loop reconstructing `source` is correct.  
  
I don't have a Box account and I don't intend to pay for one, so I'm unlikely to test it. I think, the problem might be in the `is_reparse_point_a_symlink` function, which detects a symlink. I suspect, your `Box` directory is a reparse point, but I have no idea how to distinguish it from junctions, which also are reparse points and are supposed to be interpreted as symlinks.

---

## Comment 2

> Username: daria-rogova  
> Created at: 2021-05-20 13:32:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/187#issuecomment-845125028  

@Lastique Is there any workaround here?  
  
We just need to get relative path to C:\Users\User\Box:  
  
`auto relative = boost::filesystem::relative("C:\\Users\\User\\Box\\testbox", "C:\\Users\\User\\Box");`  
  
The Box drive is free and the registration is very quick.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-05-20 14:48:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/187#issuecomment-845188209  

I can't suggest a workaround, other than to use manual lexical processing of the paths (e.g. iterate over path elements using path iterators and keep what's left as the resulting relative path). This will only work if the two paths have a common initial part.
