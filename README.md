# data-uploader

This is a simple API to upload assets into AWS s3. Notes about divergence from requirements will be covered verbally in demo. 

## Setup
Pull down the repo and run: 

Put your AWS credentials in with the following: 

```
module.exports = {
    "aws_access_key_id": "YourAccessKeyId",
    "aws_secret_access_key": "YourSecretAccessKey",
    "region": "YourRegion", 
    "bucket": "YourBucket"
}
```

Install and run with yarn - will run on localhost:8080 but this can be changed in server js
```
yarn
yarn run dev
```
Run tests - while running go to new terminal and: 
```
yarn run test
```

## API Reference
#### POST: /asset
/asset generates an S3 presigned url that can later upload a file. Expiration time is not set and is set to AWS default value. 

Requires Header 
```
Content-Type: <image/jpeg|text/html|text/plain|...|etc>
```

Returns a presigned url and id: 
```
{
    "upload_url": "https://asset-uploader-test.s3.amazonaws.com/...",
    "id": "0b8c2ea6-2d90-4bad-bc6e-ede2d8fd702f"
}
```

#### PUT Upload Asset: 
This part is doing a *PUT* to the presigned url generated by /asset

It does not hit this api directly.

I used Postman, you hit it as you see fit. 
For postman I generated a *PUT* with the url from /asset
Use Header ```Content-Type:image/jpeg```
Go to body, select 'binary' and choose a file of the type you put as the Content-Type.
Hit Send. A 200 response indicates the asset uploaded successfully. 

#### PUT /asset/{assetId}
/asset/{assetId} updates the metadata status of the asset to uploaded once it is successfully uploaded and will return a 403 if the asset is not present. On success will return a 200. 

Body: 
```
{
    "Status": "uploaded"
}
```

Headers
```
Content-Type:application/json
aws-content-type:<image/jpeg|text/html|text/plain|...|etc>
```

#### GET /asset/{assetId}?timeout=60
GET /asset/assetId retrieves a signedURL for a given asset ID to be retrieved. The timeout is optional and if it is not specified it defaults to 60. 

If the status has not been set to 'uploaded' as in the PUT call above, the get will fail. 

Headers: None additional

Response: 
```
{
    "Download_url": "https://asset-uploader-test.s3.amazonaws.com/..."
}
```

If no asset has been loaded, a 403 will be thrown. 

#### GET PresignedURL
After the previous calls have been made, the "Download_url" provided in GET /asset/{assetId} should return the original asset uploaded.
