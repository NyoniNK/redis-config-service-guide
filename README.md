# Groupbox Redis Configuration Service

This project provides a simple Redis-based configuration service for managing server-specific configurations in JSON format, with the ability to add or remove properties dynamically.

## Features

- Store configuration details in JSON format.
- Add or remove individual properties.
- Efficient, fault-tolerant, and easy to use in Blazor applications.

## Requirements

- [.NET Core SDK](https://dotnet.microsoft.com/download)
- [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) package
- [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) package
- A Redis server connection

## Installation

1. Install the necessary NuGet packages:

    ```bash
    dotnet add package StackExchange.Redis
    dotnet add package Newtonsoft.Json
    ```

2. Add the `ConfigService` class to your Blazor Server project.

## Usage

### 1. Initializing the Configuration Service

In your Blazor project, initialize the `ConfigService` by passing the Redis connection string, database index, and a unique `serverId`:

```csharp
using Groupbox.RedisConfigService;

var configService = new ConfigService("your-redis-connection-string", dbIndex: 0, serverId: "your-server-id");
```

- `connectionString`: Your Redis server connection string.
- `dbIndex`: The Redis database index (0 is usually the default).
- `serverId`: A unique identifier for your server or connection.

### 2. Saving Configuration

To save configuration, pass an object that you want to store in Redis. The object will be serialized as a JSON string.

```csharp
var config = new {
    SiteName = "My Website",
    MaxUsers = 100
};

configService.SaveConfig(config);
```

### 3. Retrieving Configuration

To retrieve the configuration, use `GetConfig<T>()`, specifying the type of object you're retrieving:

```csharp
var config = configService.GetConfig<Dictionary<string, object>>();
if (config != null)
{
    Console.WriteLine($"Site Name: {config["SiteName"]}");
}
```

### 4. Adding a New Property

To add a new property to the existing configuration:

```csharp
configService.AddProperty("ThemeColor", "Blue");
```

### 5. Removing a Property

To remove a property from the configuration:

```csharp
configService.RemoveProperty("MaxUsers");
```

## Example: Integration with Blazor Server

You can inject the `ConfigService` into your Blazor components or services:

1. Register the `ConfigService` in your `Startup.cs` or `Program.cs`:

    ```csharp
    services.AddSingleton(new ConfigService("your-redis-connection-string", 0, "your-server-id"));
    ```

2. Use dependency injection to access it in your Blazor components:

    ```csharp
    @inject Groupbox.RedisConfigService.ConfigService ConfigService

    <h3>Site Configuration</h3>

    @code {
        protected override void OnInitialized()
        {
            var config = ConfigService.GetConfig<Dictionary<string, object>>();
            if (config != null)
            {
                Console.WriteLine($"Current theme: {config["ThemeColor"]}");
            }
        }
    }
    ``` 
