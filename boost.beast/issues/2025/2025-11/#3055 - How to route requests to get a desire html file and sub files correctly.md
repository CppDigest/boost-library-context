# #3055 - How to route requests to get a desire html file and sub files correctly? [Closed]

> Username: GyroPower  
> Created at: 2025-11-07 23:10:53 UTC  
> Updated at: 2025-11-08 00:29:48 UTC  
> Closed at: 2025-11-08 00:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/3055  

I was looking to the http_server example and is very amazing but I was wondering if the user want to access to a target url, let's say in puts the url only the domain name and "/" or goes to the target called "/user/settings" and I want to retreive the need it html file with all the js code for an interactive view, how I set that the response will get all the desired docs on the directory where is stored every html file in the way I think is the best use case for me, for example every text/html response live on "./views/..." where the ... dots are the different sub folders for every html response that the file_body response of the http::get request ask, I ask because I was trying to re route the response of the user trying to get to the "/" target, and passing "/views/hello.html" but a hello.js file that goes with hello.html was not found, they both live in the same folder, so which approach should I use to do this well or should I refer something in the docs that I did pay attention, should I try something different that maybe I lack the knowledge.  
  
Or should I match the routes as the file structure on my project?  
  
```   
//How I let it get the js files or any other that are relative in the same folder of the html file that the html file needs?  
if (req.target == "/")  
    path = path_cat(doc_root, "views/hello.html)  
```

---

## Comment 1

> Username: GyroPower  
> Created at: 2025-11-08 00:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/3055#issuecomment-3505474336  

Sorry I was missing how to route correctly all the static data but now I know, I'm a rookie still in how to handle files and how a server does it, in the doc_root variable I just had to add where to look for the common "views" and depending of the url resolve it, I feel dumb.  
  
for example:  
the url is `user/settings`  
  
I just have to:  
```  
std::string path_for_target(cons std::string &target){   
    //cleaning the beginning of the url if have /  
    std::string clean_target = (target_size() && target[0] == '/' ?   target_substr(1) : target;  
    fs::path doc_path = doc_root / "views" / clean_target;  
  
    if(!doc_path.has_extension()){  
        doc_path += ".html";  
    }  
    return doc_path.string();  
}  
```  
  
Sorry is the question was dumb but I was easy to resolve, sorry
