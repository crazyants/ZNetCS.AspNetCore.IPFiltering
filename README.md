# ZNetCS.AspNetCore.IPFiltering

[![NuGet](https://img.shields.io/nuget/v/ZNetCS.AspNetCore.IPFiltering.svg)](https://www.nuget.org/packages/ZNetCS.AspNetCore.IPFiltering)

A middleware that allows whitelist or blacklist incomming requests based on IP address. It can be configured using single IP address or ranges.
It supports single IP, IP range IPv4 and IPv6. 

## Installing 

Install using the [ZNetCS.AspNetCore.IPFiltering NuGet package](https://www.nuget.org/packages/ZNetCS.AspNetCore.IPFiltering)

```
PM> Install-Package ZNetCS.AspNetCore.IPFiltering
```

##Usage 

When you install the package, it should be added to your `package.json`. Alternatively, you can add it directly by adding:


```json
{
  "dependencies" : {
    "ZNetCS.AspNetCore.IPFiltering": "1.0.1"
  }
}
```

In order to use the IP filtering middleware, you must configure the services in the `ConfigureServices` and `Configure` call of `Startup`. Make
sure middleware is added just after loging to prevent any other middleware to run, so block is most effective: 

```csharp
using ZNetCS.AspNetCore.IPFiltering.DependencyInjection;
```

```
...
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIPFiltering();
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{   
	app.UseIPFiltering();

	// other middleware e.g. MVC etc
}
```

##Configuration
This middleware can be configured using following configuration options:

 * `DefaultBlockLevel` defines default action when IP address is not listed. Can be configured to `None` or `All`. Default value is `All`.
 * `HttpStatusCode` defines status code that is returned to client when IP address is forbidden. Default value is `404` (`Not Found`).
 * `Whitelist` defines list of IP address ranges that are allowed for request.
 * `Blacklist` defines list of IP address ranges that are forbidden for request.

###IP Address Ranges
`Whitelist` and `Blacklist` can be defined as single IP address or IP address range. For parsing middleware is using extenal 
package: https://github.com/jsakamoto/ipaddressrange. Ranges can be defined in following formats:

 * `192.168.0.0/255.255.255.0`
 * `192.168.0.10-192.168.10.20`
 * `fe80::/10`

##File
Middleware can be configured in appsettings.json file. By adding following section and use following `ConfigureServices` method:

```json
{
    "IPFiltering": {
        "DefaultBlockLevel": "All",
        "HttpStatusCode": 404,
        "Whitelist": [ "192.168.0.10-192.168.10.20", "fe80::/10" ],
        "Blacklist": [ "192.168.0.100-192.168.1.200"]
    }
}
```
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIPFiltering(this.Configuration.GetSection("IPFiltering"));
}
```
