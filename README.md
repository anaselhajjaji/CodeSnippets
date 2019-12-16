# Dot Net
## Fetch random data from API + Deserialize JSON + Save to SQLServer using Entity Framework
```csharp

class Program
{
    static HttpClient client = new HttpClient();

    private async static Task<RootObject> FetchObject()
    {
        HttpResponseMessage response = await client.GetAsync("https://randomuser.me/api/");
        if (response.IsSuccessStatusCode)
        {
            var resultStr = await response.Content.ReadAsStringAsync();
            return JsonConvert.DeserializeObject<RootObject>(resultStr);
        }

        return null;
    }

    async static Task Main(string[] args)
    {
        while (true)
        {
            await Task.Delay(1000);
            var rootObject = await FetchObject();

            using (var ctx = new DbContext())
            {
                ctx.Database.Initialize(true);
                ctx.Database.CreateIfNotExists();

                ctx.Results.Add(rootObject.results[0]);
                ctx.SaveChanges();
            }
        }
    }
}

```