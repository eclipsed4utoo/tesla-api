![GitHub Workflow Status](https://img.shields.io/github/workflow/status/at0dd/tesla-api/.NET)
![Release Version](https://img.shields.io/github/v/release/at0dd/tesla-api)
![Nuget Version](https://img.shields.io/nuget/v/tesla-api)
![Nuget Downloads](https://img.shields.io/nuget/dt/tesla-api)

Buying a Tesla? [Get 1,000 miles of free supercharging](https://ts.la/alex19632) with my referral code.

# C# Tesla API Client

This is an unofficial .NET client implementation of the Tesla JSON API used by the Android and iOS apps. The API provides functionality to monitor and control the Tesla vehicles remotely.

[Tim Dorr API Documentation](https://tesla-api.timdorr.com/)

[API Documentation GitHub](https://github.com/timdorr/tesla-api)

# Installation

## .NET CLI
```ps1
dotnet add package 'Tesla-API'
```

# Setup

## Dependency Injection

When registering your services, call the method below to allow all required services to be dependency injected.
```c#
services.AddTeslaApi();
```

# Usage

To make a request with the Tesla API, you'll need to create a `HttpClient` and set the `User-Agent` header to an identifier for your application.

## Authenticating with Tesla

Follow the standard OAuth process [as documented by Tim Dorr](https://tesla-api.timdorr.com/api-basics/authentication) to get an access token. After getting an access token, add it to the `Authorization` header on the `HttpClient`, which is passed into data API calls.

You can use the [TeslaAuth](https://github.com/tomhollander/TeslaAuth/) package that provides a .NET implementation to obtain a refresh token.

# Example Code

```c#
public class TeslaService
{
    private readonly ITeslaAPI _teslaAPI;
    private readonly HttpClient _client = new HttpClient();
    
    /// <summary>
    /// Initializes a new instance of the <see cref="TeslaService"/> class.
    /// </summary>
    /// <param name="teslaClient">The TeslaAPI client.</param>
    public TeslaService(ITeslaAPI teslaAPI)
    {
        _teslaAPI = teslaAPI;
        _client.DefaultRequestHeaders.Add("User-Agent", "MyApplication");
    }
    
    /// <summary>
    /// Get all vehicles in the user's account.
    /// </summary>
    /// <returns>A list containing~~~~ all vehicles.</returns>
    public async Task<List<Vehicle>> GetVehiclesAsync(string clientId, string clientSecret, string bearerToken)
    {
        TeslaAccessToken accessToken = await _teslaAPI.GetAccesTokenAsync(_client, clientId, clientSecret, bearerToken);
        _client.DefaultRequestHeaders.Add("Authorization", $"Bearer {accessToken.AccessToken}");
        
        return await _teslaAPI.GetAllVehiclesAsync(_client);
    }
}
```
