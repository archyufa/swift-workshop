# Swift most used/popular features

##ACL

** Please note: ** that ACL applied on Container level and not per object!

### Create Publicly readable Container

Prepare files:

```
echo "some text" > 1.txt
swift post text
swift upload --object-name 1.txt text 1.txt
```

Create read-only ACL for container:

```
swift post pictures -r ".r:*"
```

This allows anybody to download the objects inside container.
However, to download an object, the exact URL path to object needs to be known,
since user cannot list the objects in the container.


Verify Read ACL:

```
swift stat text
```

Find StorageURL:

```
swift stat -v
```

Build container path (StorageURL/Container_name):

```
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/text
```

Try the link in the browser:

```
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/text
```

__Result: Unauthorized__

Try the link for container + object (StorageURL/Container_name/Object):

```
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/text/1.txt
```

__Result: Success__

### Create Publicly browsable file in swift:

The following allows anybody to list objects in the container and download
objects. The users do not need to include a token in their request. This ACL
is commonly referred to as making the container “public”. It is useful when
used with StaticWeb:

```
swift post text -r ".r:*,.rlistings"
```

* Try the link with container path in the browser:

```
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/text
```

__Result: Success__



## ACL with Keystone:

```
-r
<project-id>:<user-id>
<project-id>:*
*:<user-id>
*:*
```

### Create 2 demo users and 2 demo projects and add swiftoperator role:

** Using openstack api: **

```
openstack project create demoproject1
openstack project create demoproject2
openstack user create --project demoproject1 --password nova demo2
openstack user create --project demoproject2 --password nova demo2
openstack role list # List all roles and locate swiftoperator or swiftuser role.
openstack user-role-add --user demo1 swiftoperator
openstack user-role-add --user demo2 swiftoperator
```

** Using keystone api: **

```
keystone tenant-create --name demoproject1
keystone tenant-create --name demoproject2
keystone user-create --tenant demoproject1 --name demo1 --pass nova
keystone user-create --tenant demoproject2 --name demo2 --pass nova
keystoner role list  # List all roles and locate swiftoperator or swiftuser role.
keystone user-role-add --user demo1 --role swiftoperator
keystone user-role-add --user demo2 --role swiftoperator
```

### Create file in demorproject1 with user demo1

```
source demo1rc
echo "This is ACL test with keystone" > acltest.txt
swift post aclcontainer
swift list
swift upload --object-name acltest.txt aclcontainer acltest.txt
swift stat -v # Note the Storage URL path for this object
```

### Test that user demo2 in demoproject2 cannot access the file:

Switch to demo2 user and fetch the X-Auth-Token:

```
swift stat -v
```

Try access the file with StorageURL of shared object and token with demo2 user.

```
curl -i <StorageURL+container>/container/object>  -H "X-Auth-Token: "

or 

swift download aclcontainer acltest.txt --os-storage-url <STORAGE_URL_PATH>

```

__Result: Failed__

### Share aclcontainer with demoproject2/demo2 in tenant both wrights and reads:

```
swift post -r 'demoproject2:demo2' aclcontainer
swift post -w 'demoproject2:demo2' aclcontainer
```

Try access the file with StorageURL of shared object and token with demo2 user.

```
export OS_USERNAME=demo2
export OS_TENANT_NAME=demoproject2
export OS_PASSWORD=nova
export OS_AUTH_URL=http://openstack.domain.com:5000/v2.0
swift download aclcontainer acltest.txt --os-storage-url <STORAGE_URL_PATH>
```

_OR using Curl_
```
curl -i <StorageURL+container>/container/object>  -H "X-Auth-Token: "

```

__Result: Success__


### You can simplify access to the shared container by adding STORAGE_URL to the 
openrc file.

```
export OS_STORAGE_URL="http://openstack.domain.com:8080/v1/AUTH_e2e476b96336840e5f82f928a815805d"
swift list aclcontainer
swift download aclcontainer 
```

## A complex ACL set

More complex Swift ACLs can be constructed with wildcards and comma-separated lists.

```
# give r/w access every version of my account and to user leslie in
# project Gamma. give read-only access to user joebob in project
# Beta and everyone in project Gamma
swift post -r '*:memyself,Beta:joebob,Beta:leslie,Gamma:*' AlphaContainer
swift post -w '*:memyself,Gamma:leslie' AlphaContainer
```



# TempURL

## Make a Tempurl of image/document/template.

This procedure can be is used when an object stored in Swift must be
accessible with a URL
for specific amount of time. Example fetching template, proving coupon
discount, providing a license that will expire after some
time.

### Verify Temp-Url-Key exists, if not create one.

Meta Temp-Url-Key Allows the creation of URLs to provide temporary access to
objects
Check if Meta Temp-Url-Key exist:

```
$ swift stat
                        Account: AUTH_cca
                     Containers: 694
                        Objects: 4732
                          Bytes: 22827658656280
Containers in policy "policy-0": 694
   Objects in policy "policy-0": 4732
     Bytes in policy "policy-0": 22827658656280
              Meta Temp-Url-Key: U**************************************=
                    X-Timestamp: 1407942117.69837
                     X-Trans-Id: tx0c8721bec4fd4c7f87d57-0056f02c03
                   Content-Type: text/plain; charset=utf-8
                  Accept-Ranges: bytes
```

If the line Meta Temp-Url-Key is not present, create a secured Key and set
this metadata value:


```
$ export TEMP_KEY=$(openssl rand -base64 32)
$ swift post -m "Temp-URL-Key:$TEMP_KEY"
$ unset TEMP_KEY
```

You should now see the Meta Temp-Url-Key line:

```
$ swift stat
```

### Create a tempurl

```
touch templicense.txt
swift post container1
swift upload --object-name templicense.txt container1 templicense.txt

```

Create a tempurl with: 

```
swift-temp-url <METHOD> <DURATION_IN_SEC>
/v1/[AUTH_]<ACCOUNT>/<CONTAINER>/<OBJECT> <TEMP_URL_KEY>
```

Example of swift-temp-url creation

```
$ swift tempurl GET 3600
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/container1/templicense.txt
q+0mCAHnwmZ/Tj5h9quKnu6/OTatAY0ztBU+pKz2R7Y=

Return:

http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/container1/templicense.txt?temp_url_sig=6e9f6dc1fc74b961d83d91d973989bb3b18b2b04&temp_url_expires=1486758120
```

This will create a temporary url valid for 3600 seconds and only accessible
with the GET method.

Try URL in browser or fetch it with curl.

### Following features has been discussed in Workshop N1 can be found below:

[Uploading Large Files(SLO)](https://github.com/archyufa/swift-workshop/blob/master/session2/features.txt#L1)

[Object expiration](https://github.com/archyufa/swift-workshop/blob/master/session2/features.txt#L127)

### Following features executions steps can be found here:

[Object Versioning](http://docs.openstack.org/developer/swift/overview_object_versioning.html)

[Server Side Copy](http://developer.openstack.org/api-ref/object-storage/index.html)




