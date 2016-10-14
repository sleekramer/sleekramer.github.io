---
layout: post
title: Bloccit
categories: project
feature-img: "img/sample_feature_img.png"
thumbnail-path: "img/Bloccit_screenshots_740px.gif"
github-repo: "https://github.com/sleekramer/bloccit"
short-description: A Reddit replica, learning exercise for Ruby on Rails web development.
excerpt_separator: <!--more-->
date: 2015-11-08 19:21:19
order: 6
before:
  url: "portfolio/blocchat"
after:
  url: "portfolio/blocitoff"
---
## Summary

Bloccit is a Reddit replica site, so it operates in a manner very similar to that of Reddit.  Users are able to register an account and create and browse through topics, in which they are able to create, comment on, save, and vote on posts.
<!--more-->

## Explanation

The main purpose for building Bloccit was as an exercise for learning the ins-and-outs of web development using the Ruby on Rails framework.  Throughout the building process, I learned about everything from the basics of the MVC model to Rails scaffolds to updating page content using unobtrusive javascript.  Development of Bloccit was heavily guided by a tutorial, but that is not to say I haven't added a few personal touches to the application.  I was also aided by the instruction of my Bloc Mentor, Charlie Gaines.


## Problems and Solutions

# User Authentication and Sessions

Foregoing some of the more popular Rails Authentication systems such as Devise and OmniAuth, the user authentication system for Bloccit was built from scratch using BCrypt to encrypt passwords and the built in Rails Session object for authentication. Through building this authentication system, I was able to get a more grounded understanding of how a system verifies and protects a user's credentials and tracks the state of each user after they have signed in.

BCrypt has a `has_secure_password` method that will add methods to a User model that has a `:password_digest` attribute to authenticate a plain text password against a BCrypt encrypted password.  This allows us to simply store the BCrypt generated passwords and not the users' plain text passwords in our database.

The Rail's Session object is capable of tracking a user's data after they sign in to the application.  This means that they will only need to be authorized one time per session, rather than requiring the user to be authorized upon every single request they make to the application. The Session object will keep track of a signed in user and pass the generated Session Id along with all requests to and from the server.

## Features

* Users are able to create an account, sign in and sign out of sessions
* Users can be members or admins
  * Admins have the ability to create, update, and delete and post or topic
  * Members can only create, update, and delete their own posts or topics
  * Member and Admins can view private topics, which guests to the site cannot
* Users can create labels for their posts and topics
* Users can upvote or downvote posts, which affects the posts rank and display on the site
* Users can favorite posts in order to track them

## Conclusion

Overall building Bloccit was a great learning exercise at it covered a lot of the requisite knowledge needed for building an application using the Ruby on Rails framework.  
