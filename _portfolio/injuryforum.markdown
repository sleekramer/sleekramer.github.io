---
layout: post
title: "InjuryForum"
categories: project
feature-img: "img/sample_feature_img.png"
thumbnail-path: "img/InjuryForum_screenshots_740px.gif"
short-description: Forum site for skeletomuscular injuries.
excerpt_separator: <!--more-->
date: 2016-02-17 21:14:47
---
## Summary

There are a plethora of diagnostic and informative medical websites and applications, but there is no place for patients to share their experiences with treatment and recovery. Injury Forum provides such a space.  Users are able to share their stories about recovery, as well as their treatment plans and methods in order to provide a first or second opinion to fellow patients, who often are subscribed a single option for recovery.
<!--more-->
## Explanation

I decided to build this application as my capstone project during my time at Bloc. Each stage of the development process for this application was directed by me and the majority of the codebase was written by me, as well. Throughout the building process, my mentor, Charlie Gaines, provided helpful and insightful feedback, which directed some of the decisions I made.  The application was built over the course of five weeks during my Bloc Rails Development Apprenticeship.

## Problems and Solutions

# Image Upload

One of the features I wanted to include was the ability for users to upload photos when they created a new forum page. While there are many services I could have used, I decided to use Refile and AWS S3.  

The Refile gem requires the creation of an image model, that can be polymorphically associated with other models. Once you have the image model setup and the associations made, Refile makes it very simple for you to connect an S3 bucket to your application by generating an initializer file, in which you specify your image storage service. In this case, I had to include the information for my S3 bucket like so:

{% highlight ruby %}
aws = {
  access_key_id: ENV["ACCESS_KEY_ID"],
  secret_access_key: ENV["SECRET_ACCESS_KEY"],
  region: "region",
  bucket: "bucket-name",
}
{% endhighlight %}

The last piece of the puzzle was to complete the form submission for images, which requires the use of the Refile :`:images_files` object.

# Search

The search feature for Injury Forum needed to allow users to be able to search for body parts, activities, injuries, and posts.  Since I needed to search across multiple tables I initially implemented my search functionality using SearchKick and Elastic Search. This solution worked well in development, but upon deployment, I realized that I needed to implement a different solution for financial reasons (running an Elastic Search server is not cheap).

I was glad to have gained the experience in learning how to integrate Elastic Search into a Rails app, but I eventually decided to use the built-in search functionality of PostgreSQL. This time I opted to use the pg_search gem, which provides helpful methods for working with PostgreSQL.  

Once the gem is installed, I had to `include PgSearch` in all the models, which I wanted to search, and then specify which attributes I wanted to be searchable using `multisearchable :against => [:attribute]`.  Then, in the Searches Controller, which also needs to `include PgSearch`, I made use of the `PgSearch.multisearch` function, which searches for a parameter from the search from submission, which I called `:query`.

The last step was to create the view for search results.

# Activity Feed

After users have signed in, or have opted to browse the site, they are directed to the Browse page. On this page there is a side bar with links to all of the Body Part pages, and I wanted to include an activity feed with links to some of the most recent activity on the site.  In order to achieve this I needed to have a model to track certain activities on the site, which I called `feed_items`, since I already had an Activity model.  

In order to simplify the creation process of `feed_items`, I created a service: `AddUserFeedItems`, since all feed_items needed to be associated with a user.  Then I was able to utilize that service in any controller action, which I wanted to also create a feed_item. Effectively this created feed_items readily in a simple and reusable manner.  

On the browse page, the server simply delivers the ten most recent feed_items, and they are styled for display by a helper method.

## Features

* Users can register for an account (Devise)
* Users can create new injury pages, which are associated with a body part
  * This process includes selecting from a preloaded set of ~200 symptoms
  * uploading any images
  * and writing a brief description about the injury
    * There is a Description topic, which serves as a place for other users to post a more comprehensive description if they see fit, and the highest voted post within that topic is displayed on the injury page
* Users can create posts under the predesignated discussion topics for each injury
* Users are able to vote on posts to indicate whether they are helpful
* Users can follow injuries and posts, and view their followed content in a user profile
* Users can indicate what activity they were participating in when they incurred a specific injury, and see a list of the top five most common activities on each injury page

## Conclusion

If I were to build this project again, I would probably decide to make more intimate associations between the models used in order to simplify ActiveRecord queries used throughout the application. For instance, instead of having to go down the rabbit whole of Body Part to Injury to Topic to Posts, it would have been nice to have a way to query for Posts directly from a Body Part object. There are other features that I would want to consider alternative solutions for as well, such as using helper methods to deal with objects of more than one class. Overall, though I am pleased with the application as it stands.

Injury Forum was an application of my own design and implementation, which allowed me to learn about the full process of creating a web application from conception to production. Throughout this project I was challenged to find solutions to some common problems faced in web development such as search, activity feeds, and image uploads. I hope to continue working on this project in the future when I have a little more financial freedom.
