# Learning by Doing 
Mackenzie Child's video really inspire me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# How to build a blog in Rails     
https://mackenziechild.me/12-in-12/2/ 

The blog is pretty simple, but we're able to add posts and comments and users, as well as authentication. So only sign-in users are able to edit and destroy, or even add post your comments.

## Highlights of this course
1. Users
2. Posts
3. Comment

# Create a new blog
```console
rails new blog
```

Chage directory to the blog. Under `blog/Gemfile`, add `gem 'therubyracer'`, save and run `bundle install`.
```console
bundle install
```