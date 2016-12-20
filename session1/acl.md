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
   
   * demo1 (give access to the user1 (V1))
   
   * demo1:testtenant (give access to the user1 in tenant1 (v2))
   
   * testtenant (give access to the tenant1 (v2))

### Prerequisite: Setup 2 users and 1 tenant in keystone.
In Keystone create a user ``demo1`` and ``demo2`` in ``testtenant`` tenant.

``keystone tenant-create --name demo1`` 
``keystone user-create --tenant testtenant --name demo1 --pass nova``
``keystone user-create --tenant testtenant --name demo2 --pass nova``

Or alternatively use ``openstack`` API client in keys using Keystone V3.

### Task 1 Create acltest container and upload download.png into it:

``source demo1rc``
``swift post acltest``
``swift list``
``swift upload --object-name swiftstack.png acltest acl_files/download.png``

### Task 1: Create read/write access to the demo2 in tenant1.

### Task 1: Create read/write access to the all users in tenant1.

### Task 3 Create Publicly accessable container ``acltest``.
**Action:**

``swift post -r '.r:*' acltest``

**Result:**

Download image from anywhere acltest container:

``wget http://172.16.21.165/v1/AUTH_ayrat/acltest/swiftstack.png``

At this point it is not possible to list or see stat of this container:

``swift stat acltest`` # Stat

``curl -i http://172.16.21.165/v1/AUTH_ayrat/acltest`` # List 


### Task 4 Allow container listing, i.e. being able to list objects in a container and retrieving container stats.
**Action:**

``swift post -r '.r:*,.rlistings' acltest``

**Result:**
You can now download, list and see stat of container:

``wget http://172.16.21.165/v1/AUTH_ayrat/acltest/swiftstack.png`` # Download

``swift stat acltest`` # Stat

``curl -i http://172.16.21.165/v1/AUTH_ayrat/acltest`` # List 


### Task X: Give r/w access every version of my account/tenant, to user leslie in
project Gamma. Give read-only access to user joebob in project Beta and everyone in project Gamm.
swift post -r '*:memyself,Beta:joebob,Beta:leslie,Gamma:*' AlphaContainer
swift post -w '*:memyself,Gamma:leslie' AlphaContainer

