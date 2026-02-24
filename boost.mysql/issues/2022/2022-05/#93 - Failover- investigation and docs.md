# #93 - Failover: investigation and docs [Open]

> Username: anarthal  
> Created at: 2022-05-19 21:09:38 UTC  
> Updated at: 2022-05-19 21:09:38 UTC  
> Url: https://github.com/boostorg/mysql/issues/93  

How does this library react in different failover scenarios? Provide a section in the docs.  
  
Possible scenarios:   
* InnoDB Cluster  
* InnoDB ReplicaSet  
* NDB Cluster  
* MySQL Router (with InnoDB Cluster or ReplicaSet behind)  
* Galera Cluster and MariaDB MaxScale
