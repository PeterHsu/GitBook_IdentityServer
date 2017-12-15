# 建置Bamboo Console

## 建立專案

Bamboo/BambooConsole&gt;dotnet new console

&gt;dotnet add package IdentityServer4.AccessTokenValidation --version 2.1.0

## 程式

### 修改Program.cs

```
static void Main(string[] args)
{
    Call();
    Console.ReadLine();
}
static async void Call()
{
    var disco = await DiscoveryClient.GetAsync("http://localhost:5000");
    if (disco.IsError)
    {
        Console.WriteLine(disco.Error);
        return;
    }

    var tokenClient = new TokenClient(disco.TokenEndpoint, "client", "secret");
    var tokenResponse = await tokenClient.RequestClientCredentialsAsync("api1");

    if (tokenResponse.IsError)
    {
        Console.WriteLine(tokenResponse.Error);
        return;
    }

    Console.WriteLine(tokenResponse.Json);

    // call API
    var client = new HttpClient();
    client.SetBearerToken(tokenResponse.AccessToken);

    var response = await client.GetAsync("http://localhost:5001/identity");
    if (!response.IsSuccessStatusCode)
    {
        Console.WriteLine(response.StatusCode);
    }
    else
    {
        var content = await response.Content.ReadAsStringAsync();
        Console.WriteLine(JArray.Parse(content));
    }
}
```

## 測試

BambooServer&gt;dotnet run

BambooAPI&gt;dotnet run

BambooConsole&gt;dotnet run



