#okhttp-stats ![alt text](https://travis-ci.org/flipkart-incubator/okhttp-stats.svg?branch=master) [![](https://jitpack.io/v/flipkart-incubator/okhttp-stats.svg)](https://jitpack.io/#flipkart-incubator/okhttp-stats)

OkHttp-Stats is an android library built on top of OkHttp3, which is responsible for intercepting all the network calls and for calculating network stats such as the average network speed of the user. This is more of an analytical tool which can be used to track the success and error response logs.

Can be plugged in to any app which uses okhttp in their networking stack.

## Gradle Dependency

Add it in your root build.gradle at the end of repositories:

````java
	allprojects {
		repositories {
			...
			maven { url "https://jitpack.io" }
		}
	}
````

Add the dependency:

````java
	dependencies {
		compile 'com.github.flipkart-incubator:okhttp-stats:1.1.1'
	}
````

## How to works

Create a class that implements the ````OnResponseListener````. This is where you will get all the callbacks in case of success or error responses.

````java
private class OnResponseReceived implements OnResponseListener {

        @Override
        public void onResponseSuccess(NetworkInfo info, RequestStats requestStats) {
            Log.d(MainActivity.class.getName(), "onResponseSuccessReceived : "
                    + "\nId : " + requestStats.id
                    + "\nUrl : " + requestStats.url
                    + "\nMethod : " + requestStats.methodType
                    + "\nHost : " + requestStats.hostName
                    + "\nRequest Size : " + requestStats.requestSize
                    + "\nResponse Size : " + requestStats.responseSize
                    + "\nTime Taken: " + (requestStats.endTime - requestStats.startTime)
                    + "\nStatus Code : " + requestStats.statusCode);
        }

        @Override
        public void onResponseError(NetworkInfo info, RequestStats requestStats, Exception e) {
            Log.d(MainActivity.class.getName(), "onResponseErrorReceived : "
                    + "\nId : " + requestStats.id
                    + "\nUrl : " + requestStats.url
                    + "\nMethod : " + requestStats.methodType
                    + "\nHost : " + requestStats.hostName
                    + "\nRequest Size : " + requestStats.requestSize
                    + "\nResponse Size : " + requestStats.responseSize
                    + "\nTime Taken: " + (requestStats.endTime - requestStats.startTime)
                    + "\nStatus Code : " + requestStats.statusCode
                    + "\nException : " + e.getMessage());
        }
    }
````

Initialize the NetworkInterceptor, and register your listener with the NetworkInterceptor.

````java
        
        //listener
        OnResponseReceived onResponseReceived = new OnResponseReceived();
        
        PersistentStatsHandler networkRequestStatsHandler = new PersistentStatsHandler(this);
        //register your listener with the PersistentStatsHandler
        networkRequestStatsHandler.addListener(onResponseReceived);
        
        NetworkInterpreter networkInterpreter = new DefaultInterpreter(new NetworkEventReporterImpl(networkRequestStatsHandler));

        NetworkInterceptor networkInterceptor = new NetworkInterceptor.Builder()
              .setNetworkInterpreter(networkInterpreter)
              .setEnabled(true)
              .build();
        
        //add the networkinterceptor to the okhttpclient
        OkHttpClient okHttpClient = new OkHttpClient().newBuilder()
              .addInterceptor(networkInterceptor)
              .build();

````
Now, pass the okHttpClient (which has an interceptor added) to the okhttpstack, and you are done.


## Getting Started Guide

Head over to the [Wiki](https://github.com/Flipkart/okhttp-stats/wiki) page for the detailed documentation


## Library Dependencies

* [OkHttp](https://github.com/square/okhttp)
* [JUnit](http://junit.org/), [Roboelectric](http://robolectric.org/), [Mockito](http://mockito.org/)


## Contributing

1. Fork the repo
2. Apply the changes
3. Submit your pull request


## Releases

Checkout the [Releases](https://github.com/flipkart-incubator/okhttp-stats/releases) tab for all release info.


## Licence

```
The MIT License (MIT)

Copyright (c) 2016 Flipkart Internet Pvt. Ltd.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
