# Bakcup and Uplaod MongoDB to AWS S3

### What can it do ?

A package to backup mongodb database to amazon s3 bucket

### How do I get set up?

- Install the package
  `npm install node-mongodump-s3`
- Import it into your file
- Setup a backup client with your aws credential
- Initiate the backup

### Notes about the generated dump

#### The generated mongo dump is an archive, so while restoring you should mark the --archive flag on

`mongorestore --archive=/path/to/dump`

### Working example:

```js
const mongoS3Backup = require("node-mongodump-s3");

const bucketName = process.env.AWS_S3_DUMP_BUCKET;
const accessKey = process.env.AWS_ACCESS_KEY_ID;
const accessSecret = process.env.AWS_SECRET_ACCESS_KEY;
const dbConnectionUri = process.env.MONGO_URI;

const backupClient = mongoS3Backup({ bucketName, accessKey, accessSecret });

backupClient
  .backupDatabase({
    uri: dbConnectionUri,
    backupName: "test_backup_" + Date.now(),
    prefix: "backups/",
  })
  .then((response) => {
    console.log("Success response ", response);
    /*
  Success response includes default aws response to uploading files
  example: 
    { 
      ETag: '"exampleEtag"',
      Location: 'https://examplebucket.s3.amazonaws.com/test_backup',
      key: 'test_backup',
      Key: 'test_backup',
      Bucket: 'mybucket' 
    }

*/
  })
  .catch((err) => {
    console.log("error is ", err);
  });
```

### Possible options passed to `backupDatabase` method

- `uri: string` `required` the connection string for the mongo database `
- `backupName: string` `required` the name of the backup to be dumped on s3
- `gzip: true|false` wheather to use gzip option for the dump command, defaults to false

#### The package supports both promises & callback patterns
