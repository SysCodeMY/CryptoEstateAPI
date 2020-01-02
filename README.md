# What Is An API
An application program interface (API) is a set of routines, protocols, and tools for building software applications. Basically, an API specifies how software components should interact. Additionally, APIs are used when programming graphical user interface (GUI) components. A good API makes it easier to develop a program by providing all the building blocks. A programmer then puts the blocks together.


# API Guideline (For C# Language)
This guideline uses C# programming language and you would need to use Microsoft Visual Studio. Using API to interact with CryptoEstate is really simple.

<b>Step 1 : Install Refit on Microsoft Visual Studio</b><br/>
Install refit to your solution, your required refit version varies with your .NET Framework,version compatibility. Refit currently supports any .NET Standard 1.4 such as UWP, Xamarin.Android, Xamarin.Mac, Xamarin.iOS, Desktop .NET 4.6.1, .NET Core and Uno Platform. For more info please visit: https://github.com/reactiveui/refit<br/>
<br/>
<b>Step 2 : Interface</b><br/>
Create an interface for the API. For this example, we will be using 2 API request; list and verify token.<br/>
```
using Refit;
using System.Collections.Generic;
using System.Threading.Tasks;
using UniDemo.Services.ResponseModel;

namespace UniDemo.Services.ApiGate
{
    public interface IEstateService
    {
        [Get("/api/estate/token/list/{code}")]
        Task<List<TokenResponseModel>> GetTokenList(string code,[AliasAs("userId")] string userId);

        [Get("/api/estate/token/verify/{code}")]
        Task<VerifyTokenResponse> GetVerifyToken(string code, [AliasAs("userId")] string userId,[AliasAs("tokenId")] string tokenId);
    }
}
```
<br/>A request URL can be updated dynamically using replacement blocks and parameters on the method. A replacement block is an alphanumeric string surrounded by { and }. If the name of your parameter doesn't match the name in the URL path, use the AliasAs attribute.<br/>
<br/><b>Step 3 : Prepare Response Model</b><br/>
Create a response model for each API functions. Make sure your response model property name is the same as in the JSON format. Here is the sample of JSON format for the Token Verify function<br/>
```
{
    "ipfsImageHash": "QmPaxdPdKhQKomMDqL5Bn88aEeSjVZQuGbKDGFD4Hu6jwF",
    "name": "Lot 1876547 Mukim Kajang",
    "description": "Pajakan PTG/BPT/SEL 2/58/087(KL)",
    "externalURL": null,
    "category": "PTG/BPT/SEL 2/58/087(KL),Lot 1876547.,Lot 1876547",
    "ipfsDocumentHash": "QmZPShrf945DBuRwG9CF3d3bs81YaMrJkXF4H8ZzFSoriF",
    "recipientWallet": "0xe237f48cc65cf33c9f2b3c038d6eccab58e49674",
    "tokenName": "Syscode",
    "imageUrl": "https://beta.cryptoestate.net/public/getImage/fb7851ae-7c31-4801-9239-	9575013aa38e?hash=QmPaxdPdKhQKomMDqL5Bn88aEeSjVZQuGbKDGFD4Hu6jwF",
    "documentUrl": "https://beta.cryptoestate.net/public/getDocument/fb7851ae-7c31-4801-9239-	9575013aa38e?hash=QmZPShrf945DBuRwG9CF3d3bs81YaMrJkXF4H8ZzFSoriF"
}
```
<br/>Here is the sample of JSON format for the Token Verify function: <br/>
```
{
        "description": "Pajakan PTG/BPT/SEL 2/58/087(KL)",
        "name": "Lot 1876547 Mukim Kajang",
        "category": "PTG/BPT/SEL 2/58/087(KL),Lot 1876547.,Lot 1876547",
        "image": "QmPaxdPdKhQKomMDqL5Bn88aEeSjVZQuGbKDGFD4Hu6jwF",
        "external_url": null,
        "document": "QmZPShrf945DBuRwG9CF3d3bs81YaMrJkXF4H8ZzFSoriF",
        "tokenId": 197,
        "imageUrl": "https://beta.cryptoestate.net/public/getImage/fb7851ae-7c31-4801-9239-9575013aa38e?hash=QmPaxdPdKhQKomMDqL5Bn88aEeSjVZQuGbKDGFD4Hu6jwF",
        "type": null
    }
```

<br/> We moved to the example model which is to let the code to call each data available in the blockchain. For verify token : <br/>

```
using Newtonsoft.Json;

namespace UniDemo.Services.ResponseModel
{
    public class VerifyTokenResponseModel
    {

        [JsonProperty(PropertyName = "ipfsImageHash")]
        public string ipfsImageHash { get; set; }

        [JsonProperty(PropertyName = "name")]
        public string name { get; set; }

        [JsonProperty(PropertyName = "description")]
        public string description { get; set; }

        [JsonProperty(PropertyName = "externalURL")]
        public string externalURL { get; set; }

        [JsonProperty(PropertyName = "category")]
        public string category { get; set; }

        [JsonProperty(PropertyName = "ipfsDocumentHash")]
        public string ipfsDocumentHash { get; set; }

        [JsonProperty(PropertyName = "recipientWallet")]
        public string recipientWallet { get; set; }

        [JsonProperty(PropertyName = "tokenName")]
        public string tokenName { get; set; }

        [JsonProperty(PropertyName = "imageUrl")]
        public string imageUrl { get; set; }

        [JsonProperty(PropertyName = "documentUrl")]
        public string documentUrl { get; set; }
    }
}
```
<br/> Here is the example model for Token List <br/>
```
namespace UniDemo.Services.ResponseModel
{
    public class TokenListResponseModel
    {
        [JsonProperty(PropertyName = "description")]
        public string Description { get; set; }

        [JsonProperty(PropertyName = "name")]
        public string Name { get; set; }

        [JsonProperty(PropertyName = "category")]
        public string Category { get; set; }

        [JsonProperty(PropertyName = "image")]
        public string Image { get; set; }

        [JsonProperty(PropertyName = "externalURL")]
        public string External_Url { get; set; }

        [JsonProperty(PropertyName = "document")]
        public string Document { get; set; }

        [JsonProperty(PropertyName = "tokenId")]
        public int TokenId { get; set; }

        [JsonProperty(PropertyName = "imageUrl")]
        public string ImageUrl { get; set; }

        [JsonProperty(PropertyName = "type")]
        public string Type { get; set; }

    }
}
```

<br/> <b>Step 4 : Service </b> <br/>
RestService class generates an implementation of IGitHubApi that uses HttpClient to make its calls: <br/>
```
api = RestService.For<IEstateService>("https://beta.cryptoestate.net");
```

<br/>That’s it! now you can call the API from your controller. <br/>
```
using Refit;
using System.Collections.Generic;
using System.Threading.Tasks;
using UniDemo.Services.ResponseModel;

namespace UniDemo.Services.ApiGate
{
    public class ApiService
    {
        private IEstateService api;

        public ApiService()
        {
            api = RestService.For<IEstateService>("https://beta.cryptoestate.net");
        }

        public async Task<List<TokenResponseModel>> GetTokenList(string code, string userId)
        {
            var data = await api.GetTokenList(code,userId);
            return data;
        }

        public async Task<VerifyTokenResponse> GetVerifyToken(string code, string userId, string 	tokenId)
        {
            var data = await api.GetVerifyToken(code, userId, tokenId);
            return data;
        }
    }
}
```

