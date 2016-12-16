#ACL Workshop


## Recap of class:
* Account-Level Access Control:
    Read-only
    Read-write
    Admin

* Container-Level Access Control (ACLs )
** Read & Write ACLs, set with swift post -r/-w
** Referrer
.r:* (all referrers)
.r:.allowed.com (only from allowed.com)
.r:-.not-allowed.com (not from not-allowed.com)

* ACLs can be chained (last one wins)

* Account-Level Access Control:
    Read-only
    Read-write
    Admin

* Container-Level Access Control (ACLs )
** Read & Write ACLs, set with swift post -r/-w
** Referrer
.r:* (all referrers)
.r:.allowed.com (only from allowed.com)
.r:-.not-allowed.com (not from not-allowed.com)

* ACLs can be chained (last one wins)


## Task: Create publicly avaialble contaner with pictures.

### Upload download.png to acltest container:
``
swift post acltest
swift list
swift upload --object-name swiftstack.png acltest acl_files/download.png
``

### Create READ ACL with capability to list Containers to all reffers for
"acltest" container.
``
swift post -r '.r:*,.rlistings' acltest
swift stat acltest
         Account: AUTH_ayrat
       Container: acltest
         Objects: 1
           Bytes: 3681
        Read ACL: .r:*,.rlistings
       Write ACL:
         Sync To:
        Sync Key:
   Accept-Ranges: bytes
      X-Trans-Id: txb472785086d042d0b62e1-005853eed6
X-Storage-Policy: east
   Last-Modified: Fri, 16 Dec 2016 13:40:31 GMT
     X-Timestamp: 1481895306.82349
    Content-Type: text/plain; charset=utf-8
``

### Try to list pictures from outside of cluster:
``
curl -i http://172.16.21.165/v1/AUTH_ayrat/acltest
``

### Download image from acltest container:
``wget http://172.16.21.165/v1/AUTH_ayrat/acltest/swiftstack.png``
