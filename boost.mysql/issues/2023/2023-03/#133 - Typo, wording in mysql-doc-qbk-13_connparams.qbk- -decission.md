# #133 - Typo, wording in mysql/doc/qbk/13_connparams.qbk: "decission" [Closed]

> Username: SpareSimian  
> Created at: 2023-03-25 04:13:59 UTC  
> Updated at: 2023-05-24 10:19:07 UTC  
> Closed at: 2023-05-24 10:19:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/133  

Original text:  
```  
MySQL implements several ways of authentication with the server,  
in what is called [mysqllink pluggable-authentication.html  
pluggable authentication]. The decission of which authentication  
plugin to use is made in a per-user basis. This information  
is stored in the `mysql.user` table. In addition to this,  
servers define a default authentication plugin  
(see [mysqllink server-system-variables.html#sysvar_authentication_policy `authentication_policy`] and  
[mysqllink server-system-variables.html#sysvar_default_authentication_plugin  
`default_authentication_plugin`]). The default plugin will  
be used for newly created users, and may affect how the handshake works.  
```  
  
Suggested rewording:  
```  
MySQL implements several methods of authentication with the server,  
in what is called [mysqllink pluggable-authentication.html  
pluggable authentication]. The authentication  
plugin used is chosen on a per-user basis. This information  
is stored in the `mysql.user` table. Additionally,  
servers define a default authentication plugin  
(see [mysqllink server-system-variables.html#sysvar_authentication_policy `authentication_policy`] and  
[mysqllink server-system-variables.html#sysvar_default_authentication_plugin  
`default_authentication_plugin`]). The default plugin will  
be used for newly created users, and may affect how the handshake works.  
```
