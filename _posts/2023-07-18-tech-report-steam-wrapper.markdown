---
layout: post
title:  "Tech Report: Steam Api Wrapper"
date:   2023-07-18 8:00:00
categories: report
---

## The Situation

Recently I was working on a post about one of my favorite games, Slay the Spire, and I came upon a fun challenge - how do I show off my credentials? I could include a screenshot of my Steam profile - with hours played, achievements unlocked, trading cards (?) collected. The only issue with this approach is that it is static, meaning whenever someone reads my post, they will not be getting realtime data about my time invested. Notably, the achievements unlocked would not change, because I've already unlocked them all (flex intended).

After a quick Google I found that Steam has an API, and I discovered that there would be a way - with a simple API call - to fetch the number of hours I've played of a given Steam game. There were a few wrinkles, however. I run this site on Github Pages, using Jekyll; I had no idea how to hide the sensitive information that would need to be included as part of the Request-URI. Most importantly, the Steam API key, but also importantly my own Steam account number. I don't need anyone knowing how many hours I've spent becoming a [map staring expert](https://www.reddit.com/r/eu4/comments/3upj3e/map_staring_expert/).

## The Solution

In any case, I'm sure there's some fancy way to make sure that a site like mine can somehow hide secrets in a Github vault and somehow mask the API key when making requests, but I don't know it. Instead, inspired by my recent AZ-104 preparation, I decided to build out a simple server. So simple, I can show you all the code I wrote for it right here:

```
from flask import Flask
import yaml
import requests
import json

app = Flask(__name__)

@app.route("/")
def base():
    return "{minutes: " + str(_read_from_steam()) + "}"

def _read_from_steam():
    with open('config.yml', 'r') as file:
        config = yaml.safe_load(file)

    url = "http://api.steampowered.com/IPlayerService/GetOwnedGames/v0001/"
    url += "?key=" + str(config['ApiKey'])
    url += "&steamid=" + str(config['SteamId'])

    x = requests.get(url)

    data = json.loads(x.text)

    for game in data['response']['games']:
        if(game['appid'] == config['AppId']):
            return game['playtime_forever']
```

The config.yml file looks like this:
```
ApiKey: *
SteamId: *
AppId: *
```

The more difficult part was deploying and setting up the server. I'm not going to go through great pains to explain it, but I will say that I requisitioned one of the cheapest VMs from Azure I could wrangle (ensuring that during setup I kept port 80 open for HTTP). Then, I followed these two tutorials to set up [NGINX](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-22-04) and [Gunicorn](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-22-04). There were a few modifications to the steps in those articles (like including the pip dependencies requests and pyyaml) but they were small and very context dependent. Finally, I had to add a CORS * directive to the nginx server.

After about three hours of work (30 minutes if I needed to repeat it), I can now display how many minutes I've played Slay the Spire like so:

```
I've played for <span id="stsMin"></span> minutes; ~<span id="stsHours"></span> hours.
<script type="text/javascript">
async function readTime() {
  var response = await fetch('http://74.249.156.182/');
  var jsonVal = await response.json();
  document.getElementById("stsMin").innerHTML = jsonVal["minutes"];
  document.getElementById("stsHours").innerHTML = Math.round(jsonVal["minutes"] / 60);
}
readTime();
</script>
```

I've played for <span id="stsMin"></span> minutes; ~<span id="stsHours"></span> hours.
<script type="text/javascript">
async function readTime() {
  var response = await fetch('http://74.249.156.182/');
  var jsonVal = await response.json();
  document.getElementById("stsMin").innerHTML = jsonVal["minutes"];
  document.getElementById("stsHours").innerHTML = Math.round(jsonVal["minutes"] / 60);
}
readTime();
</script>

Is this an enterprize quality solution to this problem? Hell no. Look at the link I'm fetching from! There are surely many improvements that can be made to bring this in line with commercial quality. However, this is not a commercial application. The situation is strictly personal, so it's acceptable for the solution to be as well.