# 新浪云存储API文档
------
## 概述
> 我们提供了一套兼容AmazonS3的RESTful API，可以使您更加自由地开发出灵活的功能。
>
> 新浪云存储服务主要提供以下三类API：
>
> * Service操作
> * Bucket操作
> * Object操作
>
> 与此同时，为提高用户使用的安全性，新浪云存储服务还通过使用签名来验证请求者的身份。
> 
如需了解签名算法的详细信息，请参考[《签名算法》][1]。
> 
> 如需了解ACL的详细信息，请参考[《ACL》][2]。
>
> 如涉及Bucket和Object的命名规划，请参考[《约束与限制》][3]。
>
> ***注意：以下接口中所使用的示例都是在需要使用签名情况下；如果相关访问资源已设置为可匿名（所有用户）访问，则可不带签名。***

## 目录：

### [Service操作](#service操作-1)
* [GET Service (List Buckets)](#get-service-list-buckets)

### [Bucket操作](#bucket操作-1)
* [GET Bucket (List Objects)](#get-bucket-list-objects)
* [GET Bucket - Meta](#get-bucket---meta)
* [PUT Bucket](#put-bucket)
* [DELETE Bucket](#delete-bucket)
* [GET Bucket ACL](#get-bucket-acl)
* [PUT Bucket ACL](#put-bucket-acl)

### [Object操作](#object操作-1)
* [HEAD Object](#head-object)
* [GET Object](#get-object)
* [PUT Object](#put-object)
* [POST Object](#post-object)
* [PUT Object - Copy](#put-object---copy)
* [PUT Object - Relax](#put-object---relax)
* [PUT Object - Meta](#put-object---meta)
* [GET Object - Meta](#get-object---meta)
* [DELETE Object](#delete-object)
* [GET Object ACL](#get-object-acl)
* [PUT Object ACL](#put-object-acl)
* [Initiate Multipart Upload](#initiate-multipart-upload)
* [Upload Part](#upload-part)
* [Complete Multipart Upload](#complete-multipart-upload)
* [List Parts](#list-parts)


------

## Service操作

### GET Service (List Buckets)
 - 描述：获得当前Owner下所有Bucket的列表。
 - 请求格式：
```http
GET /?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
或者
```http
GET /<Your-Bucket-Name>/?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应格式（HTTP Body）：
```json
{
    "Owner": {
    
        "ID": "SINA0000001234567890"
    },
    
    "Buckets": [
    
        {
            "ConsumedBytes": 13243241,
            "CreationDate": "Fri, 21 Mar 2014 01:13:42 UTC",
            "Name": "bucket_name_0"
        },
        
        {
            "ConsumedBytes": 4323,
            "CreationDate": "Fri, 12 Mar 2013 02:25:22 UTC",
            "Name": "bucket_name_1"
        },
        
        ...
    ]
    
}
```
 - 返回值说明：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>Owner</td>
            <td>所有者</td>
          </tr>
          
          <tr>
            <td>Owner.ID</td>
            <td>所有者的UserId</td>
          </tr>
          
          <tr>
            <td>Buckets</td>
            <td>Buckets列表，下一级为数组</td>
          </tr>
          
          
          <tr>
            <td>Buckets.Name</td>
            <td>Bucket名称</td>
          </tr>
          
          <tr>
            <td>Buckets.ConsumedBytes</td>
            <td>当前Bucket已占用空间</td>
          </tr>
          
          <tr>
            <td>Buckets.CreationDate</td>
            <td>当前Bucket创建时间</td>
          </tr>
          
       
        </tbody>
</table>

 - 请求示例：
``` 
curl -v -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://sinastorage.cn/?formatter=json"
```
或者
```    
curl -v "http://sinastorage.cn/?KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```
 - 响应示例：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Owner": {
    
        "ID": "SINA0000001234567890"
    },
    
    "Buckets": [
    
        {
            "ConsumedBytes": 13243241,
            "CreationDate": "Fri, 21 Mar 2014 01:13:42 UTC",
            "Name": "bucket_name_0"
        },
        
        {
            "ConsumedBytes": 4323,
            "CreationDate": "Fri, 12 Mar 2013 02:25:22 UTC",
            "Name": "bucket_name_1"
        },
        
        ...
    ]
    
}
```

## Bucket操作

### GET Bucket (List Objects)

 - 描述：获取bucket下所有object。
 - 请求格式：

```http
GET /?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
或者
```php
GET /<Your-Bucket-Name>/?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 请求参数：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Parameter</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>delimiter</td>
            <td>折叠显示字符。通常使用：‘/’</td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>marker</td>
            <td>Key的初始位置，系统将列出比Key大的值，通常用作‘分页’的场景</td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>max-keys</td>
            <td>返回值的最大Key的数量。默认为400</td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>prefix</td>
            <td>列出以指定字符为开头的Key</td>
            <td>No</td>
          </tr>
          
       
        </tbody>
    </table>

响应格式举例（HTTP Body）：
```json
{

    Delimiter: "/",
    
    Prefix: "html/",
    
    CommonPrefixes: [
    
        {
            Prefix: "html/assets/"
        },
    
        {
            Prefix: "html/attributions/"
        },
    
        {
            Prefix: "html/documentation/"
        },
    
        ...
    ],
    
    Marker: null,
    
    ContentsQuantity: 5,
    
    CommonPrefixesQuantity: 3,
    
    NextMarker: null,
    
    IsTruncated: false,
    
    Contents: [
    
        {
            SHA1: "9fc710aa89efbe42020b0310d16a07449bf06131",
            Name: "html/coming-soon.html",
            Expiration-Time: null,
            Last-Modified: "Fri, 21 Mar 2014 01:50:46 UTC",
            Owner: "SINA0000000000000001",
            MD5: "934d922cac80449ee361cefeb3276b3e",
            Content-Type: "text/html",
            Size: 8781
        },
    
        {
            SHA1: "a9625a128263f05e331f6d78add9bd15911c3565",
            Name: "html/ebook.html",
            Expiration-Time: null,
            Last-Modified: "Fri, 21 Mar 2014 01:50:47 UTC",
            Owner: "SINA0000000000000001",
            MD5: "cb7ed943ead4aeb513aa8c0b76865a8b",
            Content-Type: "text/html",
            Size: 18734
        },
        
        ...
    ]
}
```
 - 返回值说明：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>Contents</td>
            <td>Object的Metadata数组</td>
          </tr>
          
          <tr>
            <td>CommonPrefixes</td>
            <td>折叠以后的Prefix，下一级是Prefix数组</td>
          </tr>
          
          <tr>
            <td>Delimiter</td>
            <td>当前使用的折叠字符</td>
          </tr>
          
          <tr>
            <td>Prefix</td>
            <td>当前使用的前缀</td>
          </tr>
          
          <tr>
            <td>Marker</td>
            <td>当前使用的Marker</td>
          </tr>
          
          <tr>
            <td>ContentsQuantity</td>
            <td>Contents中元素个数</td>
          </tr>
          
          <tr>
            <td>CommonPrefixesQuantity</td>
            <td>CommonPrefixes中元素个数</td>
          </tr>
          
          <tr>
            <td>NextMarker</td>
            <td>下一页的Marker</td>
          </tr>
          
          <tr>
            <td>IsTruncated</td>
            <td>是否还有下一页</td>
          </tr>
          
          <tr>
            <td>SHA1</td>
            <td>文件内容的sha1值</td>
          </tr>
          
          <tr>
            <td>Name</td>
            <td>Object的Key(文件名)</td>
          </tr>
                    
          <tr>
            <td>Last-Modified</td>
            <td>Object的最后修改时间</td>
          </tr>
          
          <tr>
            <td>Owner</td>
            <td>Object的拥有者</td>
          </tr>
          
          <tr>
            <td>MD5</td>
            <td>文件内容的md5值</td>
          </tr>
          
          <tr>
            <td>Content-Type</td>
            <td>文件的mime type</td>
          </tr>
          
          <tr>
            <td>Size</td>
            <td>文件的大小(字节)</td>
          </tr>
          
        </tbody>
    </table>

 - 应用举例：
假设某Bucket下有如下文件(为方便说明，没有显示为json格式，仅表现其中的一些有用信息，以下同)：
```
join/mailaddresss.txt
join/mycodelist.txt
join/personalfiles/connects.docx
join/personalfiles/myphoto.jpg
join/readme.txt
join/userlist.txt
join/zero.txt
mary/personalfiles/mary.jpg
mary/readme.txt
sai/readme.txt
```
使用prefix指定以join/为开头的文件：
```http
GET /?prefix=join/&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
返回：
```
Contents:
    join/mailaddresss.txt
    join/mycodelist.txt
    join/personalfiles/connects.docx
    join/personalfiles/myphoto.jpg
    join/readme.txt
    join/userlist.txt
    join/zero.txt
```
使用delimiter指定折叠方式为‘/’：
```http
GET /?delimiter=/&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
返回：
```
Contents:

CommonPrefix:
    join
    mary
    sai
```
使用prefix指定以join/为开头的文件，同时使用delimiter指定折叠方式为‘/’：
```http
GET /?prefix=join/&delimiter=/&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
返回：
```
Contents:
    join/mailaddresss.txt
    join/mycodelist.txt
    join/readme.txt
    join/userlist.txt
    join/zero.txt
CommonPrefix:
    join/personalfiles/
```
使用max-keys做最大值列表长度限制：
```http
GET /?prefix=join/&delimiter=/&max-keys=4&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
返回：
```
IsTruncated : true
Next-Marker : join/userlist.txt
Contents:
    join/mailaddresss.txt
    join/mycodelist.txt
    join/readme.txt
CommonPrefix:
    join/personalfiles/
```
使用marker继续获得之前的列操作的后续结果：
```http
GET /?prefix=join/&delimiter=/&max-keys=4&marker=join/userlist.txt&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
返回：
```
IsTruncated : false
Contents:
    join/userlist.txt
    join/zero.txt
```

### GET Bucket - Meta
 - 描述：获取一个Bucket的meta信息。
 - 请求格式：
```http
GET /?meta&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 1234
Content-Type: application/json
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
	DeleteQuantity: 71,
	
	Capacity: 2843723,
	
	ACL: {
	
		GRPS000000ANONYMOUSE: ["read"],
		
		GRPS0000000CANONICAL: [ ],
		
		SINA0000001001HBK3UT: ["read", "write", "read_acp", "write_acp"]
	},
		
	ProjectID: 4241,
	
	DownloadQuantity: 93,
	
	DownloadCapacity: 27365564,
		
	CapacityC: 0,
	
	QuantityC: 0,
	
	Project: "Your-Bucket-Name",
	
	UploadCapacity: 9112891,
	
	UploadQuantity: 80,
	
	Last-Modified: "Wed, 16 Apr 2014 13:49:38 UTC",
	
	SizeC: 0,
	
	Owner: "SINA0000001001HBK3UT",
	
	DeleteCapacity: 6269168,
	
	Quantity: 9
}
```
 - 请求示例：
```
curl -v -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/?meta&formatter=json"
```
### PUT Bucket
 - 描述：创建一个Bucket。
 - 请求格式：
```http
PUT /?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
x-amz-acl: <Canned-ACL> #请参照《ACL》
```
或者
```http
PUT /<Your-Bucket-Name>/?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
x-amz-acl: <Canned-ACL> #请参照《ACL》
```
 - Request Header（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>x-amz-acl</td>
            <td>创建Bucket的同时，设置一个ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》</a></td>
            <td>No</td>
          </tr>
                    
        </tbody>
    </table>

 - 响应（无HTTP Body）：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Length: 0
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - 请求示例：
```
curl -v -X PUT -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/?formatter=json"
```
或者
```
curl -v -X PUT "http://<Your-Bucket-Name>.sinastorage.cn/?KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```
或者
```
curl -v -X PUT "http://sinastorage.cn/<Your-Bucket-Name>/?KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```

### DELETE Bucket
 - 描述：删除指定Bucket。
 - ***注意：不能删除非空Bucket。***
 - 请求格式：
```http
DELETE /?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
或者
```http
DELETE /<Your-Bucket-Name>/?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应（无HTTP Body）：
```http
HTTP/1.1 204 No Content
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Length: 0
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
请求示例：
```
curl -v -X DELETE -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/?formatter=json"
```
或者
```
curl -v -X DELETE "http://<Your-Bucket-Name>.sinastorage.cn/?KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```
或者
```
curl -v -X DELETE "http://sinastorage.cn/<Your-Bucket-Name>/?KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```

### GET Bucket ACL
 - 描述：获得指定Bucket的ACL信息。更多信息请参照：[《ACL》][2]
 - 请求格式：
```http
GET /?acl&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
或者
```http
GET /<Your-Bucket-Name>/?acl&formatter=json HTTP/1.1
Host: sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Length: 123
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Owner": "SINA0000000000000001",
    
    "ACL": {
    
        "GRPS000000ANONYMOUSE": [
        
        	"read"
        ],
    
        "SINA0000001001NHT3M7": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "SINA0000000000000001": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "SINA0000001001HBK3UT": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "GRPS0000000CANONICAL": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
        
        ...
    }
}
```
 - 响应格式说明请参照：[《ACL》][2]
 - 请求示例：
```
curl -v -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/?acl&formatter=json"
```
或者
```
curl -v "http://<Your-Bucket-Name>.sinastorage.cn/?acl&KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```
或者
```
curl -v "http://sinastorage.cn/<Your-Bucket-Name>/?acl&KID=sina,<access_key>&Expires=1398873316&ssig=<ssig>&formatter=json"
```  

### PUT Bucket ACL
 - 描述：给指定Bucket设置ACL规则。更多信息请参照：[《ACL》][2]
 - 请求格式：
```http
PUT /?acl&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》

#ACL规则
{  
	'SINA0000000000000001' :  [ "read", "read_acp" , "write", "write_acp" ],
	'GRPS000000ANONYMOUSE' :  [ "read", "read_acp" , "write", "write_acp" ],
	'GRPS0000000CANONICAL' :  [ "read", "read_acp" , "write", "write_acp" ],
}
```
 - 响应（无HTTP Body）：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - 请求格式说明请参照：[《ACL》][2]
 - 请求示例：
```
curl -v -T "acl.txt" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/?acl&formatter=json"
```

## Object操作

### HEAD Object
 - 描述：使用HEAD请求方式获取Object的Metadata。
 - 请求格式：
```http
HEAD /<ObjectName>?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应（无HTTP Body）：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Type: <object-mime-type>
Content-Length: <object-file-bytes>
ETag: "<文件的MD5值>"
Last-Modified: <最后修改时间>
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
x-amz-meta-foo1: <value1> #自定义meta：foo1
x-amz-meta-foo2: <value2> #自定义meta：foo2
```
 - 标准Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>Range</td>
            <td>
            	Downloads the specified range bytes of an object. For more information about the HTTP Range header, go to http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35.<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Modified-Since</td>
            <td>
            	Return the object only if it has been modified since the specified time, otherwise return a 304 (not modified).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Unmodified-Since</td>
            <td>
            	Return the object only if it has not been modified since the specified time, otherwise return a 412 (precondition failed).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Match</td>
            <td>
            	Return the object only if its entity tag (ETag) is the same as the one specified; otherwise, return a 412 (precondition failed).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>  
            
          <tr>
            <td>If-None-Match</td>
            <td>
            	Return the object only if its entity tag (ETag) is different from the one specified; otherwise, return a 304 (not modified).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr> 
                    
        </tbody>
    </table>

 - Response Headers（响应头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Content-Type</td>
            <td>Object的mime-type</td>
		  </tr>
		  <tr>
            <td>Content-Length</td>
            <td>Object的Size(bytes)</td>
		  </tr>
		  <tr>
            <td>ETag</td>
            <td>Object的hash值，一般是md5值</td>
		  </tr>
		  <tr>
            <td>Last-Modified</td>
            <td>Object的最后修改时间</td>
		  </tr>
		  <tr>
            <td>x-amz-meta-*</td>
            <td>用户可自定义文件属性信息，读取时原值返回。<br>
            例如：<br>
            x-amz-meta-UploadLocation: My Home<br>
            X-amz-meta-ReviewedBy: test@test.net<br>
            X-amz-meta-FileChecksum: 0x02661779<br>
            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
		  </tr>
		  <tr>
            <td>x-amz-meta-crc32</td>
            <td>Object的CRC32值</td>
		  </tr>
        </tbody>
</table>

 - 请求示例：
```
curl -v -X HEAD -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/<Object-Name>?formatter=json"
```

### GET Object
 - 描述：获取一个Object（下载）。
 - 请求格式：
```http
GET /<ObjectName>?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
Range: bytes=<byte_range> #支持断点下载
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Type: <object-mime-type>
Content-Length: <object-file-bytes>
ETag: "<文件的MD5值>"
Last-Modified: <最后修改时间>
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
x-amz-meta-foo1: <value1> #自定义meta：foo1
x-amz-meta-foo2: <value2> #自定义meta：foo2

#文件内容
<BODY>
```
 - 标准Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>Range</td>
            <td>
            	Downloads the specified range bytes of an object. For more information about the HTTP Range header, go to http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35.<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Modified-Since</td>
            <td>
            	Return the object only if it has been modified since the specified time, otherwise return a 304 (not modified).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Unmodified-Since</td>
            <td>
            	Return the object only if it has not been modified since the specified time, otherwise return a 412 (precondition failed).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>If-Match</td>
            <td>
            	Return the object only if its entity tag (ETag) is the same as the one specified; otherwise, return a 412 (precondition failed).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr>  
            
          <tr>
            <td>If-None-Match</td>
            <td>
            	Return the object only if its entity tag (ETag) is different from the one specified; otherwise, return a 304 (not modified).<br>
            	• Type: String<br>
				• Default: None<br>
				• Constraints: None
            </td>
            <td>No</td>
          </tr> 
                    
        </tbody>
</table>

 - Response Headers（响应头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Content-Type</td>
            <td>Object的mime-type</td>
		  </tr>
		  <tr>
            <td>Content-Length</td>
            <td>Object的Size(bytes)</td>
		  </tr>
		  <tr>
            <td>ETag</td>
            <td>Object的hash值，一般是md5值</td>
		  </tr>
		  <tr>
            <td>Last-Modified</td>
            <td>Object的最后修改时间</td>
		  </tr>
		  <tr>
            <td>x-amz-meta-*</td>
            <td>用户可自定义文件属性信息，读取时原值返回。<br>
            例如：<br>
            x-amz-meta-UploadLocation: My Home<br>
            X-amz-meta-ReviewedBy: test@test.net<br>
            X-amz-meta-FileChecksum: 0x02661779<br>
            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
		  </tr>
		  <tr>
            <td>x-amz-meta-crc32</td>
            <td>Object的CRC32值</td>
		  </tr>
        </tbody>
</table>

 - 请求示例：
```
curl -v -H "Range: bytes=0-1024" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/<Object-Name>?formatter=json"
```
 - 应用举例：
  * 标准示例：
```http
GET /my_bucket/path/to/my/file.txt?formatter=json HTTP/1.1
Host: sinastorage.cn
Date: Sun, 1 Jan 2006 12:00:00 GMT
Authorization: SINA AccessKey:ssig
Range: bytes=100-2048
```
 -  
  * 响应：
```http
HTTP/1.1 206 Partial Content
x-amz-id-2: id
x-amz-request-id: request_id
Date: date
Last-Modified: Sun, 1 Jan 2006 12:00:00 GMT
ETag: "etag"
Content-Length: length
Content-Type: type
Connection: close
Server: SinaS3
x-amz-meta-foo: foo_value

...
file_content
...
```
 -  
   * 使用各种验证措施的下载方式：
```http
GET /path/to/my/file.txt?KID=sae,youraccount&Expires=1175139620&ssig=your_ssig&ip=time,ipaddress&fn=filename.txt&formatter=json HTTP/1.1
Host: my_bucket.sinastorage.cn
Date: date
Range: bytes=byte_range
```
 -  
  * 响应：
```http
HTTP/1.1 206 Partial Content
x-amz-id-2: id
x-amz-request-id: request_id
Date: date
Last-Modified: Sun, 1 Jan 2006 12:00:00 GMT
ETag: "etag"
Content-Length: length
Content-Type: type
Connection: close
Server: SinaS3
x-amz-meta-foo: foo_value

...
file_content
...
```
***以上示例中QueryString的含义请参照[《签名算法》][1]中的关于 认证方式 的说明。***

### PUT Object
 - 描述：PUT方式上传一个文件（同时可以设置meta和acl）。
 - 请求格式：
```http
PUT /<ObjectName>?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Length: <object data length>
Content-Type: <mime-type>
Authorization: <authorization string> #请参照《签名算法》

[object data]
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 0
ETag: "<文件的MD5值>"
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>x-sina-expire</td>
            <td>
            	文件过期时间，到期系统将自动清除文件（非即时，清除时间不定期），格式为：Sat, 20 Nov 2286 17:46:39 GMT
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info</td>
            <td>
            	固定头meta信息，支持64位str值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info-int</td>
            <td>
            	固定头meta信息，支持int值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>Cache-Control</td>
            <td>
            	文件Cache，标准HTTP协议，更多内容参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Expires</td>
            <td>
            	文件在客户端或浏览器的缓存过期时间，允许客户端在这个时间之前不去服务端检查，读取时原值返回。格式为：Sun, 29 Jul 2018 20:36:14 UTC
            </td>
            <td>No</td>
          </tr>
            
          <tr>
            <td>Content-Type</td>
            <td>
            	文件mime type，读取时原值返回
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Length</td>
            <td>
            	文件大小，读取时原值返回
            </td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>Content-MD5</td>
            <td>
            	文件MD5（与传送内容不符时失败），注意：字符串格式为rfc标准使用base64编码的值
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>s-sina-sha1</td>
            <td>
            	文件SHA-1（与传送内容不符时失败），注意：字符串格式为hex编码的值
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Disposition</td>
            <td>
            	HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Encoding</td>
            <td>
            	文件编码，HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-acl</td>
            <td>
            	文件ACL：创建文件的同时，设置一个ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》</a>
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-meta-*</td>
            <td>
            	用户可自定义文件属性信息，读取时原值返回。<br>
	            例如：<br>
	            x-amz-meta-UploadLocation: My Home<br>
	            X-amz-meta-ReviewedBy: test@test.net<br>
	            X-amz-meta-FileChecksum: 0x02661779<br>
	            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
            <td>No</td>
          </tr> 
                    
        </tbody>
</table>
 - 请求示例：
```
curl -v -T "myfile.txt" -H "x-amz-meta-UploadLocation: My Home" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/myfile.txt?formatter=json"
```

### POST Object
 - 描述：POST方式上传一个文件（基于浏览器表单的上传方式）。
 - 请求格式：
```http
POST / HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Content-Length: <length>

Content-Type:multipart/form-data; boundary=----WebKitFormBoundary1dIjDASRYXQm6DNA

------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="key"

destinationProject/${filename}
------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="success_action_redirect"

http://123.abc.com/1.php?f=1111.txt
------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="AWSAccessKeyId"

100M414ZO0X30
------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="Policy"

eyJleHBpcmF0aW9uIjoiMjAxMi0wNi0wNlQwNjozOTo0MS4wMDBaIiwiY29uZGl0aW9ucyI6W3siYnVja2V0IjoiczN4cC5zM3dhdGNoIn0sWyJzdGFydHMtd2l0aCIsIiRrZXkiLCJhbmdlbFwvIl1dfQ==
------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="Signature"

VK6Kw4kRqW2e84ZIX2cV2QqHo58=
------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="file"; filename="112233.txt"
Content-Type: text/plain

------WebKitFormBoundary1dIjDASRYXQm6DNA
Content-Disposition: form-data; name="submit"

上传
------WebKitFormBoundary1dIjDASRYXQm6DNA-
```
 - 表单元素：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td width="185px">AWSAccessKeyId</td>
            <td>
            	就是AccessKey，可以到控制台获取
            </td>
            <td>Yes</td>
          </tr>    
            
          <tr>
            <td>key</td>
            <td>
            	object上传后的key（路径），例如：angel/${filename}，变量${filename}将被自动替换成被上传文件的文件名；当然也可以直接指定被上传文件存储在Sinastorage的文件名。如：angel/path/to/myfile.txt
            </td>
            <td>Yes</td>
          </tr>    
            
          <tr>
            <td>acl</td>
            <td>
            	文件的ACL：创建文件的同时，设置一个ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》
            </a></td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>success_action_status</td>
            <td>
            	上传成功后的响应码，通常设置为201
            </td>
            <td>No</td>
          </tr>  
            
          <tr>
            <td>success_action_redirect</td>
            <td>
            	上传成功后客户端重定向的URL。
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Policy</td>
            <td>
            	文件的策略，json格式字符串，并使用base64进行编码。后面详细介绍
            </td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>Signature</td>
            <td>
            	使用SecretKey签名后的字符串。后面详细介绍
            </td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>file</td>
            <td>
            	type=file的input表单
            </td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>x-amz-meta-*</td>
            <td>
            	用户可自定义文件属性信息，读取时原值返回。
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Cache-Control</td>
            <td>
            	文件的缓存策略(例如：max-age=31536000)，读取时原值返回。
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Expires</td>
            <td>
            	文件在客户端或浏览器的缓存过期时间，允许客户端在这个时间之前不去服务端检查，读取时原值返回。格式为：Sun, 29 Jul 2018 20:36:14 UTC
            </td>
            <td>No</td>
          </tr>
                    
        </tbody>
</table>

 - Policy的构建：
```json
{
    "expiration": "2014-04-10T08:55:34.000Z",

    "conditions": [

        {
            "bucket": "my-bucket-name"
        },

        {
            "acl": "private"
        },

        [
            "starts-with", "$key", "my_prefix/"
        ],

        [
            "content-length-range", 0, 52428800
        ]
    ]
}
```
 - 以上示例的说明：

  - 上传必须在”2014-04-10T08:55:34.000Z”之前。
  - 文件上传到名为”my-bucket-name”的bucket。
  - starts-with：$key必须以”my_prefix/”开始 (Policy中”$key”前必须带”$”)。若$key值为空，文件名前无前缀。
  - content-length-range：文件大小必须在指定范围内。
  - 最终将policy进行base64编码设置到表单Policy的value中。
  
 - Signature的构建：
  - 用UTF-8对policy进行编码
  - 用Base64对UTF-8形式的policy进行编码
  - 用HMAC SHA-1和你的Secret Key将你的policy进行转换。最后进行base64编码。如：使用php时，base64_encode( hash_hmac( "sha1", $policy, $SECRETKEY, true ) );
  - 将最终的值设置到表单Signature的value中。
 - 最终生成的html表单：
```html
<form method="post" action="http://my-bucket.sinastorage.cn/" enctype="multipart/form-data">
    <input type="hidden" name="AWSAccessKeyId" value="您的accesskey" />
    <input type="hidden" name="key" value="my_prefix/${filename}" />
    <input type="hidden" name="acl" value="private" />
    <input type="hidden" name="success_action_status" value="201" />
    <input type="hidden" name="Policy" value="eyJleHBpcmF0aW9uIjoiMjAxNC0wNC0xMFQwOToxNzozMC4wMDBaIiwiY29uZGl0aW9ucyI6W3siYnVja2V0IjoiY2xvdWQxMjMqwqwsdifsdGFydHMtd2l0aCIsIiRrZXkiLCJteWZpbGVzLyJdLFsic3RhcnRzLXdpdGgiLCIkQ29udGVudC1UeXBlIiwiIl0sWyJzdGFydHMtd2l0aCIsIiRDb250ZW50LURpc3Bvc2l0aW9uIiwiIl0seyJ1aWQiOiIxMjMifSxbImNvbnRlbnQtbGVuZ3RoLXJhbmdlIiwwLDUyNDI4ODAwXV19" />
    <input type="hidden" name="Signature" value="HnOSk3kfx5LFtn4CIiFcSglQUXc=" />
    <input type="file" name="file" />
    <input type="submit" value="上传" />
</form>
```
 - ***注意事项：***
  - POST请求后的uri只能是“/”
  - success_action_redirect：指定上传成功后客户端重定向的URL。
  - key：变量${filename}将被自动替换成被上传文件的文件名；当然也可以直接指定被上传文件存储在Sinastorage的文件名。
 - 您还可以参考PHP-SDK中的例子：https://github.com/SinaCloudStorage/SinaCloudStorage-SDK-PHP/blob/master/examples/example-form.php

### PUT Object - Copy
 - 描述：通过拷贝方式创建Object（不上传具体的文件内容。而是通过COPY方式对系统内另一文件进行复制）。
 - 请求格式：
```http
PUT /<ObjectName>?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
x-amz-copy-source: </source-bucket/source-object>
x-amz-metadata-directive: <metadata-directive>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 0
ETag: "<文件的MD5值>"
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>x-amz-copy-source</td>
            <td>
            	被copy的文件地址。格式：/source-bucket/source-object，需要整体进行urlencode编码.
            </td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>x-amz-metadata-directive</td>
            <td>
            	文件Meta以COPY方式填写还是REPLACE方式填写，以COPY方式时会使用源文件的meta而忽略本次上传的meta。格式：COPY | REPLACE
            </td>
            <td>No</td>
          </tr> 
        
          <tr>
            <td>x-sina-expire</td>
            <td>
            	文件过期时间，到期系统将自动清除文件（非即时，清除时间不定期），格式为：Sat, 20 Nov 2286 17:46:39 GMT
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info</td>
            <td>
            	固定头meta信息，支持64位str值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info-int</td>
            <td>
            	固定头meta信息，支持int值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>Cache-Control</td>
            <td>
            	文件Cache，标准HTTP协议，更多内容参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Expires</td>
            <td>
            	文件在客户端或浏览器的缓存过期时间，允许客户端在这个时间之前不去服务端检查，读取时原值返回。格式为：Sun, 29 Jul 2018 20:36:14 UTC
            </td>
            <td>No</td>
          </tr>
            
          <tr>
            <td>Content-Type</td>
            <td>
            	文件mime type，读取时原值返回
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Length</td>
            <td>
            	必须是0
            </td>
            <td>=0</td>
          </tr>
                             
          <tr>
            <td>Content-Disposition</td>
            <td>
            	HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Encoding</td>
            <td>
            	文件编码，HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-acl</td>
            <td>
            	文件ACL：创建文件的同时，设置一个ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》</a>
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-meta-*</td>
            <td>
            	用户可自定义文件属性信息，读取时原值返回。<br>
	            例如：<br>
	            x-amz-meta-UploadLocation: My Home<br>
	            X-amz-meta-ReviewedBy: test@test.net<br>
	            X-amz-meta-FileChecksum: 0x02661779<br>
	            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
            <td>No</td>
          </tr> 
                    
        </tbody>
</table>

 - 请求示例：
```
curl -v -X PUT -H "x-amz-copy-source: /bucket-123/path/to/file123.txt" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/myfile.txt?formatter=json"
```

### PUT Object - Relax
 - 描述：通过“秒传”方式创建Object（不上传具体的文件内容。而是通过SHA-1值对系统内文件进行复制）。
 - 请求格式：
```http
PUT /<ObjectName>?relax&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Length: 0
Content-Type: <mime-type>
s-sina-sha1:: <文件内容的sha1值>
s-sina-length: <要秒传文件的大小(bytes)>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 0
ETag: "<文件的MD5值>"
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>s-sina-sha1</td>
            <td>
            	文件SHA-1（系统内无此文件则返回失败）
            </td>
            <td>Yes</td>
          </tr>
          <tr>
            <td>s-sina-length</td>
            <td>
            	文件大小，读取时以Content-Length返回
            </td>
            <td>Yes</td>
          </tr>
        
          <tr>
            <td>x-sina-expire</td>
            <td>
            	文件过期时间，到期系统将自动清除文件（非即时，清除时间不定期），格式为：Sat, 20 Nov 2286 17:46:39 GMT
            </td>
            <td>No</td>
          </tr>    
          <tr>
            <td>x-sina-info</td>
            <td>
            	固定头meta信息，支持64位str值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info-int</td>
            <td>
            	固定头meta信息，支持int值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
          <tr>
            <td>Cache-Control</td>
            <td>
            	文件Cache，标准HTTP协议，更多内容参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9
            </td>
            <td>No</td>
          </tr>
          <tr>
            <td>Expires</td>
            <td>
            	文件在客户端或浏览器的缓存过期时间，允许客户端在这个时间之前不去服务端检查，读取时原值返回。格式为：Sun, 29 Jul 2018 20:36:14 UTC
            </td>
            <td>No</td>
          </tr>  
          <tr>
            <td>Content-Type</td>
            <td>
            	文件mime type，读取时原值返回
            </td>
            <td>No</td>
          </tr>
          <tr>
            <td>Content-Length</td>
            <td>
            	必须是0
            </td>
            <td>=0</td>
          </tr>
          <tr>
            <td>Content-Disposition</td>
            <td>
            	HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1
            </td>
            <td>No</td>
          </tr>
          <tr>
            <td>Content-Encoding</td>
            <td>
            	文件编码，HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11
            </td>
            <td>No</td>
          </tr>
          <tr>
            <td>x-amz-acl</td>
            <td>
            	文件ACL：创建文件的同时，设置一个ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》</a>
            </td>
            <td>No</td>
          </tr>
          <tr>
            <td>x-amz-meta-*</td>
            <td>
            	用户可自定义文件属性信息，读取时原值返回。<br>
	            例如：<br>
	            x-amz-meta-UploadLocation: My Home<br>
	            X-amz-meta-ReviewedBy: test@test.net<br>
	            X-amz-meta-FileChecksum: 0x02661779<br>
	            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
            <td>No</td>
          </tr> 
        </tbody>
</table>
 - 请求示例：
```
curl -v -X PUT -H "s-sina-sha1: 00fd4b4549a1094aae926ef62e9dbd3cdcc2e456" -H "s-sina-length: 1122" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/myfile.txt?relax&formatter=json"
```

### PUT Object - Meta
 - 描述：更新一个已经存在的文件的附加meta信息。
 - ***注意：这个接口无法更新文件的基本信息，如文件的大小和类型等。***
 - 请求格式：
```http
PUT /<ObjectName>?meta&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Length: 0
x-amz-meta-foo1: <foo1-value>
x-amz-meta-foo2: <foo2-value>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 0
ETag: "<文件的MD5值>"
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - Request Headers（请求头）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>x-sina-expire</td>
            <td>
            	更新文件过期时间，到期系统将自动清除文件（非即时，清除时间不定期），格式为：Sat, 20 Nov 2286 17:46:39 GMT
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info</td>
            <td>
            	固定头meta信息，支持64位str值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>x-sina-info-int</td>
            <td>
            	固定头meta信息，支持int值，读取时原值返回，列文件时原值返回
            </td>
            <td>No</td>
          </tr>    
            
          <tr>
            <td>Cache-Control</td>
            <td>
            	文件Cache，标准HTTP协议，更多内容参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Expires</td>
            <td>
            	文件在客户端或浏览器的缓存过期时间，允许客户端在这个时间之前不去服务端检查，读取时原值返回。格式为：Sun, 29 Jul 2018 20:36:14 UTC
            </td>
            <td>No</td>
          </tr>  
            
          <tr>
            <td>Content-Type</td>
            <td>
            	文件mime type，读取时原值返回
            </td>
            <td>No</td>
          </tr>
                  
          <tr>
            <td>Content-Disposition</td>
            <td>
            	HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>Content-Encoding</td>
            <td>
            	文件编码，HTTP标准文件属性信息，读取时原值返回。参见：http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.11
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-acl</td>
            <td>
            	文件ACL：更新ACL。请参照：<a href="http://open.sinastorage.com/?c=doc&a=guide&section=acl">《ACL》</a>
            </td>
            <td>No</td>
          </tr>
          
          <tr>
            <td>x-amz-meta-*</td>
            <td>
            	用户可自定义文件属性信息，读取时原值返回。<br>
	            例如：<br>
	            x-amz-meta-UploadLocation: My Home<br>
	            X-amz-meta-ReviewedBy: test@test.net<br>
	            X-amz-meta-FileChecksum: 0x02661779<br>
	            X-amz-meta-CheckSumAlgorithm: crc32<br>
            </td>
            <td>No</td>
          </tr> 
                    
        </tbody>
</table>
 - 请求示例：
```
curl -v -X PUT -H "x-amz-meta-UploadLocation: My Home" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/myfile.txt?meta&formatter=json"
```

### GET Object - Meta
 - 描述：获取一个已经存在的文件的附加meta信息。
 - 请求格式：
```http
GET /<ObjectName>?meta&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
Content-Length: 1234
Content-Type: application/json
ETag: "<文件的MD5值>"
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Info": "foo info!",
    
    "File-Name": "myfile.txt",
    
    "Info-Int": 1122,
    
    "Content-MD5": "050fdc0e690bfae7b29392f152bcf305",
    
    "Last-Modified": "Fri, 11 Apr 2014 02:50:31 UTC",
    
    "Content-SHA1": "7c483439a26140b163d82251860ec73d3824d6b0",
    
    "Owner": "SINA0000000000000001",
    
    "Type": "text/plain",
    
    "File-Meta": {
    
        "x-amz-meta-sex": "female",
    
        "x-amz-meta-age": "38",
    
        "x-amz-meta-crc32": "7D45CD11"
    },
    
    "Size": 12
}
```
 - 返回值说明：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>Info</td>
            <td>
            	创建object时设置的 x-sina-info 值。
            </td>
          </tr>
          
          <tr>
            <td>File-Name</td>
            <td>
            	object key （文件名）。
            </td>
          </tr>
          
          <tr>
            <td>Info-Int</td>
            <td>
            	创建object时设置的 x-sina-info-int 值。
            </td>
          </tr> 
          
          <tr>
            <td>Content-MD5</td>
            <td>
            	文件内容的 MD5 值。
            </td>
          </tr> 
          
          <tr>
            <td>Content-SHA1</td>
            <td>
            	文件内容的 SHA1 值。
            </td>
          </tr>
          
          
          <tr>
            <td>Last-Modified</td>
            <td>
            	文件的最后修改时间。
            </td>
          </tr>
          
          
          <tr>
            <td>Owner</td>
            <td>
            	文件的所有者。
            </td>
          </tr>
          
          <tr>
            <td>Type</td>
            <td>
            	文件的mime-type。
            </td>
          </tr>
          
          <tr>
            <td>File-Meta</td>
            <td>
            	创建文件时设置的x-amz-meta-*值，下一级为key-value数组。
            </td>
          </tr>    
          
          <tr>
            <td>Size</td>
            <td>
            	文件的大小（bytes）。
            </td>
          </tr>
          
        </tbody>
    </table>

 - 请求示例：
```
curl -v -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/myfile.txt?meta&formatter=json"
```

### DELETE Object
 - 描述：删除指定Object。
 - 请求格式：
```http
DELETE /<ObjectName>?formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应（无HTTP Body）：
```http
HTTP/1.1 204 No Content
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Length: 0
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - 请求示例：
```
curl -v -X DELETE -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/my/file.txt?formatter=json"
```

### GET Object ACL
 - 描述：获得指定Object的ACL信息。更多信息请参照：[《ACL》][2]
 - 请求格式：
```http
GET /<ObjectName>?acl&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Content-Length: 123
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Owner": "SINA0000000000000001",
    
    "ACL": {
    
        "GRPS000000ANONYMOUSE": [
        
        	"read"
        ],
    
        "SINA0000001001NHT3M7": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "SINA0000000000000001": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "SINA0000001001HBK3UT": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
    
        "GRPS0000000CANONICAL": [
            "read",
            "write",
            "read_acp",
            "write_acp"
        ],
        
        ...
    }
}
```
 - 响应格式说明请参照：[《ACL》][2]
 - 请求示例：
```
curl -v -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/my/file.txt?acl&formatter=json"
```

### PUT Object ACL
 - 描述：给指定Object设置ACL规则。更多信息请参照：[《ACL》][2]
 - 请求格式：
```htt[
PUT /<ObjectName>?acl&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》

#ACL规则
{  
	'SINA0000000000000001' :  [ "read", "read_acp" , "write", "write_acp" ],
	'GRPS000000ANONYMOUSE' :  [ "read", "read_acp" , "write", "write_acp" ],
	'GRPS0000000CANONICAL' :  [ "read", "read_acp" , "write", "write_acp" ],
}
```
 - 响应（无HTTP Body）：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - 请求格式说明请参照：[《ACL》][2]
 - 请求示例：
```
curl -v -T "acl.txt" -H "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/my/file.txt?acl&formatter=json"
```

### Initiate Multipart Upload

 - 描述：大文件分片上传初始化接口
 - ***注意：在初始化上传接口中要求必须进行用户认证，匿名用户无法使用该接口。在初始化上传时需要给定文件上传所需要的meta绑定信息，在后续的上传中该信息将被保留，并在最终完成时写入云存储系统。***
 - 请求格式：
```http
POST /<ObjectName>?multipart&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Type: <mime-type>
x-amz-meta-foo1: <value1> #自定义meta：foo1
x-amz-meta-foo2: <value2> #自定义meta：foo2
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Bucket": "<Your-Bucket-Name>",
    "Key": "<ObjectName>",
    "UploadId": "7517c1c49a3b4b86a5f08858290c5cf6"
}
```
 - 请求示例：
```
curl -v -X POST "Date: Sat, 20 Nov 2286 17:46:39 GMT" -H "Authorization: SINA <access_key>:<ssig>" "http://<Your-Bucket-Name>.sinastorage.cn/path/to/my/file.txt?multipart&formatter=json"
```

### Upload Part
 - 描述：上传分片接口
 - 请求格式：
```http
PUT /<ObjectName>?partNumber=<PartNumber>&uploadId=<UploadId>&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Length: <Content-Length>
Content-MD5: <Content-MD5>
Authorization: <authorization string> #请参照《签名算法》

...
file_content
...
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
Etag: <Etag>
```
- 请求参数(QueryString)：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Parameter</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>partNumber</td>
            <td>文件分片的序号，从1开始</td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>uploadId</td>
            <td>通过Initiate Multipart Upload（大文件分片上传初始化接口）获得的uploadId值</td>
            <td>Yes</td>
          </tr>
       
        </tbody>
    </table>
  - ***注意：分片数不能超过2048。***


### Complete Multipart Upload
 - 描述：大文件分片上传拼接完成接口（合并接口）
 - 请求格式：
```http
POST /<ObjectName>?uploadId=<UploadId>&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Content-Type: text/json
Authorization: <authorization string> #请参照《签名算法》

[
	{
		"PartNumber": 1,
		"ETag": "050fdc0e690bfae7b29392f152bcf301"
	},
	
	{
		"PartNumber": 2,
		"ETag": "050fdc0e690bfae7b29392f152bcf302"
	},
	
	{
		"PartNumber": 3,
		"ETag": "050fdc0e690bfae7b29392f152bcf303"
	},
	
	{
		"PartNumber": 4,
		"ETag": "050fdc0e690bfae7b29392f152bcf304"
	},
	
	{
		"PartNumber": 5,
		"ETag": "050fdc0e690bfae7b29392f152bcf305"
	},
	
	...
]
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId
```
 - 请求参数(QueryString)：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Parameter</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>uploadId</td>
            <td>通过Initiate Multipart Upload（大文件分片上传初始化接口）获得的uploadId值</td>
            <td>Yes</td>
          </tr>
       
        </tbody>
 </table>

 - Body内容说明（json格式）：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>PartNumber</td>
            <td>文件分片的序号</td>
            <td>Yes</td>
          </tr>
          
          <tr>
            <td>ETag</td>
            <td>通过Upload Part（上传分片接口）上传成功后返回的响应头中的Etag值</td>
            <td>Yes</td>
          </tr>
       
        </tbody>
</table>
  
### List Parts
 - 描述：列出已经上传的所有分块
 - 请求格式：
```http
GET /<ObjectName>?uploadId=<UploadId>&formatter=json HTTP/1.1
Host: <Your-Bucket-Name>.sinastorage.cn
Date: <date>
Authorization: <authorization string> #请参照《签名算法》
```
 - 响应：
```http
HTTP/1.1 200 OK
Date: Tue, 08 Apr 2014 02:59:47 GMT
Connection: keep-alive
X-RequestId: 00078d50-1404-0810-5947-782bcb10b128
X-Requester: Your UserId

{
    "Bucket": "<Your-Bucket-Name>",

    "Key": "<ObjectName>",

    "Owner": {
	
        "ID": "<ID>",
        "DisplayName": "<DisplayName>"
    },

    "Parts": [

        {
            "PartNumber": 1,
            "Last-Modified": "Wed, 20 Jun 2012 14:57:10 UTC",
            "ETag": "050fdc0e690bfae7b29392f152bcf301",
            "Size": 1024
        },
	
        {
            "PartNumber": 2,
            "Last-Modified": "Wed, 20 Jun 2012 14:57:10 UTC",
            "ETag": "050fdc0e690bfae7b29392f152bcf302",
            "Size": 1024
        },
	
        {
            "PartNumber": 3,
            "Last-Modified": "Wed, 20 Jun 2012 14:57:10 UTC",
            "ETag": "050fdc0e690bfae7b29392f152bcf303",
            "Size": 1024
        },
	
        ...
    ]
	
}
```
- 请求参数(QueryString)：
<table class="table table-condensed">
        <thead>
          <tr>
            <th>Parameter</th>
            <th>Description</th>
            <th>Required</th>
          </tr>
        </thead>
        <tbody>
        
          <tr>
            <td>uploadId</td>
            <td>通过Initiate Multipart Upload（大文件分片上传初始化接口）获得的uploadId值</td>
            <td>Yes</td>
          </tr>
       
        </tbody>
</table>


[1]: http://open.sinastorage.com/?c=doc&a=guide&section=sign
[2]: http://open.sinastorage.com/?c=doc&a=guide&section=acl
[3]: http://open.sinastorage.com/?c=doc&a=guide#limitations
