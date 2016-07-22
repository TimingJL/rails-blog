# Learning by Doing 
Mackenzie Child's video really inspire me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# Project 2: How To Build A Blog In Rails     
https://mackenziechild.me/12-in-12/2/ 

The blog is pretty simple, but we're able to add posts and comments and users, as well as authentication. So only sign-in users are able to edit and destroy, or even add post your comments.

### Highlights of this course
1. Users
2. Posts
3. Comment

# Create a new blog
```console
$ rails new blog
```

Chage directory to the blog. Under `blog/Gemfile`, add `gem 'therubyracer'`, save and run `bundle install`.      
Note: Because there is no Javascript interpreter for Rails on Ubuntu Operation System, we have to install `Node.js` or `therubyracer` to get the Javascript interpreter.
```console
$ bundle install
```

Then run the `rails server` and go to `http://localhost:3000` to make sure everything is correct.

Git initialize:
```console
$ git init
$ git add .
$ git commit -am 'Initial Commit'
```

# Post
Last time, we build the reddit clone mainly through scaffold, so this time we're going to try to do it everything manually.    
We're going to start with the post. Let's go ahead to create a controller
```console
$ rails g controller posts
```
Then we need to create some routes.     
`blog/config/routes.rb`. We need the post to be at the root of the application.    
```ruby
Rails.application.routes.draw do
  resources :posts
  root "posts#index"
end
```
And I know the controller needs a index action, so let's open up the post controller and define it.     
`/blog/app/controllers/posts_controller.rb`

```ruby
class PostsController < ApplicationController
	def index
	end
end
```

Now, if we run the rails server and go to `http://localhost:3000`, we will get an error "Template is missing" which tells us exactly that we need to create a `view`.     
So under `app/views/posts`, we are going to add a new file `index.html.erb`.
Let's put a placeholder:

```html
	
	<h1>This is the index.html.erb file.. Yay!</h1>
```
Then go back to our browser and refresh, you'll see the index page.

### To Create New Posts

Next, we want to have the ability to create new posts.     
To do that, let's start in our post controller and we're gonna add a new action.
```ruby
class PostsController < ApplicationController
	def index
	end

	def new
	end
end
```
Then, under `app/views/posts`, let's create a new file and we'll call this `new.html.erb`.

Then, we need to add a form. So I'm going to do user rails form for method
```html

	<h1>New post!</h1>

	<%= form_for :post, url: posts_path do |f| %>
		<p>
			<%= f.label :title %><br>
			<%= f.text_field :title %><br>
		</p>

		<p>
			<%= f.label :body %><br>
			<%= f.text_area :body %>
		</p>

		<p>
			<%= f.submit %>
		</p>	
	<% end %>
```

Then, if we go back to our browser, we should be able to go `http://localhost:3000/posts/new`. 
And we'll see the new form now.

Now, we have a new form, but we don't have a model to associate it with which means we won't be able to save our data.

### To Save Our Posts Data

So let's do that next inside of our terminal. Let's generate a model.
We're going to do the fields we just created in our form `new.html.erb`, title and body.
```console
$ rails g model Post title:string body:text
```
And you can see that generates a migration file for us the model file as well as some tests files. You can see those under `blog/db/migrate/xxxxxxxxxxxxxx_create_posts.rb`       
So back to our terminal, we need to migrate our database.
```console
$ rake db:migrate
```
And that creates the migration for us.

Now, let's focus on the controller.
In order to save our post, we need to define a `create` method.
Under `app/controllers/posts_controller.rb`

```ruby
class PostsController < ApplicationController
	def index
	end

	def new
	end

	def create
		@post = Post.new(post_params)
		@post.save

		redirect_to @post
	end	
end
```

Rails4 has a security feature called a strong parameters you have to define or explicitly say what parameters you want to allow.
So under the private method:
```ruby
class PostsController < ApplicationController
	def index
	end

	def new
	end

	def create
		@post = Post.new(post_params)
		@post.save

		redirect_to @post
	end	

	private
		def post_params
			params.require(:post).permit(:title, :body)
		end
end
```

Then, we need to create our show page to show our new post.
Let's do that under `app/views/posts`, we're going to create a new file called `show.html.erb`.
This is going to be where the actual post lives.
For the post, we want to show the title, the body, and also when the post submitted that.
Under `app/views/posts/show.html.erb`
```html

	<h1 class="title">
		<%= @post.title %>
	</h1>

	<p class="data">
		Submitted <%= time_ago_in_words(@post.created_at) %> Ago
	</p>


	<p class="body">
		<%= @post.body %>
	</p>
```
So we can save this but it's not going to work because we're grabbing at post which we have not defined yet.
Before we refresh, let's go ahead and do that     
Under `app/controllers/posts_controller.rb`
```ruby
def show
	@post = Post.find(params[:id])
end
```
So now we can new post in `http://localhost:3000/posts/new`

### To List Out All Of the Posts

Then, we want to list out all of the post that's going to be at slash or at the root of the application(http://localhost:3000).

So first we need to edit are `index action`.     
Under `app/controllers/posts_controller.rb`
```ruby
class PostsController < ApplicationController
	def index
		@posts = Post.all
	end
	...
```
And this would work by itself. But I also want to sense the blog. I want the newer posts to show up on top.
So we're gonna to order and then created at descending. So this going to reverse the post order.
```ruby
class PostsController < ApplicationController
	def index
		@posts = Post.all.order('created_at DESC')
	end
	...
```

And then inside of our `app/views/posts/index.html.erb`     
I'm going to do a loop through each of the posts.
So for each of the post, I want to put them in a post wrapper `div` and the title of the post is going to be in `h2` tag class of title.
And we're gonna to do link to `post.title` and that's going to be the post path.
And then, on the index page, I don't want the description we're just going to do the time at risk or the date it was created that.
On the index, I want to have the actual date.
```ruby

	<% @posts.each do |post| %>
		<div class="post_wrapper">
			<h2 class="title"><%= link_to post.title, post %></h2>
			<p class="date"><%= post.created_at.strftime("%B %d, %Y") %></p>
		</div>
	<% end %>
```
We still have the ability to edit or delete a post yet.
I'm just going to go ahead and commit what we have.
```console
$ git add .
$ git commit -am 'Add posts'
```

## Styling
Next, I want to add a little structure navigation to our app before you move on as well as some styling to make it look a bit nicer.
Let's do it on a new branch.
```console
$ git checkout -b styling
```

### To Create the Sidebar

We are focus on the layout     
`app/views/layouts/application.html.erb`
I'm going to create a sidebar that's fixed a header and then the main body.
```html

	<!DOCTYPE html>
	<html>
	  <head>
	    <title>Blog</title>
	    <%= csrf_meta_tags %>
	    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
	    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
	  </head>

	<body>
		<div id="sidebar">
			<div id="logo">
				<%= link_to root_path do %>
					<%= image_tag "logo.png" %>
				<% end %>
			</div>

			<ul>
				<li class="category">Website</li>
				<li><%= link_to "Blog", root_path %></li>
				<li>About</li>
			</ul>

	  		<ul>
				<li class="category">Social</li>
				<li><a href="https://www.facebook.com/timing.chen">Facebook</a></li>
				<li><a href="https://www.instagram.com/timingjl">Instagram</a></li>
				<li><a href="https://github.com/TimingJL">Github</a></li>
				<li><a href="mailto:eefozeo@gmail.com">Email</a></li>
	  		</ul>
	  </div>


	    <%= yield %>
	  </body>
	</html>
```
I went ahead and added the application are added the logo as well as a few other pictures we're going to be using inside of this application.
And I added it to `app/assets/images`

Then we edit the css file `app/assets/stylesheets/application.css`
First, we need to rename to `application.css.scss`
We're going to user normalize. So I'm gonna to create a new stylesheets. And it's going to be a partial, so I'm going to call this underscore normalize `app/assets/stylesheets/_normalize.css.scss`. And paste the normalize stuff in.
```scss
/*! normalize.css v3.0.1 | MIT License | git.io/normalize */

/**
 * 1. Set default font family to sans-serif.
 * 2. Prevent iOS text size adjust after orientation change, without disabling
 *    user zoom.
 */

html {
  font-family: sans-serif; /* 1 */
  -ms-text-size-adjust: 100%; /* 2 */
  -webkit-text-size-adjust: 100%; /* 2 */
}

/**
 * Remove default margin.
 */

body {
  margin: 0;
}

/* HTML5 display definitions
   ========================================================================== */

/**
 * Correct `block` display not defined for any HTML5 element in IE 8/9.
 * Correct `block` display not defined for `details` or `summary` in IE 10/11 and Firefox.
 * Correct `block` display not defined for `main` in IE 11.
 */

article,
aside,
details,
figcaption,
figure,
footer,
header,
hgroup,
main,
nav,
section,
summary {
  display: block;
}

/**
 * 1. Correct `inline-block` display not defined in IE 8/9.
 * 2. Normalize vertical alignment of `progress` in Chrome, Firefox, and Opera.
 */

audio,
canvas,
progress,
video {
  display: inline-block; /* 1 */
  vertical-align: baseline; /* 2 */
}

/**
 * Prevent modern browsers from displaying `audio` without controls.
 * Remove excess height in iOS 5 devices.
 */

audio:not([controls]) {
  display: none;
  height: 0;
}

/**
 * Address `[hidden]` styling not present in IE 8/9/10.
 * Hide the `template` element in IE 8/9/11, Safari, and Firefox < 22.
 */

[hidden],
template {
  display: none;
}

/* Links
   ========================================================================== */

/**
 * Remove the gray background color from active links in IE 10.
 */

a {
  background: transparent;
}

/**
 * Improve readability when focused and also mouse hovered in all browsers.
 */

a:active,
a:hover {
  outline: 0;
}

/* Text-level semantics
   ========================================================================== */

/**
 * Address styling not present in IE 8/9/10/11, Safari, and Chrome.
 */

abbr[title] {
  border-bottom: 1px dotted;
}

/**
 * Address style set to `bolder` in Firefox 4+, Safari, and Chrome.
 */

b,
strong {
  font-weight: bold;
}

/**
 * Address styling not present in Safari and Chrome.
 */

dfn {
  font-style: italic;
}

/**
 * Address variable `h1` font-size and margin within `section` and `article`
 * contexts in Firefox 4+, Safari, and Chrome.
 */

h1 {
  font-size: 2em;
  margin: 0.67em 0;
}

/**
 * Address styling not present in IE 8/9.
 */

mark {
  background: #ff0;
  color: #000;
}

/**
 * Address inconsistent and variable font size in all browsers.
 */

small {
  font-size: 80%;
}

/**
 * Prevent `sub` and `sup` affecting `line-height` in all browsers.
 */

sub,
sup {
  font-size: 75%;
  line-height: 0;
  position: relative;
  vertical-align: baseline;
}

sup {
  top: -0.5em;
}

sub {
  bottom: -0.25em;
}

/* Embedded content
   ========================================================================== */

/**
 * Remove border when inside `a` element in IE 8/9/10.
 */

img {
  border: 0;
}

/**
 * Correct overflow not hidden in IE 9/10/11.
 */

svg:not(:root) {
  overflow: hidden;
}

/* Grouping content
   ========================================================================== */

/**
 * Address margin not present in IE 8/9 and Safari.
 */

figure {
  margin: 1em 40px;
}

/**
 * Address differences between Firefox and other browsers.
 */

hr {
  -moz-box-sizing: content-box;
  box-sizing: content-box;
  height: 0;
}

/**
 * Contain overflow in all browsers.
 */

pre {
  overflow: auto;
}

/**
 * Address odd `em`-unit font size rendering in all browsers.
 */

code,
kbd,
pre,
samp {
  font-family: monospace, monospace;
  font-size: 1em;
}

/* Forms
   ========================================================================== */

/**
 * Known limitation: by default, Chrome and Safari on OS X allow very limited
 * styling of `select`, unless a `border` property is set.
 */

/**
 * 1. Correct color not being inherited.
 *    Known issue: affects color of disabled elements.
 * 2. Correct font properties not being inherited.
 * 3. Address margins set differently in Firefox 4+, Safari, and Chrome.
 */

button,
input,
optgroup,
select,
textarea {
  color: inherit; /* 1 */
  font: inherit; /* 2 */
  margin: 0; /* 3 */
}

/**
 * Address `overflow` set to `hidden` in IE 8/9/10/11.
 */

button {
  overflow: visible;
}

/**
 * Address inconsistent `text-transform` inheritance for `button` and `select`.
 * All other form control elements do not inherit `text-transform` values.
 * Correct `button` style inheritance in Firefox, IE 8/9/10/11, and Opera.
 * Correct `select` style inheritance in Firefox.
 */

button,
select {
  text-transform: none;
}

/**
 * 1. Avoid the WebKit bug in Android 4.0.* where (2) destroys native `audio`
 *    and `video` controls.
 * 2. Correct inability to style clickable `input` types in iOS.
 * 3. Improve usability and consistency of cursor style between image-type
 *    `input` and others.
 */

button,
html input[type="button"], /* 1 */
input[type="reset"],
input[type="submit"] {
  -webkit-appearance: button; /* 2 */
  cursor: pointer; /* 3 */
}

/**
 * Re-set default cursor for disabled elements.
 */

button[disabled],
html input[disabled] {
  cursor: default;
}

/**
 * Remove inner padding and border in Firefox 4+.
 */

button::-moz-focus-inner,
input::-moz-focus-inner {
  border: 0;
  padding: 0;
}

/**
 * Address Firefox 4+ setting `line-height` on `input` using `!important` in
 * the UA stylesheet.
 */

input {
  line-height: normal;
}

/**
 * It's recommended that you don't attempt to style these elements.
 * Firefox's implementation doesn't respect box-sizing, padding, or width.
 *
 * 1. Address box sizing set to `content-box` in IE 8/9/10.
 * 2. Remove excess padding in IE 8/9/10.
 */

input[type="checkbox"],
input[type="radio"] {
  box-sizing: border-box; /* 1 */
  padding: 0; /* 2 */
}

/**
 * Fix the cursor style for Chrome's increment/decrement buttons. For certain
 * `font-size` values of the `input`, it causes the cursor style of the
 * decrement button to change from `default` to `text`.
 */

input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  height: auto;
}

/**
 * 1. Address `appearance` set to `searchfield` in Safari and Chrome.
 * 2. Address `box-sizing` set to `border-box` in Safari and Chrome
 *    (include `-moz` to future-proof).
 */

input[type="search"] {
  -webkit-appearance: textfield; /* 1 */
  -moz-box-sizing: content-box;
  -webkit-box-sizing: content-box; /* 2 */
  box-sizing: content-box;
}

/**
 * Remove inner padding and search cancel button in Safari and Chrome on OS X.
 * Safari (but not Chrome) clips the cancel button when the search input has
 * padding (and `textfield` appearance).
 */

input[type="search"]::-webkit-search-cancel-button,
input[type="search"]::-webkit-search-decoration {
  -webkit-appearance: none;
}

/**
 * Define consistent border, margin, and padding.
 */

fieldset {
  border: 1px solid #c0c0c0;
  margin: 0 2px;
  padding: 0.35em 0.625em 0.75em;
}

/**
 * 1. Correct `color` not being inherited in IE 8/9/10/11.
 * 2. Remove padding so people aren't caught out if they zero out fieldsets.
 */

legend {
  border: 0; /* 1 */
  padding: 0; /* 2 */
}

/**
 * Remove default vertical scrollbar in IE 8/9/10/11.
 */

textarea {
  overflow: auto;
}

/**
 * Don't inherit the `font-weight` (applied by a rule above).
 * NOTE: the default cannot safely be changed in Chrome and Safari on OS X.
 */

optgroup {
  font-weight: bold;
}

/* Tables
   ========================================================================== */

/**
 * Remove most spacing between table cells.
 */

table {
  border-collapse: collapse;
  border-spacing: 0;
}

td,
th {
  padding: 0;
}
```
And then, inside of our `app/assets/stylesheets/application.css.scss`, I'm simply going to import it.
```scss
@import 'normalize';
```

So now, you can see if we refresh, it changes the fonts a bit. I'm removes the margin and all that.
To styling, we paste the css to the `app/assets/stylesheets/application.css.scss`.
```scss
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or vendor/assets/stylesheets of plugins, if any, can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any styles
 * defined in the other CSS/SCSS files in this directory. It is generally better to create a new
 * file per style scope.
 *
 *= require_tree .
 *= require_self
 */

@import 'normalize';

html, body {
	font-family: 'Raleway', sans-serif;
}

h1, h2, h3, h4, h5, h6 {
	font-weight: 500;
}

a {
	text-decoration: none;
	color: inherit;
}

#sidebar {
	width: 250px;
	position: fixed;
	left: 0;
	top: 0;
	height: 100%;
	background: #f5f7f9;
	padding: 7em 0 0 0;
	border-right: 1px solid #d6dce0;
	#logo {
		width: 40px;
		position: absolute;
		right: 3em;
		top: 3em;
	}
	ul {
		list-style: none;
		text-align: right;
		padding-right: 3em;
		.category {
			font-weight: 700;
			font-size: 0.7em;
			text-transform: uppercase;
			color: #33acb7;
		}
		li {
			padding: .5em 0;
			a {
				color: #9eafba;
				text-decoration: none;
				transition: all .4s ease;
				&:hover {
					color: #33acb7;
				}
			}
		}
		.active {
			a {
				color: #33acb7;
			}
		}
	}
	.sign_in {
		position: absolute;
		right: 3em;
		top: 80%;
		font-size: .8em;
		color: #9eafba;
	}
}

.button {
	outline: none;
	background: transparent;
	border: 1px solid #d6dce0;
	padding: .5em 1.5em;
	border-radius: 1.5em;
	&:hover {
		border: 1px solid #33acb7;
		color: #33acb7;
		a {
			color: #33acb7 !important;
		}
	}
}

.clearfix:after {
   content: ".";
   visibility: hidden;
   display: block;
   height: 0;
   clear: both;
}

#main_content {
	margin-left: 250px;
	#header {
		padding: 1em 3em;
		border-bottom: 1px solid #d6dce0;
		background: #f5f7f9;
		color: #9eafba;
		p {
			display: inline;
		}
		a {
			color: #9eafba;
			text-decoration: none;
		}
		.buttons {
			float: right;
			margin-top: -6px;
			.button {
				font-size: .8em;
				margin-left: .5em;
			}
		}
	}
	.post_wrapper {
		padding: 3em;
		border-bottom: 1px solid #d6dce0;
		.title {
			margin: 0;
			a {
				font-weight: 500;
				text-decoration: none;
				color: #2a2f35;
				font-size: 1.5em;
				&:hover {
					color: #33acb7;
				}
			}
		}
		.date_and_author {
			color: #9eafba;
			margin: .5em 0 0 0;
		}
	}
	#post_content {
		padding: 1em 3em;
		.title {
			font-weight: 500;
			text-decoration: none;
			color: #2a2f35;
			font-size: 2.5em;
			margin-bottom: 0;
		}
		.body {
			font-size: 1.1em;
			line-height: 1.75;
		}
		.date_and_author {
			color: #9eafba;
			margin: .5em 0 2em 0;
		}
		#comments {
			h2 {
				margin: 3em 0 1em 0;
				border-bottom: 1px solid #d6dce0;
				padding-bottom: 0.5em;
			}
			h3 {
				margin-top: 2em;
			}
			.comment {
				border-bottom: 1px solid #d6dce0;
				padding: 1.5em 2em;
				.clear_both {
					clear: both;
				}
				&:after {
					clear: both;
				}
				.comment_content {
					float: left;
					.comment_name {
						margin: 1em 0 0 0;
						font-size: 0.7em;
						text-transform: uppercase;
					}
					.comment_body {
						font-size: 1.2em;
						margin: 0.2em 0 0 0;
					}
					.comment_time {
						margin-top: 1.2em;
						font-size: .8em;
					}
				}
				.button {
					float: right;
				}
			}
			input[type="text"], textarea {
				width: 50%;
			}
		}
	}
	#page_wrapper {
		padding: 3em;
		#profile_image {
			width: 300px;
			float: left;
			margin-right: 2em;
			img {
				width: 100%;
				border-radius: 0.35em;
			}
		}
		#content {
			h1 {
				font-weight: 500;
			}
			p {
				font-size: 1.1em;
				line-height: 1.75;
			}
			a {
				color: #33acb7;
				font-weight: 700;
				text-decoration: none;
			}
		}
	}
	.links {
		margin: 2em 0;
	}
	input[type="text"], input[type="email"], input[type="password"], textarea {
		width: 90%;
		border: 1px solid #d6dce0;
		border-radius: .35em;
		margin-top: 10px;
		padding: .5em 1em;
		line-height: 1.75;
	}
	input[type="text"] {
		height: 35px;
	}
	textarea {
		min-height: 180px;
	}
	input[type="submit"] {
		outline: none;
		background: transparent;
		border: 1px solid #d6dce0;
		padding: .5em 1.5em;
		font-size: 1.1em;
		border-radius: 1.5em;
		margin-left: .5em;
		&:hover {
			border: 1px solid #33acb7;
			color: #33acb7;
		}
	}
}
```

Basically, we're setting font all the sidebar styles.
If we inspect the source, we still have the post. They're just hidden behind the sidebar right now because we have not added the wrapper to the `application.html.erb`

### To Add Navbars and Sign-in Button

So few more things we want to do is that I want to add a sign-in button.
`app/views/layouts/application.html.erb`
```html

	<p class="sign_in">Admin Login</p>
```

We have not yet added the google font. So to do that. I grabbed the font of google font and I define that in the CSS, but we haven't include that in our application. So I'm going to do that now.
`app/views/layouts/application.html.erb`
```html

	<head>
	...
	<%= stylesheet_link_tag 'application', 'http://fonts.googleapis.com/css?family=Raleway:400,700' %>
	...
	</head>
```
This is a linking to the google font API.
So if we refresh this, you can see the fonts are now changed to what they should be.

Alright, so let's go ahead and keep working under the sidebar, we're going to add a main content area.
And inside of here, I want a header.
And also I want to add some buttons for us to add a new post and sign-in or logout.
`app/views/layouts/application.html.erb`

```html

  <body>
	...
  <div id="main_content">
  	<div id="header">
  		<p>All Posts</p>
  		<div class="buttons">
  			<button class="button"><%= link_to "New Post", new_post_path %></button>
  			<button class="button">Log Out</button>
  		</div>
  	</div>
  </div>
  ...
  </body>
```
You can see `All Post` shows up.

Note: My button tag doesn't work in Rails5, so I change it to the following code:
```html

  <body>
	...
  <div id="main_content">
  	<div id="header">
  		<p>All Posts</p>
  		<div class="buttons">
  			<%= link_to "New Post", new_post_path, class: "button" %>
  			<button class="button">Log Out</button>
  		</div>
  	</div>
  </div>
  ...
  </body>
```

### Page Wrapper

We go in the show page and you can see it's not pushed over like it should be on. And then the new post obviously the same thing is happening.
In `app/views/posts/new.html.erb`, I'm just going to wrap this entire thing inside of a page_wrapper `div` which is all styled and we'll fix the formatting for us.
```html

	<div id="page_wrapper">
		<h1>New post!</h1>
		...
	</div>

```

And the same as `app/views/posts/show.html.erb`
```html

	<div id="page_wrapper">
		<h1 class="title">
			<%= @post.title %>
		</h1>

		<p class="data">
			Submitted <%= time_ago_in_words(@post.created_at) %> Ago
		</p>

		<p class="body">
			<%= @post.body %>
		</p>
	</div>

```
Eveything is looking pretty good right now. So I'm going to go ahead and commit and merge what we just did.
```console
$ git add .
$ git commit -am "Styling and structure"
$ git checkout master
$ git merge styling
```
![image](https://github.com/TimingJL/blog/blob/master/pic/Styling%20and%20structure.jpeg)

To be continute...