## IBM Cloud Activity Tracker with LogDNA for IBM Cloud Object Storage
See https://www.ibm.com/cloud/blog/announcements/ibm-cloud-activity-tracker-with-logdna-for-ibm-cloud-object-storage

To enable logging for an IBM Cloud Object Storage service:

### Step 1
(1) Create a Cloud Object Storage instance: 
https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-provision#provision-instance.

(2) Create a bucket within the Cloud Object Storage instance.

(3) Enable Activity Monitoring at the bottom of the configuration dialog:

If an instance of **IBM Cloud Activity Tracker with LogDNA** is provisioned in the correlated location, it will default to using that instance:
![image](https://media.github.ibm.com/user/158291/files/0fc02c00-deda-11e9-8f04-f182b0ba2c72) 

If **IBM Cloud Activity Tracker with LogDNA** is not yet provisioned, it will prompt you to create an instance in the correlated location as you are creating your bucket:
![image](https://media.github.ibm.com/user/158291/files/5d15c980-dfbe-11e9-8979-8cff1067244b)

Or configure an existing bucket to enable monitoring:
https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-at

**NOTE:** See this page for details on which Activity Tracker service location that will be correlated to your Cloud Object Storage bucket:
https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-service-availability#service-availability-geo

### Step 2
Provision an instance of **IBM Cloud Activity Tracker with LogDNA**

https://cloud.ibm.com/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-getting-started#gs_step1

*NOTE:* There is 1 instance per region allowed to be provisioned.

### Step 3
Manage access to Activity Tracker service:

https://cloud.ibm.com/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-getting-started#gs_step2

*NOTE:* To grant administration permissions to manage the IBM Cloud Activity Tracker with LogDNA service, a user needs the following roles:

- Platform role: Administrator
- Service role: Manager

*NOTE:* To grant user permissions to manage the IBM Cloud Activity Tracker with LogDNA service, a user needs the following roles:

- Platform role: Viewer
- Service role: Reader

### Step 4
Launch the web UI console for IBM Activity Tracker service

https://cloud.ibm.com/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-getting-started#gs_step4

### Step 5
Select "LIVE" option at the bottom right of the Activity Tracker console UI.

### Step 6
You can narrow down the events by selecting "cloud-object-storage" in the "All Sources" dialog. This will give you all events for this service type.

You can get your specific instance by getting the ID number:
```
ibmcloud resource service-instance <service name>
```
then searching on the ID number in the search field of the "All Apps" dialog and selecting your instance.

After some time, the COS events will show up.  You may have to refresh the UI to see the events. 

Try uploading a file into the COS bucket, then refresh the UI to see the "Cloud Object Storage: create object" event.

To isolate Cloud Object Storage events, open the "All Sources" menu from top menu bar and select cloud-object-storage.
### Events
To understand events operations go to https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events.

#### Event Types
Object Storage [global events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-global) are forwarded to the **IBM Cloud Activity Tracker with LogDNA** service instance that is located in Frankfurt.
- List the buckets
- Create a bucket
- Delete a bucket

Object Storage [management events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-mngt) are forwarded to the **IBM Cloud Activity Tracker with LogDNA** instance that is associated with the bucket. To view these events, you must access the web UI of the **IBM Cloud Activity Tracker with LogDNA**  instance in the location that is associated with the bucket.
- [Resource configuration events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-mngt-1)
- [Bucket events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-mngt-2)
- [Object events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-mngt-3)

Object Storage [data events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-data) are forwarded to the **IBM Cloud Activity Tracker with LogDNA** instance that is associated with the bucket. To view these events, you must access the web UI of the **IBM Cloud Activity Tracker with LogDNA** instance in the location that is associated with the bucket.
- [Bucket access events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-data-1)
- [Object access events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-data-2)
- [Multipart events](https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-actions-data-3)

#### Querying events
To query on different event types, issue the query in the *search field* at the bottom of the UI. See https://cloud.ibm.com/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-views#views_step2 for more details.

To see a specific type of action, enter the format like:
```
action:<action name>
```
For instance, to see all the `info bucket` events enter the search string as
```
action:cloud-object-storage.bucket.info
```
Queries can be combined such as
```
responseData.serviceInstanceId:977ad335-788c-4287-bdcd-ae2d504bd78c AND action:cloud-object-storage.bucket-lifecycle.read
```
##### Examples of some queries

List object create actions for a particular bucket
```
<name of bucket> AND action:cloud-object-storage.object.create 
```

List all actions by a couple of users in a particular region that wasn't a failure
```
(initiator.name:Ann.Umberhocker@ibm.com OR initiator.name:Sowmya@ibm.com) AND responseData.bucketLocation:us-east AND -failure 
```
List any actions in buckets located in us-east, us-south that resulted in failure
```
responseData.bucketLocation:us-east OR responseData.bucketLocation:us-south AND failure 
```
In *timeframe field* (next to the *search field*), you can specify a period of time using several formats

Absolute time: 
- 3 days ago
- yesterday 10 am to yesterday 11 am
- Sept 28 at 12 pm

Time frame:
- 9/28 12 pm - 10/1 12 pm

#### Analyzing events
To expand an event, click on the arrow that appears when hovering over the far left margin of the event.  See https://cloud.ibm.com/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-at-events#at-events-analyze for more details on how to interpret this information. 

You can hover over the field data, select it and click the +, -, magnifying glass to add queries on that data in the "search" field at the bottom of the UI. 

### Additional features:
- Creating Custom Views:
https://cloud.ibm.com/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-views#views_step2

### Pricing Plans
- Cloud Object Storage
![image](https://media.github.ibm.com/user/158291/files/b1984680-dee3-11e9-9124-031fbeb9c643)
![image](https://media.github.ibm.com/user/158291/files/3f276680-dee3-11e9-986e-dcbfdbe21459)
- Activity Tracker with LogDNA
![image](https://media.github.ibm.com/user/158291/files/7dbd2100-dee3-11e9-812e-e40f5d118d5a)
