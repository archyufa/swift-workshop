# Swift Policies

## **Task:** Create Container and underlying Objects in Custom Policy.

Policy can be created according to certain criteria:

 * Protection schema (erasure or replica 2X or 3X or 4X)
 * Disk performance (Gold (SSD), Silver (15K), Bronze(10K))
 * According to geo location (e.g Region / Zone)

## Check which policies exist on the cluster

The Storage Policies configured for a Swift cluster can be fetched from
```swift info```
API Call or observed from SwiftStack UI.

```
swift info| grep policies
policies: [{u'name': u'global', u'aliases': u'global'}, {u'name': u'central',
u'aliases': u'central'}, {u'default': True, u'name': u'east', u'aliases':
u'east'}]
```

In the above example policy "east" is marked as default. It means when you
create a new container, without specifying policy, it will be placed under
"east" policy. 

## Create a container in Custom policy

To create a container with a Storage Policy - send the X-Storage-Policy header
and specify the name:

```
$ curl -H "X-Auth-Token: <X-AUTH-TOKEN>" -H "X-Storage-Policy: east" \
https://swift.example.com/v1/AUTH_test/mycontainer -
```

or using swift CLI:

```
swift post policytest -H "X-Storage-Policy: global"
```

From this point all objects created under policytest container will be under
global policy.

## Verify that policytest container has required policy.

```
swift stat policytest | grep Policy
```

