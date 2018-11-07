Toxiproxy.Net
=============

Toxiproxy.Net is a .Net client for Shopify's [Toxiproxy](https://github.com/shopify/toxiproxy) REST/JSON Api. Toxiproxy is a proxy to test your system by simulating network failure.


Project

[![Build status](https://ci.appveyor.com/api/projects/status/y6hj8h6x2po0o9rp?svg=true)](https://ci.appveyor.com/project/nenad-plutoflume/toxiproxy-net)

Master branch

[![Build status](https://ci.appveyor.com/api/projects/status/y6hj8h6x2po0o9rp/branch/master?svg=true)](https://ci.appveyor.com/project/nenad-plutoflume/toxiproxy-net/branch/master)

[//]: # (Test Coverage)

Getting started
---------------

To install via the nuget package console

```
Install-Package Toxiproxy.Net
```

A copy of toxiproxy compiled for windows is in the [compiled folder](https://github.com/mdevilliers/Toxiproxy.Net/tree/master/compiled/Win64). Linux, Darwin and Windows builds are available from the [official release site](https://github.com/Shopify/toxiproxy/releases)

Usage
-----

The unit tests give a good overview of how to fully use the api. 
Here are some examples to get started -

Start Toxiproxy

```
_proxyProcess = new Process()
    {
        StartInfo = new ProcessStartInfo(@".\packages\Toxiproxy.Net\compiled\Win64\toxiproxy-server-2.1.2-windows-amd64.exe")
    };
_proxyProcess.Start();

```

Set up a proxy

```
var connection = new Connection();
var client = connection.Client();

//proxy all traffic from 127.0.0.1:44399 to https://google.com
var localToGoogleProxy = new Proxy() { 
    Name = "localToGoogle", 
    Enabled = true, 
    Listen = "127.0.0.1:44399", 
    Upstream = "google.com:443" 
};

client.Add(localToGoogleProxy);

```

Disable a proxy

```
var connection = new Connection();
var client = connection.Client();
var proxy = client.FindProxy("localToGoogle");

proxy.Enabled = false;
proxy.Update();

```

Timeout a proxy

```
var connection = new Connection();
var client = connection.Client();
var proxy = client.FindProxy("localToGoogle");

var timeoutProxy = new TimeoutToxic();
timeoutProxy.Attributes.Timeout = 100;
timeoutProxy.Toxicity = 1.0;

proxy.Add(timeoutProxy);
proxy.Update();

```

Return a list of all proxies

```
var connection = new Connection();
var client = connection.Client();

client.All();

```

Reset all proxies to a good state automatically

```
// assuming the proxy is running with its default settings e.g. localhost:8474
// and there is a connection defined with the name "ms_sql"
// create a connection to toxiproxy that will call reset on disposal
// this resets the state of all the toxics and re-enables all of the proxies
    using (var connection = new Connection(true))
    {

        // find the upstream latency toxic
        var proxy = connection.Client().FindProxy("ms_sql");
        var latency = (LatencyToxic)proxy.GetToxicByName("Latency");


        // set a latency of 1 second 
        latency.Attributes.Latency = 1000;

        // save it
        proxy.Update();

        // do your test....

    }

```

Thanks
------

[soneyworld](https://github.com/soneyworld)
[Federico Orlandini](https://github.com/federicoorlandini)
[vromantp](https://github.com/vromantp)
[mgkeen](https://github.com/mgkeen)
