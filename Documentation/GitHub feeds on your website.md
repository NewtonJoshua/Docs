This document explains how to display your GitHub feeds/timeline on your website.

Note: [it is not merely OK to ask and answer your own question, it is explicitly encouraged][1]

**Example:** A live example is available at:

> [https://newtonjoshua.com](\"https://newtonjoshua.com\")

**GitHub timeline:**

GitHub provides the public timeline for any user in Atom format.

You can view your timeline at:

> [https://github.com/](\"https://github.com/\"){{GitHub_username}}.atom

refer:  
[https://developer.github.com/v3/activity/feeds](\"https://developer.github.com/v3/activity/feeds\")


**Reading the Feeds**

The below is a Java code to parse the atom feeds.

    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.net.HttpURLConnection;
    import java.net.URL;
    
    import com.google.appengine.labs.repackaged.org.json.JSONException;
    import com.google.appengine.labs.repackaged.org.json.JSONObject;
    import com.google.appengine.labs.repackaged.org.json.XML;
    
    public class Feeds {
    	public static int PRETTY_PRINT_INDENT_FACTOR = 4;
    
    	public static JSONObject getFeeds(String inputUrl) throws Exception {
    		URL url = new URL(inputUrl);
    		HttpURLConnection conn = (HttpURLConnection) url.openConnection();
    
    		conn.setRequestMethod("GET");
    
    		String line, outputString = "";
    		BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    
    		while ((line = reader.readLine()) != null) {
    			outputString += line;
    		}
    		
    		JSONObject xmlJSONObj = null;
    		
    		try {
                xmlJSONObj = XML.toJSONObject(outputString);
                String jsonPrettyPrintString = xmlJSONObj.toString(PRETTY_PRINT_INDENT_FACTOR);
            } catch (JSONException je) {
                System.out.println(je.toString());
            }
    		return xmlJSONObj;	
    	}
    }

Using the above feed parses, read the atom feed and then you can format and display your GitHub feeds/timeline on your website.


  [1]: https://stackoverflow.blog/2011/07/01/its-ok-to-ask-and-answer-your-own-questions/
