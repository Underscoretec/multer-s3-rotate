# Multer S3

This project is mostly an extension of [multer-s3](https://github.com/badunk/multer-s3). As many images from an iPhone were rotating while uploading, we extended the code to solve this issue using sharp.

## Installation

```sh
npm install --save multer-s3-rotate
```

## Usage

```javascript
var aws = require('aws-sdk')
var express = require('express')
var multer = require('multer')
var multerS3 = require('multer-s3-rotate')

var app = express()
var s3 = new aws.S3({ /* ... */ })

var upload = multer({
  storage: multerS3({
    s3: s3,
    bucket: 'some-bucket',
    metadata: function (req, file, cb) {
      cb(null, {fieldName: file.fieldname});
    },
    key: function (req, file, cb) {
      cb(null, Date.now().toString())
    }
  })
})

app.post('/upload', upload.array('photos', 3), function(req, res, next) {
  res.send('Successfully uploaded ' + req.files.length + ' files!')
})
```

## Testing

The tests mock all access to S3 and can be run completely offline.

```sh
npm test
```
