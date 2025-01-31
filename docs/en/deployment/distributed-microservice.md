# Deploying Distributed / Microservice Solutions

ABP is designed for distributed and microservice systems, where multiple applications and/or services communicate internally. All of its features are compatible with distributed scenarios. This document highlights some points you should consider when deploying your distributed or microservice solution.

## Application Name & Instance Id

ABP provides the `IApplicationInfoAccessor` service that provides the following properties:

* `ApplicationName`: A human-readable name for an application. It is a unique value for an application.
* `InstanceId`: A random (GUID) value generated by the ABP each time you start the application.

ABP uses these values in several places to distinguish the application and the application instance (process) in the system. For example, the [audit logging](../framework/infrastructure/audit-logging.md) system saves the `ApplicationName` in each audit log record written by the related application so you can understand which application has created the audit log entry. So, if your system consists of multiple applications saving audit logs to a single point, you should be sure that each application has a different `ApplicationName`.

The `ApplicationName` property's value is set automatically from the **entry assembly's name** (generally, the project name in a .NET solution) by default, which is proper for most cases, since each application typically has a unique entry assembly name.

There are two ways to set the application name to a different value. In this first approach, you can set the `ApplicationName` property in your application's [configuration](../framework/fundamentals/configuration.md). The easiest way is to add an `ApplicationName` field to your `appsettings.json` file:

````json
{
    "ApplicationName": "Services.Ordering"
}
````

Alternatively, you can set `AbpApplicationCreationOptions.ApplicationName` while creating the ABP application. You can find the `AddApplication` or `AddApplicationAsync` call in your solution (typically in the `Program.cs` file), and set the `ApplicationName` option as shown below:

````csharp
await builder.AddApplicationAsync<OrderingServiceHttpApiHostModule>(options =>
{
    options.ApplicationName = "Services.Ordering";
});
````

## Using a Distributed Event Bus

ABP's [Distributed Event Bus](../framework/infrastructure/event-bus/distributed) system provides a standard interface for communicating with other applications and services. While the name is "distributed," the default implementation is in process. That means your applications/services can not communicate with each other unless you explicitly configure a distributed event bus provider.

The applications should communicate through an external distributed messaging server if you are building a distributed system. Please follow the [Distributed Event Bus](../framework/infrastructure/event-bus/distributed) document to learn how to install and configure your distributed event bus provider.

> **Warning**: Even if you do not use the distributed event bus directly in your application code, ABP and some of the modules you are using may use it. So, if you are building a distributed system, always configure a distributed event bus provider.

> **Info**: [Clustered deployment](./clustered-environment.md) of a single application is not considered as distributed system. So, a real distributed messaging server may not be needed if you only have a single application with multiple instances serving behind a load balancer.

## See Also

* [Deploying to a Clustered Environment](./clustered-environment.md)
