This document explains how to get display your steam profile, dashboard, game stats and achievementsin your websites.

**Example:** A live example is available in

> https://newtonjoshua.com

**Steam :**

Steam is a digital distribution platform developed by Valve Corporation offering digital rights management (DRM), multiplayer gaming and social networking services. Steam provides the user with installation and automatic updating of games on multiple computers, and community features such as friends lists and groups, cloud saving, and in-game voice and chat functionality.
It has over 3,500 games. Whether you’re on a PC, Mac, Linux box, mobile device, or even your television, you can enjoy the benefits of Steam.
Download and run the Steam Installer from Steampowered.com
You can start purchasing, downlaoding and ultimately playing games in it.

refer:

> http://store.steampowered.com
> 
>  https://steamcommunity.com

> https://www.facebook.com/Steam

**Steam Web API:**

The Steam Web API is a set of data services for obtaining information related to Valve Software's Steam platform.
Valve provides these APIs so website developers can use data from Steam in new and interesting ways. They allow developers to query Steam for information that they can present on their own sites.

refer:

> https://developer.valvesoftware.com/wiki/Steam_Web_API

**Steam Web API Key:**

All use of the Steam Web API requires the use of an API Key. You can acquire one by filling out this form. 
https://steamcommunity.com/dev/apikey

    String key = "{{apikey}}";

**Steam ID:**

Use your steam user name to get your steam id.

> http://steamid.co/player.php?input={{User_Name}}

The Steam 64 ID is the required steam_id

    String steamId = "{{steam_id}}";

**Interfaces and method:**

note: You cannot send API requests from the (client) browser. It'll throw 'No 'Access-Control-Allow-Origin' header is present on the requested resource.'
So access Steam Web API from your server.

A JAVA example is given in this answer.

***GetPlayerSummaries:*** Returns basic profile information for a list of 64-bit Steam IDs.

> http://api.steampowered.com/ISteamUser/GetPlayerSummaries/v0002/?key={{key}}&steamids={{steamId}}

     //      Get Player Details           
        JSONObject playerSummary = HttpGet.get("http://api.steampowered.com/ISteamUser/GetPlayerSummaries/v0002/?key=" + key + "&steamids=" + steamId);
        response = playerSummary.getJSONObject("response");
        JSONArray players = response.getJSONArray("players");
        player = players.getJSONObject(0);
        JSONObject result = new JSONObject();
        result.put("player", player);

***GetOwnedGames:*** GetOwnedGames returns a list of games a player owns along with some playtime information.

> http://api.steampowered.com/IPlayerService/GetOwnedGames/v0001/?key={{key}}&steamid={{steamId}}&include_appinfo=1&format=json

   

    //      Get all the Owned Games
        JSONObject ownedGames = HttpGet.get("http://api.steampowered.com/IPlayerService/GetOwnedGames/v0001/?key=" + key + "&steamid=" + steamId + "&include_appinfo=1&format=json&include_played_free_games=1");
        JSONObject response = ownedGames.getJSONObject("response");
        JSONArray games = response.getJSONArray("games");

   **Get details of all games:**

   

    //      Loop through response.games[]
        for (int i = 0; i < games.length(); i++) {
            JSONObject game = games.getJSONObject(i);
            JSONObject gameData = new JSONObject();
    
            //          Copy the data in 'game' to 'gameData' and use 'game' for iteration
            gameData = game;
    
            String appid = game.getString("appid");
    
            // userStats : get stats and achievements of the user for the app
    
            // gameSchema :  get schema of the app
    
            gameData = getGameData(userStats, gameSchema, gameData);
            gameFeeds.put(gameData);
        }

You will need the appid to get details of that App/Game

***GetUserStatsForGame:*** Returns a list of achievements for this user by app id

> http://api.steampowered.com/ISteamUserStats/GetUserStatsForGame/v0002/?key={{key}}&steamid={{steamId}}&appid={{appid}}

This will give the list of your achievements and statistics in the game

    // Get UserStats (Achievements and Statistics) For game
    JSONObject userStats =
        HttpGet.get("http://api.steampowered.com/ISteamUserStats/GetUserStatsForGame/v0002/?key=" + key + "&steamid=" + steamId + "&appid=" + appid);

***GetSchemaForGame:*** GetSchemaForGame returns gamename, gameversion and availablegamestats(achievements and stats).

> http://api.steampowered.com/ISteamUserStats/GetSchemaForGame/v0002/?key={{key}}&steamid={{steamId}}&appid={{appid}}

This will give the complete detail of all the achievements and Statistics. 

 

    //          Get Schema For game to get more details about the achievements and statistics of the user
    JSONObject gameSchema =
        HttpGet.get("http://api.steampowered.com/ISteamUserStats/GetSchemaForGame/v0002/?key=" + key + "&steamid=" + steamId + "&appid=" + appid);

return the result

   

    //          set gameFeeds[] to result.games
        result.put("games", gameFeeds);
    
        return result;

**Map gameSchema with userStats**

You can map your achievements and stats with the schema to get more details of ypur achievements.
This will give a more appealing data to display in your website.

An Example of mapping the userStats with gameSchema is given below.

 

   

     public static JSONObject getGameData(JSONObject userStats, JSONObject gameSchema, JSONObject gameData) throws JSONException {
    
        //          Set the playerStats and gameSchema
        if (userStats.has("playerstats")) {
            userStats = userStats.getJSONObject("playerstats");
    
            if (gameSchema.has("game")) {
                gameSchema = gameSchema.getJSONObject("game");
    
                if (gameSchema.has("availableGameStats")) {
                    JSONObject availableGameStats = gameSchema.getJSONObject("availableGameStats");
    
                    // Achievements
                    if (userStats.has("achievements") && availableGameStats.has("achievements")) {
                        JSONArray achievements = userStats.getJSONArray("achievements");
                        JSONArray resAchievements = new JSONArray();
                        JSONArray reference = availableGameStats.getJSONArray("achievements");
    
                        //                          Iterate though achievements from playerstats.achievements[] obtained from GetUserStatsForGame
                        for (int j = 0; j < achievements.length(); j++) {
                            JSONObject achievement = achievements.getJSONObject(j);
                            String name = achievement.getString("name");
    
                            //                              Iterate through reference game.availableGameStats.achievements[] obtained from GetSchemaForGame
                            for (int k = 0; k < reference.length(); k++) {
                                JSONObject ref = reference.getJSONObject(k);
                                String refName = ref.getString("name");
    
                                if (name.equals(refName)) {
    
                                    //                                      put all the data from Schema for all the Achievements of the user in resAchievements
                                    resAchievements.put(ref);
    
                                    break;
                                }
                            }
                        }
    
                        //                          put resAchievements in gameData.achievements
                        gameData.put("achievements", resAchievements);
                    }
    
                    // Stats
                    if (userStats.has("stats") && availableGameStats.has("stats")) {
                        JSONArray stats = userStats.getJSONArray("stats");
                        JSONArray resStats = new JSONArray();
                        JSONArray reference = availableGameStats.getJSONArray("stats");
    
                        //                          Iterate though stats from playerstats.stats[] obtained from GetUserStatsForGame
                        for (int j = 0; j < stats.length(); j++) {
                            JSONObject stat = stats.getJSONObject(j);
                            String name = stat.getString("name");
                            int value = stat.getInt("value");
    
                            //                              Iterate through reference game.availableGameStats.stats[] obtained from GetSchemaForGame
                            for (int k = 0; k < reference.length(); k++) {
                                JSONObject ref = reference.getJSONObject(k);
                                String refName = ref.getString("name");
    
                                if (name.equals(refName)) {
    
                                    //                                      put all the data from Schema for all the stats of the user in resStats
                                    ref.put("value", value);
                                    resStats.put(ref);
    
                                    break;
                                }
                            }
                        }
    
                        //                          put resStats in gameData.stats
                        gameData.put("stats", resStats);
                    }
                }
            }
        }
    
        return gameData;
    }

note: the above JAVA example is not tuned for better performance. Having a large number of games may delay the response as the Steam API requests are sent in a single thread. try a multi threaded approach t send the requests.


***Display data:***

get the results from the server API and display the data in your website.

    'use strict';
    
    function getSteamFeeds() {
        $.get('{{api URI}}',
            function (feeds) {
                var avatar = feeds.player.avatarfull;
                var profile = feeds.player.profileurl;
    		
    	// Iterate through each game.
                feeds.games.forEach(function (game) {
    		// Get game data
    		var gameName = game.name;
                    var gameIcon = 'http://media.steampowered.com/steamcommunity/public/images/apps/' + game.appid + '/' + game.img_icon_url + '.jpg';
                    var gameLogo = 'http://media.steampowered.com/steamcommunity/public/images/apps/' + game.appid + '/' + game.img_logo_url + '.jpg';
                    var gamePlayTime = Math.ceil(game.playtime_forever / 60);
                    var gameLink = 'http://store.steampowered.com/app/' + game.appid;
    
                    if (game.achievements) {
                        game.achievements.forEach(function (achievement) {
    			var achievementIcon = achievement.icon;
    			var achievementDisplayName = achievement.displayName;
    			var achievementLink = 'http://steamcommunity.com/id/NewtonJoshua/stats/' + game.appid;
    			var achievementDescription = achievement.description;
                            // display the achievements for thegame
                        });
                    }
    		if (game.stats) {
    			var statDisplayName = stat.displayName;
    			var statValue = stat.value;
    			// use google.visualization.Table to display the data
                    }
                });
            });
    }


