# Week 7 - Connecting Frontend with Backend

Now that we've discussed many aspects of .NET, it's time to bring everything together and explore how the frontend should interact with the backend. Please make sure that you complete all steps in order to successfully participate in the class.

## Meal sharing database

We covered database setup in the previous class. For this class please make sure that you have your meal-sharing database locally on your MySQL server.

## Prepare Frontend

Create a new branch `week7-meal-sharing` and then create a new folder `meal-sharing-final` and subfolder `frontend`. Inside the `frontend` folder copy content of the meal-sharing app. **Be careful not to copy .git folder from your original meal-sharing repository!** 

Make sure that you have a `.env` file in the `frontend` folder and that `API_PORT=5000` and `CLIENT_PORT=3000` are defined inside of it. Port 5000 will be the port number on which we will be running our backend later on.

From the `frontend` folder run `npm install` and then try running the client with `npm run client` command. You should be able to access the client through the browser using [http://localhost:3000](http://localhost:3000/). If you open developer tools you will probably see a failed request to the backend but don’t worry we will get there.

## Prepare Backend

Remove the `src/backend` folder as we will use the .NET server instead. From the `meal-sharing-final` run the dotnet command to create a new backend project `dotnet new web -o backend`

The structure of the `meal-sharing-final` folder should be close to this:

```bash
meal-sharing-final/
├─ frontend/
│  ├─ node_modules/
│  ├─ public/
│  ├─ src/
│  │  ├─ client/
│  │  ├─ backend/           ← remove this
│  ├─ package.json
│  ├─ …
├─ backend/                 ← new backend folder for the .NET project
│  ├─ obj/
│  ├─ Properties/
│  ├─ backend.csproj
│  ├─ Program.cs
│  ├─ …
```

In the backend find `launchSettings.json` and search for `applicationUrl`. Replace [http://localhost:XXXX](http://localhost:XXXX/) with [http://localhost:5000](http://localhost:5000/).

## ⚒️ **Task:** Connect Frontend with the Backend

Your frontend should be able to connect to the backend now but to actually make it somewhat workable let us introduce an `api/meals` endpoint in the backend that will return some fake data. Change the `Meal` class if it is needed.

```
app.MapGet("/api/meals", () => new[] { new Meal(){
    ...
}});

public class Meal
{
    public string Title { get; set; }
    [JsonPropertyName("max_reservations")]
    public int MaxReservations { get; set; }
    public decimal Price { get; set; }
}
```

Run the backend and the frontend. Try navigating to [http://localhost:3000](http://localhost:3000). Do you see that one meal showing up on the list?

In the classroom, we will push this further by connecting our backend with the database and return some real data.
