# s3-bucket-version
[![CircleCI](https://circleci.com/gh/cortezcristian/s3-bucket-version/tree/master.svg?style=svg)](https://circleci.com/gh/cortezcristian/s3-bucket-version/tree/master)

S3 Bucket Version made easy

## Install

```bash
$ npm i s3-bucket-version --save
```

## Configure

```js
const AWSBucket = require('s3-bucket-version');

const bucket = new AWSBucket({
  accessKeyId: 'your-access-key-here',
  secretAccessKey: 'your-secret-here',
  region: 'us-east-1',
  bucketACL: 'public-read',
  bucketName: 'my-bucket'
});

```

## Usage

### Get Upload URL

Get upload URL

```js
bucket.getUploadUrl({
  ContentType: 'application/javascript',
  Key: 'your-dir/test.js'
}).then(function(res){
  /* Signed URL => res.signedUrl */
}).catch(function(err){
  /* err */
});

/*
Result:
{
  signedUrl: 'https://your-bucket.s3.amazonaws.com/your-dir/test.js?AWSAccessKeyId=...'
}
*/
```

### Upload File 

```js
bucket.uploadFile({
  filePath: './test/upload-test.txt',
  Key: 'upload-test.txt'
}).then(function(res){
  /* res.url => S3 upload url */
}).catch(function(err){
  /* err */
});

/*
 Result:
{ response: { ETag: '"abc.."' },
  url: 'https://my-bucket.s3.amazonaws.com/upload-test.txt' }
*/
```

### List Files

```js
bucket.listFiles().then(function(res){
  /* res.contents => bucket contents */
}).catch(function(err){
  /* err */
});

/*
Result:
{ IsTruncated: false,
  Contents:
   [ { Key: 'upload-test.txt',
       LastModified: 2018-04-15T22:48:27.000Z,
       ETag: '"abc..."',
       Size: 26,
       StorageClass: 'STANDARD' } ],
  Name: 'my-bucket',
  Prefix: '',
  MaxKeys: 1000,
  CommonPrefixes: [],
  KeyCount: 1 }
*/
```


### List File Versions

Calls `s3.listObjectVersions` for file path Prefix

```js
bucket.listFileVersions({
  Key: 'upload-test.txt'
}).then(function(res){
  /* res.Versions => Versions found */
}).catch(function(err){
  /* err */
});

/*
Result:
{ IsTruncated: false,
  KeyMarker: '',
  VersionIdMarker: '',
  Versions:
   [ { ETag: '"abc..."',
       Size: 44,
       StorageClass: 'STANDARD',
       Key: 'upload-test.txt',
       VersionId: 'null',
       IsLatest: true,
       LastModified: 2018-05-02T12:53:29.000Z,
       Owner: [Object] } ],
  DeleteMarkers: [],
  Name: 'your-bucket',
  Prefix: 'upload-test.txt',
  MaxKeys: 1000,
  CommonPrefixes: [] }
*/
```

### Delete Files 

```js
bucket.deleteFiles({
    files: ['upload-test.txt']
  }).then(function(res){
  /* res.Deleted => Deleted contents */
  done();
}).catch(function(err){
  /* err */
});

/*
Result:
{ Deleted: [ { Key: 'upload-test.txt' } ], Errors: [] }
*/
```

### Delete All Versions from a Single File

Delete all versions for a given file

```js
bucket.deleteAllVersions({
  Key: 'upload-test.txt',
}).then(function(res){
  /* res.Deleted => Deleted versions of single file */
  done();
}).catch(function(err){
  /* err */
});

/*
Result:
{ Deleted:
   [ { Key: 'upload-test.txt',
       VersionId: 'abc...' } ],
  Errors: [] }
*/
```

### Delete All Markers from a Single File

Delete all Markers for a given file

```js
bucket.deleteAllMarkers({
  Key: 'upload-test.txt',
}).then(function(res){
  /* res.Deleted => Deleted markers of single file */
}).catch(function(err){
  /* err */
});
```

### Delete All Versions and Markers from a Single File

Delete all Versions and Markers for a given file

```js
bucket.deleteAllVersionsAndMarkers({
  Key: 'upload-test.txt',
}).then(function(res){
  /* res.Deleted => Deleted versions and markers of single file */
}).catch(function(err){
  /* err */
});
```

### Delete Specific Versions from files

```js
bucket.deleteFilesVersioned({
  files: [{
      Key: 'upload-test.txt',
      // 'null' means latest version
      VersionId: 'null'
    }, {
      // you can repeat the same file
      Key: 'upload-test.txt',
      VersionId: 'abc...'
    }, {
      // several times
      Key: 'upload-test.txt',
      VersionId: 'def...'
    }, {
      // OR you can mix passing multiple files and versions
      Key: 'other-file.txt',
      VersionId: 'ghi...'
  }]
}).then(function(res){
  /* res.Deleted => Deleted versions and files specified */
  done();
}).catch(function(err){
  /* err */
});

/*
Result:
{ Deleted:
   [ { Key: 'upload-test.txt',
       VersionId: 'abc...' } ],
  Errors: [] }
*/
```

### Get All buckets

Get All buckets for this account

```js
bucket.getAllBuckets().then(function(res) {
  /* Buckets => res.Buckets */
}).catch(function(err) {
  /* err */
});

/*
Result:
{
  Buckets:
   [ { Name: 'my-bucket',
       CreationDate: 2018-03-19T17:49:05.000Z } ],
  Owner:
   { DisplayName: 'cris',
     ID: '...' }
}
*/

```


## References

- [AWS Docs: listObjectVersions](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#listObjectVersions-property)
- [Delete all versions of all files in s3 versioned bucket using AWS CLI and jq.](https://gist.github.com/weavenet/f40b09847ac17dd99d16)

