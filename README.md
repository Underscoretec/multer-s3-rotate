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

## Newly Added

To resize image, add resize and resizeOpts keys to the object

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
    resize: true, // default false
    resizeOpts: { // Required if resize is true
      width: 400,
      height:400,
      fit: 'inside'
    },
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
### Value for resize
- resize ( Boolean ) If true, resize action occurs else only images are rotated 

### Keys for resizeOpts

- width ( String || Number )
- height ( String || Number )
- fit ( String ) how the image should be resized to fit both provided dimensions, one of cover, contain, fill, inside or outside. (optional, default 'cover')
- position ( String ) position, gravity or strategy to use when fit is cover or contain. (optional, default 'centre')
- background ( String || Object ) background colour when using a fit of contain, parsed by the colormodule, defaults to black without transparency. (optional, default {r:0,g:0,b:0,alpha:1})
- kernel ( String ) the kernel to use for image reduction. (optional, default 'lanczos3')
- withoutEnlargement ( Boolean ) do not enlarge if the width or height are already less than the specified dimensions, equivalent to GraphicsMagick's > geometry option. (optional, default false)
- fastShrinkOnLoad ( Boolean ) take greater advantage of the JPEG and WebP shrink-on-load feature, which can lead to a slight moiré pattern on some images. (optional, default true)


## Testing

The tests mock all access to S3 and can be run completely offline.

```sh
npm test
```
