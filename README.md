
rails new hardway

rails s

check your browser on localhost:3000

Now we are going to create a home page without using a generator.

config/routes.rb

root 'home#index'

touch app/controllers/home_controller.rb

class HomeController < ApplicationController
  def index

  end
end

Now you are missing a view.

create a new folder in the views by right clicking and it should read app/views/home

Then in the home folder right click and create a new file called index.html.erb

Navigate to app/views/home/index.html.erb that we jsut created and write the following

<h1>Hello World</h1>

now we want to create an article to do this we test the browser to see what the error is when we go to the following url

localhost:3000/articles/new

Now read the error that says there is no route matching this path

Now we create the the path in the config/routes.rb by adding the following

resources :articles

Now the next error was that we don't have a controller

In terminal
touch app/controllers/articles_controller.rb

navigate to the app/controllers/articles_controller.rb

class ArticlesController < ApplicationController
  def new

  end

end


See error... You now need to create the corresponding view to the controller new action

Right click views and create articles folder

Inside the articles folder right click and create a new.html.erb

Now the next step to create an article would be to give the application data through a form in the new.html.erb view we just created.

So navigate to app/views/articles/new.html.erb

<h1>New Articles</h1>

<%= form_for :article do |f| %>

  <%= f.label :title %><br>
  <%= f.text_field :title %><br>

  <%= f.label :content %><br>
  <%= f.text_area :content %><br>

  <%= f.submit "Save article" %>

<% end %>

Open inspect element and review what the form is doing noting that we need to run the create

The path is going to the articles/new however when we check the rails routes we need to have the article CREATE once we save it

<h1>New Articles</h1>

<%= form_for :article, url: '/articles' do |f| %>

  <% f.label :title %><br>
  <%= f.text_field :title %><br>

  <%= f.label :content %><br>
  <%= f.text_area :content %><br>

  <%= f.submit "Save article" %>

<% end %>

rails routes

now navigate to the articles_controller.rb

Now we

def create
  @article = Article.new(params[:article])
  @article.save
end

Now the next error we need to save the data to an article model in your terminal run the below command

touch app/models/article.rb

navigate to app/models/article.rb

class Article < ApplicationRecord

end

Now we need somewhere to save the database in a table in the model

In terminal run the below

mkdir db/migrate
touch db/migrate/20180419184801_create_articles.rb

navigate to db/migrate/

class CreateArticles < ActiveRecord::Migration[5.1]
  def change
    create_table :articles do |t|
      t.string :title
      t.text   :content

      t.timestamps null: false
  end
end

Every time we create a database migration we are making a change to the database

Run a rails db:migrate in your terminal

Now here we need to redirect to the show page once we have created the article so that the user can review it. To do this we will nee to do 2 steps. We will need to redirect to the article and when we get to the view the system will need to find the database.

in app/controllers/articles_controllers.rb

class ArticlesController < ApplicationController
  def new

  end


  def create
    @article = Article.new(article_params)
    @article.save
    redirect_to @article
  end

  def show
    @article = Article.find(params[:id])
  end

  private

  def article_params
    params.require(:article).permit(:title, :content)
  end

end

Now that we have created the method in the controller we need to create the show.html.erb in the articles folder

Right click the folder and create a new file called show.html.erb

navigate to app/views/articles/show.html.erb

<h1><%= @article.title %></h1>
<hr>
<p><%= @article.content %></p>

Now we are going to create some navigational links at the top of the page. Seeing as we want it to be on every page we will need to do it in the file below:

navigate to app/views/layouts/application.html.erb

<%= link_to 'Home', root_path %>
<%= link_to 'Articles', articles_path %>
<%= link_to 'New Article', new_article_path%>

Now we need to create the index page
