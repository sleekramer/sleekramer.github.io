---
layout: post
title: "BlocJams and BlocJamsAngular"
categories: project
feature-img: "img/sample_feature_img.png"
thumbnail-path: "img/BlocJams_screenshots_740px.gif"
short-description: A Spotify replica, learning exercise for vanilla Javascript, JQuery, HTML, CSS, and AngularJS.
excerpt_separator: <!--more-->
date: 2016-02-29 11:38:34
order: 3
before:
  url: "portfolio/injuryforum"
after:
  url: "portfolio/blocmetrics"
---
## Summary

BlocJams is a Spotify replica that was built for the purpose of learning the basics of Javascript and JQuery. The app consists of three main views for the home page, collections, and albums and includes a simple audio player with play/pause, next/previous and seek-bar functionalities.

BlocJamsAngular is a refactoring of the original using AngularJS in place of JQuery and vanilla Javascript.
<!--more-->

## Explanation

These projects were guided by a tutorial through Bloc.  They were built as part of my Bloc apprenticeship with my mentor, John O'Connor.

## Problems and Solutions

# Music Player Seek Bar

One of the trickiest features to implement in BlocJams was the seek bar functionality for tracking song location. The seek bar needed to update automatically as the song played through as well as respond to clicks and mousemoves on the head to adjust the display of the seek bar and the playback time of the song.

The first thing to do what create a function that would update the css of the head and fill of the seek bar based on the current playback percentage or relative position of a click or mousemove that should update the seek bar:

{% highlight javascript %}
var updateSeekPercentage = function ($seekBar, seekBarFillRatio) {
    var offsetXPercent = seekBarFillRatio * 100;
    offsetXPercent = Math.max(0, offsetXPercent);
    offsetXPercent = Math.min(100, offsetXPercent);

    var percentageString = offsetXPercent + '%';
    $seekBar.find('.fill').width(percentageString);
    $seekBar.find('.thumb').css({left: percentageString});
};
{% endhighlight %}

The next step was to listen for the two types of events for the seek bar (clicks and mousemove). For each event the callback function will find the relative position of the event, click or mousemove followed by mouseup, and use that to calculate the `seekBarFillRatio`, which is subsequently passed to the `updateSeekPercentage()` function.

Once that functionality was working properly, the last thing to do was to link the seek bar to the currently playing song file via the [Buzz library](http://buzz.jaysalvat.com/). Buzz comes with a custom event called `timeupdate` that repeatedly fires during song playback.  I bound the `timeupdate` event to current song file and then used the `Buzz.getTime()` and `Buzz.getDuration()` functions to calculate the `seekBarFillRatio`.  Then using the `Buzz.setTime()` function, I wrote an additional function to add to the click and mousemove events that would use the same `seekBarFillRatio` as well as the songs total time from `.getDuration()` to update the playback position of the song.

# Refactoring to AngularJS

While Javascript and JQuery are a perfectly fine way to implement the client-side portions of BlocJams, AngularJS is a good alternative for a SPA such as BlocJams. The biggest challenge here was learning the ins-and-outs of Angular in order to implement all of the BlocJams features. The logic had already been thought out in the first implementation of BlocJams, so the goal was to Angularize all of the application.

Using Angular directives and services, much of the work needed to complete the refactoring was quite a pleasure, as these features of the framework are what really make Angular so dynamic. The modularity and organizational setup of Angular gives a much more elegant feel and structure to the code base as well.  

Using UI-Router for routing and assigning Controllers and Templates to each state of the application gives a nice sense of connectivity between the independent portions of the application. And the pattern of dependency injection relieves the worry of global objects and values that comes with using vanilla javascript.


## Features

* Users can browse through a collection of albums
* Users can view each album, which contains a list of all the songs with duration
* Users can play, pause, and switch to the previous or next songs in the list via the player bar
* Users can adjust the playback position of the song and the playback volume using seek bars within the player bar

## Conclusion

As a learning exercise, BlocJams was a great way to learn the basics of Javascript, JQuery and dealing with DOM manipulation. I was able to play around with event listeners and right my own functions and selectors in order to accomplish the desired functionality. I was also introduced to some of the finer points in CSS that involve browser compatibility such as the 'clearfix' solution for floats and the 'boarder-box' property that can help to normalize the appearance of DOM elements across browsers. There was even an opportunity to throw in a CSS animation. Learning the inner workings of the dynamic AngularJS framework also broadened my perspective on the things that are possible when working with Javascript. It is exciting to know that Angular is just the tip of the iceberg when it comes to javascript frameworks.  

All in all, building BlocJams and refactoring the project to use AngularJS was an great way to expand my knowledge of programming in Javascript and the many frameworks and libraries written in javascript.
