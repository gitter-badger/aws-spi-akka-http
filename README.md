# AWS Akka-Http SPI implementation

This library implements the provided [SPI](https://en.wikipedia.org/wiki/Service_provider_interface) for the asynchronous 
and non-blocking http calls in the new [AWS Java SDK](https://github.com/aws/aws-sdk-java-v2) with 
[Akka HTTP](https://github.com/akka/akka-http).

This is a prototypical implementation to explore an alternative to netty as the build-in http engine in the aws sdk.

This library is **not production ready** and early alpha. Use at your own risk. 
Also, the underlying SPI is subject to change.

## Usage

Currently, there is no binary release available (probably available soon). The current way to try it out, is to build and 
publish it locally, e.g. in `sbt` run:

    publishLocal
     
to install it in the local Ivy repository or

    publishM2
    
to install it in the local Maven repository.


Then its available as a dependency, e.g. in SBT

    "com.github.matsluni" %% "aws-spi-akka-http" % "0.0.1-SNAPSHOT"
    
or for Maven

```
<dependency>
    <groupId>com.github.matsluni</groupId>
    <artifactId>aws-spi-akka-http_2.12</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

An example (in scala) from the test shows how to use akka-http as the underlying http provider instead of netty.

```scala
val akkaClient = new AkkaHttpAsyncHttpService().createAsyncHttpClientFactory().createHttpClientWithDefaults(AttributeMap.empty())

val client = S3AsyncClient
              .builder()
              .credentialsProvider(ProfileCredentialsProvider.builder().build())
              .region(Region.EU_CENTRAL_1)
              .asyncHttpConfiguration(ClientAsyncHttpConfiguration.builder().httpClient(akkaClient).build())
              .build()
              
val eventualResponse = client.listBuckets()
```

There also exists an [mini example project](https://github.com/matsluni/aws-spi-akka-http-example) in gradle which shows the usage.

## Running the tests in this repo

In this repository there are unit tests and integration tests. The unit tests run against some local started aws 
services, which test some basic functionality and do not use real services from aws and cost no money. 

But, there are also integration tests which require **valid aws credentials** to run and running these tests is **not for free**. 
If you are not careful it can cost you money on aws. Be warned.

The integration tests look for aws credentials as either an environment variable, a system property or a credential file.
See the [here](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) and 
[here](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html) in the aws documentation for details.

The tests can be run in `sbt` with:

    test
    
To run the integration tests

    it:test



# License
This library is Open Source and available under the Apache 2 License.