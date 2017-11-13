# bsumm.net
bsumm.net uses Jekyll to build, and CloudFront backed by S3 for deployment.

## Installation
Install dependencies using `gem install jekyll s3_website`. Note that as of writing (Nov 2017) you must install the Java 8 JDK for `s3_website` as Java 9 is not supported.

Add a `.env` file to the root directory with the following format:

    S3_ID=...
    S3_SECRET=...

## Usage
When writing, run:

    jekyll serve

Deploy using:

    _scripts/deploy

