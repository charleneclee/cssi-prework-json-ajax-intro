# JSON and AJAX

## Overview

* Understand the basics of Javascript Object Notation (JSON)
* Use AJAX to submit a request to an API
* Parse JSON Data from an API
* Get familiar with Spotify's API

## Introduction to JSON

Most likely, a few of your students will want to use external data in their project such as databases of movies, libraries of otter gifs, or map data from Google. Most data that developers want to access can be delivered via a format called JSON.

JSON stands for JavaScript Object Notation and it has become the defacto standard for computer-to-computer communication (APIs). Sites like Flickr, WeatherUnderground, and Spotify expose at least some of their data in a JSON format.

Once you open up a JSON file, you'll quickly see that the file is just a series of nested JavaScript objects. Accessing and modifying these objects uses the same syntax as any Javascript object.

Take this section of the prework as a primer. We will be able to dive more in depth with APIs and JSON at our in person training and your lead instructors will also be able to help you figure some of this out.

Before we get our hands dirty, install the Chrome extension [JSONView](https://github.com/jamiew/jsonview-chrome) and then open this page using a Chrome browser. Now that you've installed the extension, which will just make JSON easier to read, take a look at OpenWeatherMap's data for the weather in Palo Alto, which in OpenWeatherMap's database had an id of 5380748.

[http://api.openweathermap.org/data/2.5/weather?q=New%20York,us](http://api.openweathermap.org/data/2.5/weather?q=New%20York,us)

The page you opened probably looks something like this:

```json
"coord": {
  "lon": -122.14,
  "lat": 37.44
},
"weather": [
{
  "id": 701,
  "main": "Mist",
  "description": "mist",
  "icon": "50n"
}
],
"base": "stations",
"main": {
  "temp": 291.76,
  "pressure": 1018,
  "humidity": 72,
  "temp_min": 287.15,
  "temp_max": 294.15
},
"visibility": 16093,
"wind": {
  "speed": 4.1,
  "deg": 320
},
"clouds": {
  "all": 5
},
"dt": 1456454478,
"sys": {
  "type": 1,
  "id": 451,
  "message": 0.0298,
  "country": "US",
  "sunrise": 1456497854,
  "sunset": 1456538352
},
"id": 5380748,
"name": "Palo Alto",
"cod": 200
}
```
This is just a giant nested object. For example, to access the wind's speed, we would just need to call weatherData.wind.speed.

But first we need to use jQuery to load and save the JSON object into a variable.

Open your Javascript condole and  load jQuery into the console:
```js
var jq = document.createElement('script');
jq.src = "https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js";
document.getElementsByTagName('head')[0].appendChild(jq);
```
Now that jQuery is loaded we'll need to use AJAX to get our JSON data.

##AJAX
[AJAX](http://stackoverflow.com/a/1510156/2890716) stands for Asynchronous Javascript And Xml. In a nutshell, it allows your browser to send and fetch information to and from APIs without a page refresh. Pretty much, AJAX makes it possible to update parts of a web page, without reloading the whole page.

The first step is to load the data into your browser's console. To do this, we'll use jQuery's `ajax()` function:

```javascript
$.ajax({
  "url":  "http://charts.spotify.com/api/tracks/most_streamed/us/daily/latest",
  "method": "GET",
  "dataType": "JSONP"
}) // we'll add code here in a second
```

The `ajax()` function accepts an object as a parameter. This object has three keys
* url - the source of your JSON file, often from an API
* method - the type of request (GET or POST for example)
* dataType - how the data should be returned

While we do want our datatype to be JSON, we're going to specify JSONP. Don't worry too much about this for now, but if you insist on worrying about it, read [this](http://json-jsonp-tutorial.craic.com/index.html).

`.ajax()` will go get our data, but in order to use it, we will have to write instructions for how to parse it. We should only parse the object once we know we have successfully retrieved it. For this reason, the  `.success()` method is what we chain on to the return value of this AJAX request.

Once the `.ajax()` function grabs the JSONP object, we pass that data as a parameter to the `.success` callback function. You can name the returned object anything you'd like. In this case, we'll call it weatherData.

But weatherData only exists as the return value inside the .ajax() function. In order to see the object stored inside the weatherData variable, we can assign it a new variable, in this case myWeatherData.

Copy and paste the code below into your browser's console. The weather data for Palo Alto will be stored in a variable myWeatherData for you to parse.
```js
$.ajax({
  url:  "http://api.openweathermap.org/data/2.5/weather?id=5380748&appid=44db6a862fba0b067b1930da0d769e98",
  method: "GET",
  dataType: "JSON"
}).success(function(weatherData) { //weatherData is the name of the object returned by the AJAX request
  myWeatherData = weatherData; //assign object to the variable myWeatherData so that we can access it
});
```

## Parsing JSON Data
Now we can parse through myWeatherData just like any other JavaScript object.

Either dot notation or bracket notation can be used to access key names.
* Dot notation is easier to write
* Bracket notation allows access to special characters or variables in the key since the key is a "string"

```
>localData["coord"]["lon"]
-122.14
>localData.coord.lat
37.44
>myWeatherData["name"]
"Palo Alto"
>myWeatherData.main.temp_max
283.326
```


##Spotify API
Spotify has a great API for accessing data about music. You can learn about all the data Spotify will give you in [their API Documentation](https://developer.spotify.com/web-api/endpoint-reference/).  

In late 2015, as a Christmas present to the world, the Beatles released their entire catalog to 7 streaming music services including Spotify. Let's see which of their hits have been streamed the most since then. The Beatles Spotify Id is 3WrFJ7ztbogyGnTHbHJFl2. So according to the Spotify API Documentation, we can find data about their top tracks at  [https://api.spotify.com/v1/artists/3WrFJ7ztbogyGnTHbHJFl2/top-tracks?country=US](https://api.spotify.com/v1/artists/3WrFJ7ztbogyGnTHbHJFl2/top-tracks?country=US)

This object that you see on the page linked above is a JSON object. The property `tracks` points to an array of the 10 most popular Beatles songs. The first item in this array is the most streamed Beatles song.

### AJAX with Spotify's Chart API

The eventual goal of this section is for you to add the name of the most streamed song on Spotify to the end of this HTML page. To accomplish this goal, we'll break up the task into two steps:

* Making an AJAX Request
* Adding the Song Title to the DOM


### The AJAX Request to Spotify
This looks similar to last time, except the url has changed
```javascript
$.ajax({
  url:  "https://api.spotify.com/v1/artists/3WrFJ7ztbogyGnTHbHJFl2/top-tracks?country=US",
  method: "GET",
  dataType: "JSON"
}).success(function(beatlesData) {
  myFabFourData = beatlesData; //
});
```

Now that we've retrieved and stored the myFabFourData object, take a look at how the tracks are laid out.

For example, as of now, this is what the JSON data looks like for the first track, which we can access by indexing our data object at 0: 'myFabFourData.tracks[0]'. To get the link to track, we could use dot notation to dig one layer deeper: `myFabFourData.tracks[0].href`

```
"href": "https://api.spotify.com/v1/tracks/6dGnYIeXmHdcikdzNNDMm2",
"id": "6dGnYIeXmHdcikdzNNDMm2",
"name": "Here Comes The Sun - Remastered",
"popularity": 72,
"etc": "Links to the song, album, and artwork also appear here"

```

We can also see that there are 10 tracks listed from this url
```js
>myFabFourData
Object {tracks: Array[10]}
```

Finally we know the name of the 10th most popular track is (interestingly enough) Norwegian Wood.
```js
>myFabFourData.tracks[9].name
"Norwegian Wood (This Bird Has Flown) - Remastered"

```

### Adding the Song Title to the DOM

Non-developers never open the console, or if they do it's by complete accident, so parsing the data within the the console will be completely invisible to them.

Instead, we're going to open up the `spotify.html` file and use AJAX to manipulate that page's DOM.

To do this, we need to change our `.success()` function. Using jQuery's `.append()` method, we can easily add text as an '<p>' element to the DOM's `<body>` tag.

```javascript
$.ajax({
  url:  "https://api.spotify.com/v1/artists/3WrFJ7ztbogyGnTHbHJFl2/top-tracks?country=US",
  method: "GET",
  dataType: "JSON"
}).success(function(spotifyData) {
  var songTitle = spotifyData.tracks[0].name;
  $("body").append("<p> The Beatles most popular song on Spotify is: "+ songTitle + "<p>")
});
```

Assuming the Spotify Chart API is functional, running the code above from your browser should add some content to the end of your page. Try it out for yourself!

As a challenge, add a loop that goes through all of the Beatle's top tracks and prints them.
```js
for (i in myFabFourData.tracks) {
\\your code here
}
```

As a stretch challenge, see if you can link the title of each song so that it plays on Spotify.


## Resources

* [Wikipedia - APIs](https://en.wikipedia.org/wiki/Application_programming_interface)
* [SquareSpace Docs - What is JSON?](http://developers.squarespace.com/what-is-json/)
* [Copter Labs - JSON: What It Is and How to Use It](http://www.copterlabs.com/blog/json-what-it-is-how-it-works-how-to-use-it/)
* [StackOverflow - How Does AJAX Work?](http://stackoverflow.com/questions/1510011/how-does-ajax-work)
* [jQuery Documentation](http://jquery.com/)

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/cssi-prework-json-ajax-intro'>JSON and Ajax</a> on Learn.co and start learning to code for free.</p>
