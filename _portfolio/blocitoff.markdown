---
layout: post
title: "Blocitoff"
categories: project
feature-img: "img/sample_feature_img.png"
thumbnail-path: "img/Blocitoff_screenshot_740px.gif"
short-description: To-do List Application with API.
excerpt_separator: <!--more-->
date: 2016-02-29 17:12:41

---
## Summary

Blocitoff is a to-do list application that allows a user to dynamically create and mark items for completion. Often tasks are a bit more complicated than one simple step, so Blocitoff permits users to create sub-list of items which must be completed before the overall item may be marked as complete.  Upon marking an item as complete, Blocitoff clears that item from the list keeping the to-do list free from clutter.

Also included in Blocitoff is an API for clients to make HTTP requests directly to the server. This allows for other potential applications and command-line interfaces to gain access to Blocitoff.
<!--more-->
## Explanation

The basic specifications for Blocitoff were provided by Bloc, but beyond that, I was able to approach the development process in my own manner. With guidance from my Bloc mentor, Charlie Gaines, I wrote the code for this application myself. During the development of this project I was faced with writing my first scripts for DOM manipulation using JQuery and unobtrusive javascript. Until this point, I had briefly encountered JQuery. Another piece of this project that was particularly enlightening was creating the API, which was the first that I had implemented on my own. Using the "tiddle" gem I was able to implement token based user authentication for the Blocitoff API as well.

## Problems and Solutions

# API Token Authentication with Tiddle and Devise

I wanted to implement an API for Blocitoff to allow users to manage their to-do items either from the command-line or build third party applications that could access Blocitoff. In order to make the API endpoints accessible only to Blocitoff users, I needed to implement some sort of user authentication. Since I was already using Devise, a common Rails user authentication gem, in the main portion of my application, I wanted to find a solution that would utilize the Devise system already in place.

After some searching, I discovered the Tiddle gem, a lightweight token-based authentication solution for Devise. I had to install the gem and execute some setup to get it working. First, I had to add a `:token_authenticatable` attribute to my Devise::User model. Then I generated a model to store authentication tokens, which Tiddle requires to have four attributes: body, last_used, ip_address, and user_agent. After assigning the requisite `:has_many` and `:belongs_to` relationships for authentication tokens, api user sessions controller to generate and return an authentication token upon session creation:

{% highlight ruby %}
def create
  user = warden.authenticate!(scope: :user)
  token = Tiddle.create_and_return_token(user, request)
  render json: { authentication_token: token }
end
{% endhighlight %}

Running the application locally, a user can sign in to Blocitoff from the command-line with the following curl command:

`$ curl -v -H "Accept: application/json" -H "Content-type: application/json" -X POST -d '{"user":{"email":"[user.email]","password":"[user.password]"}}' localhost:3000/api/users/sign_in`.

With a proper email and password combination, the application should respond with an authentication token: `{"authentication_token":"BdyWovLfPPLicV_sXneS"}`.

Once the user has the authentication token, they can make other requests to the Blocitoff api as long as they include X-USER-EMAIL and X-USER-TOKEN headers in their requests.

## Features

* Users must create an account to use the application
* Users are redirected to their to-do list upon signing in
* Users can create, and destroy items on their to-do list
* Users can create a sub-list of to-do list items for each of the main items on their profile
  * Once each item in the sub-list of items is completed, the parent item may be destroyed

## Conclusion

Blocitoff presented a great opportunity for me to further expand on my Ruby on Rails development skills. By incorporating some unobtrusive javascript for item creation and destruction as well as creating an API for external access to Blocitoff, I was able to independently learn about JQuery and construct an API from scratch.
