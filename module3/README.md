# Swift most used/popular features

##ACL

** Please note: ** that ACL applied on Container level and not per object!
### Create Publicly readable Container

The following allows anybody to upload or download objects. However, to
download an object, the exact name of the object must be known since users
cannot list the objects in the container.

```
echo "some text" > 1.txt
swift post text
swift upload --object-name 1.txt text 1.txt
```

Create read-only ACL for container:

```
swift post pictures -r ".r:*"
```

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

Result: Unauthorized

Try the link for container + object (StorageURL/Container_name/Object):

```
http://swiftstack-objects.staging.cloud.ca/v1/AUTH_ayrat/text/1.txt
```

Result: Success

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


Result: Success

### ACL with Keystone related:

```
-r
<project-id>:<user-id>
<project-id>:*
*:<user-id>
*:*
```

** Create file that shared with tenant or user in tenant





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




