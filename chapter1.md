# 建置

## 軟體

dotnet --version

2.1.2

## 建立專案

Bamboo/BambooServer&gt;dotnet new web

&gt;dotnet add package IdentityServer4 --version 2.0.5

## 程式

### 新增Config.cs

```cs
using System.Collections.Generic;
using IdentityServer4.Models;

namespace BambooServer
{
    static class Config
    {
        public static IEnumerable<ApiResource> GetApiResources()
        {
            return new List<ApiResource>
            {
                new ApiResource("api1", "My API")
            };
        }
        public static IEnumerable<Client> GetClients()
        {
            return new List<Client>
            {
                new Client
                {
                    ClientId = "client",
                    AllowedGrantTypes = GrantTypes.ClientCredentials,
                    ClientSecrets =
                    {
                        new Secret("secret".Sha256())
                    },
                    AllowedScopes = { "api1" }
                }
            };
        }
    }
}
```

### 修改Startup.cs

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentityServer()
        .AddDeveloperSigningCredential()
        .AddInMemoryApiResources(Config.GetApiResources())
        .AddInMemoryClients(Config.GetClients());
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    app.UseIdentityServer();
}
```





