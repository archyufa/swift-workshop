#ACL Workshop


## Recap of class:

The ACL let the account (or tenant) owner set R/W access rights for authenticated users or even unauthenticated access.
The later is really cool to configure data access for anyone.

* Account-Level Access Control:

 * Read-only
 * Read-write
 * Admin

* Container-Level Access Control (ACLs )

 * Read & Write ACLs, set with swift post -r/-w
 
 * Referrer:

   * .r:* (all referrers) 
   * .r:.allowed.com (only from allowed.com)
   * .r:-.not-allowed.com (not from not-allowed.com)  
   * user1 (give access to the user1 (V1))  
   * user1:tenant1 (give access to the user1 in tenant1 (v2))  
   * tenant1 (give access to the tenant1 (v2))
   

## Task 1: Create publicly avaialble contaner with pictures.

### Upload download.png to acltest container:
``
swift post acltest
swift list
swift upload --object-name swiftstack.png acltest acl_files/download.png
``

### Create READ ACL with capability to list Containers to all reffers for acltest: container.

``
swift post -r '.r:*,.rlistings' acltest
``

``
swift stat acltest
``

### Try to list pictures from outside of cluster:
``
curl -i http://172.16.21.165/v1/AUTH_ayrat/acltest
``

### Download image from acltest container:
``wget http://172.16.21.165/v1/AUTH_ayrat/acltest/swiftstack.png``

## Task 2: Create publicly avaialble contaner with pictures.

