# Manage Spotify using Terraform

# Prerequisites

To complete this tutorial, you will need:

Terraform version 1.0+
Docker Desktop
Spotify account with developer access
Create Spotify developer app
Before you can use Terraform with Spotify, you need to create a Spotify developer app and run Spotify's authorization proxy server.

Login to the Spotify developer dashboard.

![Screenshot 2024-09-19 at 18 25 42](https://github.com/user-attachments/assets/e96022a1-11fb-44ad-ae0f-8b1e111805e4)

Click the green Create an app button.

Fill out the name and description according to the table below, check the box to agree to the terms of services, then click Create.

![Screenshot 2024-09-19 at 18 26 08](https://github.com/user-attachments/assets/127d5e93-9c17-4ad0-8063-01ac5a421b59)

Once Spotify creates the application, find and click the green Edit Settings button on the top right side.

Copy the URI below into the Redirect URI field and click Add so that Spotify can find its authorization application locally on port 27228 at the correct path. Scroll to the bottom of the form and click Save.

```
http://localhost:27228/spotify_callback
```

![Screenshot 2024-09-19 at 18 27 17](https://github.com/user-attachments/assets/2be0dc10-29d8-4716-a7d6-bf70696a6fe1)


# Run authorization server

Now that you created the Spotify app, you are ready to configure and start the authorization proxy server, which allows Terraform to interact with Spotify.

![Screenshot 2024-09-19 at 18 27 57](https://github.com/user-attachments/assets/0cab2c34-2fc8-4e85-bf86-f61fc8f55df2)

Return to your terminal and set the redirect URI as an environment variable, instructing the authorization proxy server to serve your Spotify access tokens on port 27228.

```
export SPOTIFY_CLIENT_REDIRECT_URI=http://localhost:27228/spotify_callback
```

Next, create a file called .env with the following contents to store your Spotify application's client ID and secret.

```
SPOTIFY_CLIENT_ID=
SPOTIFY_CLIENT_SECRET=
```

Copy the Client ID from the Spotify app page underneath your app's title and description, and paste it into .env as your SPOTIFY_CLIENT_ID.

![Screenshot 2024-09-19 at 18 29 15](https://github.com/user-attachments/assets/db70d65c-bf45-4ea2-b7c2-13531ac73a7a)

Click Show client secret and copy the value displayed into .env as your SPOTIFY_CLIENT_SECRET.

Make sure Docker Desktop is running, and start the server. It will run in your terminal's foreground.

```
docker run --rm -it -p 27228:27228 --env-file ./.env ghcr.io/conradludgate/spotify-auth-proxy
```

Visit the authorization server's URL by visiting the link that your terminal output lists after Auth:.

The server will redirect you to Spotify to authenticate. After authenticating, the server will display Authorization successful, indicating that the Terraform provider can use the server to retrieve access tokens.

Leave the server running.

# Explore the configuration

Open main.tf. This file contains the Terraform configuration that searches Spotify and creates the playlist. The first two configuration blocks in the file:

configure Terraform itself and specify the community provider that Terraform uses to communicate with Spotify.
configure the Spotify provider with the key you set as a variable.

![Screenshot 2024-09-19 at 18 31 48](https://github.com/user-attachments/assets/fa5a7e53-c512-4c28-b6b5-ecbd0ae3377f)

The next block defines a Terraform data source to search the Spotify provider for Dolly Parton songs.

![Screenshot 2024-09-19 at 18 32 09](https://github.com/user-attachments/assets/b9da12c5-7f60-456c-8de2-7ed88b029250)

The next block uses a Terraform resource to create a playlist from the first three songs that match the search in the data source block.

![Screenshot 2024-09-19 at 18 32 26](https://github.com/user-attachments/assets/6abc4ea4-c0c4-414a-8268-1179d0ae0db7)

Open outputs.tf, which defines an output value for the URL of the playlist.

![Screenshot 2024-09-19 at 18 32 45](https://github.com/user-attachments/assets/da94f45c-c76b-4abd-8559-05c78650c1fb)

# Set the API key

Rename the terraform.tfvars.example file terraform.tfvars so that Terraform can detect the file.

```
mv terraform.tfvars.example terraform.tfvars
```

The .gitignore file in this repository excludes files with the .tfvars extension from version control to prevent you from accidentally committing your credentials.


Find the terminal window where the Spotify authorization proxy server is running and copy the APIKey from its output.

Open terraform.tfvars, and replace ... with the key from the proxy, so that Terraform can authenticate with Spotify. Save the file.

![Screenshot 2024-09-19 at 18 33 53](https://github.com/user-attachments/assets/7ee7d313-d9c7-44bf-aaa1-c6b4da3cf7f2)


# Install the Spotify provider

```
terraform init
```

# Create the playlist

```
terraform apply
```

Confirm the apply with a yes, and Terraform will create your playlist.

![Screenshot 2024-09-19 at 18 35 14](https://github.com/user-attachments/assets/07e2588e-2de4-462e-9ff6-13abb7bd29b1)

# Listen to your playlist

Open the playlist URL returned in the Terraform output and enjoy your playlist!

![Screenshot 2024-09-19 at 18 35 40](https://github.com/user-attachments/assets/af8b2302-218b-43d5-a352-e131d7736cd4)
