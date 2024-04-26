---
layout: page_v2
title: 51Degrees RTD Provider Module
display_name: 51Degrees RTD Module
description: 51Degrees Real Time Data Module
page_type: module
module_type: rtd
module_code : 51DegreesRtdProvider
enable_download : true
vendor_specific: true
sidebarType : 1
---

# 51Degrees RTD Submodule

{:.no_toc}

* TOC
{:toc}

## Description
51Degrees module enriches an OpenRTB request with the device data using [51Degrees Cloud API](https://51degrees.com/documentation/4.4/index.html) (can also be used with a self-hosted 51Degrees service). 51Degrees detects and sets the following fields of the device object: `make`, `model`, `os`, `osv`, `h`, `w`, `ppi`, `pixelratio` - interested bidder adapters may use these fields as needed.  In addition the module sets `device.ext.fiftyonedegrees_deviceId` to a permanent device ID which may be used with a 51Degrees data file to look up over 250 properties on the backend.

## Usage

### Integration

Compile the 51Degrees RTD Module with other modules and adapters into your Prebid.js build:

```
gulp build --modules="rtdModule,51DegreesRtdProvider,appnexusBidAdapter,..."  
```

> Note that the 51Degrees RTD module is dependent on the global real-time data module, `rtdModule`.

### Prerequisites

#### Resource Key
In order to use the module please first obtain a Resource Key using the [Configurator tool](https://configure.51degrees.com/AQRVdgJ-9x1dNvxk3Eg) - choose the following properties:
* DeviceId
* DeviceType
* HardwareVendor
* HardwareName
* HardwareModel
* PlatformName 
* PlatformVersion
* ScreenPixelsHeight
* ScreenPixelsWidth
* ScreenInchesHeight
* ScreenInchesWidth
* PixelRatio (optional)

PixelRatio is desirable, but it's a paid property so will work either in a free trial mode or with a license.  Also free API service is limited to 500,000 requests per month - consider picking a [51Degrees pricing plan](https://51degrees.com/pricing) that fits your needs. 

#### Client Hint Permissions

Some client-hint headers are not available to third parties. To allow 51Degrees cloud service to access these headers for more accurate detection and lower latency, it is highly recommended to set `Permissions-Policy` in one of two ways:

In the HTML of the publisher's web page where Prebid.js wrapper is integrated:

```html
<meta http-equiv="Delegate-CH" content="sec-ch-ua-arch https://cloud.51degrees.com; sec-ch-ua-full-version https://cloud.51degrees.com; sec-ch-ua-full-version-list https://cloud.51degrees.com; sec-ch-ua-model https://cloud.51degrees.com; sec-ch-ua-platform https://cloud.51degrees.com; sec-ch-ua-platform-version https://cloud.51degrees.com"/>
```

Or in the Response Headers of the publisher's web server:

```http
Permissions-Policy: ch-ua-arch=(self "https://cloud.51degrees.com"), ch-ua-full-version=(self "https://cloud.51degrees.com"), ch-ua-full-version-list=(self "https://cloud.51degrees.com"), ch-ua-model=(self "https://cloud.51degrees.com"), ch-ua-platform=(self "https://cloud.51degrees.com"), ch-ua-platform-version=(self "https://cloud.51degrees.com")

Accept-CH: sec-ch-ua-arch, sec-ch-ua-full-version, sec-ch-ua-full-version-list, sec-ch-ua-model, sec-ch-ua-platform, sec-ch-ua-platform-version
```

### Configuration

This module is configured as part of the `realTimeData.dataProviders`

```javascript
pbjs.setConfig({
    debug: true, // we recommend turning this on for testing as it adds more logging
    realTimeData: {
        auctionDelay: 1000, // should be set lower in production use
        dataProviders: [
            {
                name: '51Degrees',
                waitForIt: true, // should be true, otherwise the auctionDelay will be ignored
                params: {
                    // Get your resource key from https://configure.51degrees.com/AQRVdgJ-9x1dNvxk3Eg
                    resourceKey: '<YOUR_RESOURCE_KEY>',
                    // alternatively, you can use the on-premise version of the 51Degrees service
                    // onPremiseJSUrl: 'https://localhost/51Degrees.core.js'
                },
            },
        ],
    },
});
```

### Parameters 

> Note that `resourceKey` and `onPremiseJSUrl` are mutually exclusive parameters. Use strictly one of them: either a `resourceKey` for cloud integration and `onPremiseJSUrl` for the on-premise self-hosted integration. 

{: .table .table-bordered .table-striped }
| Name                  | Type    | Description                                                                                  | Default            |
|:----------------------|:--------|:---------------------------------------------------------------------------------------------|:-------------------|
| name                  | String  | Real time data module name                                                                   | Always '51Degrees' |
| waitForIt             | Boolean | Should be `true` if there's an `auctionDelay` defined (mandatory)                            | `false`            |
| params                | Object  |                                                                                              |                    |
| params.resourceKey    | String  | Your 51Degrees Cloud Resource Key                                                            |                    |
| params.onPremiseJSUrl | String  | Direct URL to your self-hosted on-premise JS file (e.g. https://localhost/51Degrees.core.js) |                    |

## Example 

> Note: you need to have a valid resource key to run the example.\
> It should be set in the configuration instead of `<YOUR_RESOURCE_KEY>`.\
> It is located in the `integrationExamples/gpt/51DegreesRtdProvider_example.html` file.

If you want to see an example of how the 51Degrees RTD module works,\
run the following command:

`gulp serve --modules=rtdModule,51DegreesRtdProvider,appnexusBidAdapter`

and then open the following URL in your browser:

`http://localhost:9999/integrationExamples/gpt/51DegreesRtdProvider_example.html`

Open the browser console to see the logs.