---
layout: post
title: "Blocipedia"
categories: project
feature-img: "img/sample_feature_img.png"
thumbnail-path: "img/blocipedia_screenshots_740px.gif"
github-repo: "https://github.com/sleekramer/blocipedia"
short-description: Create, Share, and Update wikis on any subject you choose.
excerpt_separator: <!--more-->
date: 2015-11-23 11:27:48
order: 8
before:
  url: "portfolio/blocitoff"

---
## Summary

Blocipedia is an application that allows users to create public and private wikis for any topic they desire. All wikis are Markdown-based, public wikis are free to be edited by all users, and private wikis are only able to be updated by designated collaborators. Creation of private wikis, however, is a privilege reserved for premium users, who have paid the $15 upgrade fee. Blocipedia serves as a great place for users to create and share knowledge and ideas about any subject. Private wikis are only a
<!--more-->

## Explanation

I was the primary developer on this project and the user stories were provided in a project outline from Bloc. The user stories were used as a guide for the eventual functionality of the app, but I was given the liberty of implementing each feature as I saw fit.

As this was one of my earlier projects, I decided to get some experience with some common technologies. This was my first time working with Haml as a templating language, Foundation as a front-end framework, Devise and Pundit for user authentication and authorization, and stripe for payment.

## Problems and Solutions

# User Authorization with Pundit

I had three different user roles: standard, premium, and admin. In order to provide each role with a specified set of permissions, I needed to implement some sort of user authorization.  Enter, Pundit.

Pundit is a popular Ruby gem used for user authorization. The implementation of Pundit revolves around policy classes, which correspond to a model class in your application.  For Blocipedia, I created a WikiPolicy class that had access to a user and wiki objects. Then I created policy methods that corresponded to my controller actions in the WikisController but are suffixed with a "?". These policy methods just return a boolean value that indicates whether a user is authorized to perform a given controller action on a given wiki object. To use the policy methods in a controller, I simply needed to use the Pundit method `authorize`, which, in this case, takes a wiki object. Pundit calls `current_user` to determine the user, and it uses the context, in which `authorize` was called, to determine which policy method to run. In this way, I was able to write concise methods to authorize user actions in relation to the wiki model.

For example, the `WikisPolicy::create?` looks like this:

{% highlight ruby %}
def create?
  user.premium? || user.admin?
end
{% endhighlight %}

Since I allowed all users to create wikis, I use a conditional to call this `authorize` method only if the wiki being created is a private wiki.

The last benefit of using Pundit, is the allowance of a policy scope class, `class Scope` nested within the aforementioned policy class. Pundit assumes that this class will have access to a user object and a scope of some sort, which is passed in when you call the `policy_scope` Pundit method. The class also needs a `resolve` method that will return the proper scope of a collection based on the user's authorization. For the WikiPolicy::Scope class, the main purpose was to filter the scope of wikis index for users. I returned all wikis for admin users, and only public and privately owned or operated wikis for premium and standard users. Pundit makes the use of this class very simple in the corresponding controller:

{% highlight ruby %}
class WikisController ...

def index
  @wikis = policy_scope(Wiki.all)
end
{% endhighlight %}

The `:scope` object is set to all wikis and then the resolve method is automatically called and filters out the unauthorized wikis and returns the remainder to be assigned to the `@wikis` variable.

# Live Markdown Preview

I allowed users of Blocipedia to write and edit wiki content using markdown syntax. To display the content in a proper format, I created a Haml filter for markdown with the assistance of the RedCarpet gem. While this feature worked well, I wanted users to have a way to preview their wikis live while editing the content in markdown.

After some searching I discovered the Marked library, a markdown parser and compiler, which I was able to use in the wiki#edit and #new views to render a live preview. The benefit of using marked was the minimal setup and the ease of use, I just needed to include the library and then write a little snippet of Javascript:

{% highlight javascript %}
marked.setOptions({
  highlight: function (code) {
    return require('highlight.js').highlightAuto(code).value;
  }
});

var convertMarkdown = function(source, target) {
  var html = $(source).val();
  $(target).html(marked(html));
};

$(document).ready(function(){
  $('#wiki_title').keyup(function(){
    convertMarkdown($(this), '#markdown_title')
  });
  $('#wiki_body').keyup(function(){
    convertMarkdown($(this), '#markdown_body')
  });
});
{% endhighlight %}

I configured marked to user `highlight.js` and then wrote a simple function `convertMarkdown()`, which I used as a callback to be fired upon `keyup` events within the title and body input fields. The result produces a properly styled preview of the markdown as it is written by the user.

## Features

* Users must create an account in order to view the site content
* Users can upgrade to a premium account for $15, which allows them to create and manage private wikis (viewable only to those they grant access, and admins)
* Standard users can view all public wikis and private wikis that they contribute to
* Admins have authority to CRUD all wikis
* Wiki content is markdown-based and users can see a live preview of their content as they create or edit it

## Conclusion

The finished Blocipedia application provides users with a place to create and share their knowledge through markdown-based wikis. This project gave me the opportunity to explore technologies that I was unfamiliar with such as Foundation, Pundit, markdown parsers: Marked and RedCarpet, and Haml.
