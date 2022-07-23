# How to fetch Spotify Thumbnails using JavaScript

### **`#1`** Installing NPM packages

In your terminal, type the following commands:

```bash
npm install fs
npm install spotify-web-api-node
```

### **`#2`** Getting Spotify Credentials

In order to access the **Spotify API**, you need credentials, a Client ID and a Client Secret.

In the this **[link](https://www.avermedia.com/us/creator_central_spotify)**, you can learn how to setup a **Spotify App**.

You can store your credentials in a **.env** file:

```bash
SPOTIFY_CLIENT_ID=<EXMPLE>
SPOTIFY_CLIENT_SECRET=<EXAMPLE>
```

### **`#3`** Generating an Access Token

Now that you have your credentials, you need to have an **Access Token**, to actually have access to the **Spotify API**.

You can generate it, using the credentials you just got.

***Note:** Access Tokens are only valid for 1 hour, so you need to keep updating your Access Token.*

*File: **`getToken.js`***

```javascript
const fs = require('fs')
const SpotifyAPI = require('spotify-web-api-node')

const spotifyAPI = new SpotifyAPI({
    clientId: process.env.SPOTIFY_CLIENT_ID,
    clientSecret: process.env.SPOTIFY_CLIENT_SECRET
})

async function generateToken() {

    // Generate the Access Token

    let { body } = await spotifyAPI.clientCredentialsGrant()

    // Store Access Token in a file

    fs.writeFile('src/config/token.json', JSON.stringify(body), function (error) {
        if (error) generateToken()
    })

    // Generate a new Access Token in 50 minutes

    setTimeout(generateToken, 60000*50)
}
generateToken()
```

### **`#4`** Logging in using the Access Token

Now that you have an Access Token, it's time to use it!

***Note #1**: Make sure everytime you want to fetch a thumbnail, you login again, in case the previous login is using an invalid Access Token.*

***Note #2:** Whenever you get the Access Token from the file, use **FS** or else you will not be getting the most updated Access Token, and might get an error.*

*File: **`getThumbnail.js`***

```javascript
const fs = require('fs')
const SpotifyAPI = require('spotify-web-api-node')

// Get Access Token

let current = fs.readFileSync('src/config/token.json')
current = JSON.parse(current)
current = current.access_token

// Login

let spotifyAPI = new SpotifyAPI()
spotifyAPI.setAccessToken(current)
```

### **`#5`** Fetching Songs' Thumbnails

Now that you logged in using your Access Token, you can finally fetch thumbnails.

*File: **`getThumbnail.js`***

```javascript
let track = (await spotifyAPI.getTracks(<SPOTIFY_TRACK_URL>.replace('https://open.spotify.com/track/',''))).body.tracks[0]
let thumbnail = track.album.images[0].url
```
