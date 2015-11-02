# Elastcoder
[![Build Status](https://travis-ci.org/dumpk/elastcoder.svg?branch=master)](https://travis-ci.org/dumpk/elastcoder)
AWS Elastic Transcoder integration for Laravel. This is a simple wrapper if you don't want to go too deep with the [AWS SDK for PHP 3.x](http://docs.aws.amazon.com/aws-sdk-php/v3/api/index.html)!.

## Installation

Require this package with composer:

```
composer require dumpk/elastcoder
```

Add this variables to your .env with your Amazon credentials

```
AWS_ACCESS_KEY_ID={AMAZONACCESSKEY}
AWS_SECRET_ACCESS_KEY={SECRETAMAZONSOMETHINGLONG}
AWS_REGION={YOURREGION}
```

## Usage

Add the ElastcoderAWS class to your class header
```
use Dumpk\Elastcoder\ElastcoderAWS;
```

Now you just need to instance your class

```
$elastcoder = new ElastcoderAWS();
```

You'll need to have your video uploaded to S3 before using this library, so that means that you already have the key for the file you want to encode.
The configuration has to look like:

```

$config = [
    'PresetId' => '',
    'width'  => 1920,
    'height' => 1080,
    'aspect' => '16:9',
	'ext'	 => 'mp4',
	'PipelineId' => '14340594502-n2fuqq4',
    'Watermarks' => [[
            'PresetWatermarkId' => 'BottomRight',
            'InputKey'          => '1080.png',
    ]],
]

```

There's an example file you can throw in on your config folder in dumpk/elastcoder/example/elastcoder.php remember to change the values for the pipeline and presetId you want and you have configured on your AWS account.

So now you can call the transcoding function:

```
$job = $elastcoder->transcodeVideo($asset->key, $destinationKey, $config, $thumbPattern);

```

This will return a job with an array, where you can find the "Id" of the Job, you'll need to check the job for completeness to know if your video is ready. Buy you already know that, so for your convienience you can use:

```
$job = $elastcoder->getJob($job["Id"]);

```
And check if $job['Status'] == 'complete'

Also you can change the ACL of your file, because you might want to give access to everyone to see your amazing video, so:
```
$elastcoder->setPublicObject($key, $bucket);

```

If you want to be more specific about that and have different types of permissions, you can use:
```
$elastcoder->setObjectACL($key, $bucket, $acl);

```
$acl can be a string with this values => private|public-read|public-read-write|authenticated-read|bucket-owner-read|bucket-owner-full-control
