# Learning by Doing 
Mackenzie Child's video really inspire me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# Project 2: How to build a blog in Rails     
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
Then Go back to our browser and refresh, you'll see the index page.

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

Then, we want to list out all of the post.

To be continute...