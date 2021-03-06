![rails for zombies banner](https://d1ffx7ull4987f.cloudfront.net/images/courses/hero/5/rails-for-zombies-2-44125c4204cb5f67e1feae3e9aa25e39.jpg)
<img align='right' width="120px" src='https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/25/completed-rails-for-zombies-2-632404d16a1f49d01f5c19105dae07be.png' />
# Rails for Zombies 2
Learn more Rails with this sequel to the infamous Rails for Zombies course. Increase your Ruby on Rails knowledge with even more zombie learning.

<img align='right' src='https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/26/level-1-on-rails-for-zombies-2-cfb6d7850ec88fa3917d650740003eb8.png' />
### Chapter 1 - FROM THE GROUND UP
- Installing Rails
- Creating a Rails App
- The Command Line
- Database Migrations
- Ruby 1.9 Hash Syntax
- Bundler
- Database Configuration

#### Notes
* To create a new rails app simply run `rails new MyApp`, where `MyApp`
is the name of your application.;
* After creating our app, we need to download and install the project
dependencies(called gems), change to your project directory and run `bundle install`;
* `rails server` will start a development server to our project, on port 3000;
* On rails we have the `rails generate scaffold zombie`, it will generate a lot
of files containing the model, migration, route, view and controller that we specify;
* We can specify the model on the CLI like this: `rails generate scaffold zombie name:string bio:text age:integer`;
* `migration` is a kind of 'versioning system' to our database, all the database changes
are made separately.
* After a migration is created we need to run them, so, use `rake db:migrate`. It will update our database with the changes of this migration.
* We can play with our models using `rails console`;
* Now, we can test our new model using the `rails console`.
* To create a new zombie we'll use the `create` method, it will receive an hash containing the zombie's information:
```ruby
Zombie.create(name: "John Doe", age: 27)
```
* It will return the hash of the zombie that we just create, everything in ruby will return a value;
* We can use `Zombie.first` to find the first zombie on our database;
* If we need to make some changes in our database, we'll need to create a new migration, so let's use the `rails generate migration AddEmailToZombies email:string`. 
'AddEmailToZombies' is the description of what we'll change in our database and after we specify what we'll be changed in this migration. Template: `Add<Anything>To<Table name>` or `Remove<Anything>From<Table name>`.
* If we want to undo a migration, we just need run `rake db:rollback`, it will back to the previous migration.
* The migration has the `up` and `down` methods, the `up` is used to make our changes and the `down` method is used to rails know how to rollback this migration.
```ruby
class RemoveAgeFromZombie < ActiveRecord::Migration
  def up
    remove_column :zombies, :age
  end

  def down
    add_column: :zombies, :age, :integer
  end
```
* The `add_column` needs: table name, field name, data type. In this order.
```ruby
  add_column :zombies, :age, :integer
```
* The `remove_column` need: table name, column name.
```ruby
  remove_column :zombies, :age
```
* The `rename_column` needs: table name, old field name, new field name
```ruby
  rename_column :zombies, :bio. :description
```
* The `drop_table` need only the table name
```ruby
   drop_table :zombies
```
* The `change_column` needs: table name, column name, (things that you will change)
```ruby
  change_column :zombies, :age, :integer, limit: 4
```
* `Gemfile` contains all project dependencies
* Migration to create a new table:
```ruby
class CreateTweets < ActiveRecord::Migration
  def change
    create_table :tweets do |t|
      t.string :status
      t.integer :zombie_id
    end
  end
end
```
* `rake db:setup` will create the database, load the schema and run the seed file.
* The `seed` file is a file containing some data to be loaded on our database.

<img src="https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/27/level-2-on-rails-for-zombies-2-296b00028b4e97a1beb4917bff1bc1dd.png"  align="right" />
### Chapter 2 - MODELS STILL TASTE LIKE CHICKEN
- Named Scope
- Callbacks
- has_one
- Relationship Options
- Using Includes
- has_many :through

#### Notes
* We can create named scopes to simplify a query. Like this:
```ruby
class RottingZombiesController < ApplicationController
  def index
    # @rotting_zombies = Zombie.where(rotting: true) # without named scope
    @rotting_zombies = Zombie.rotting # using named scope
    ...
  end
end

class Zombie < ActiveRecord::Base
  scope :rotting, where(rotting: true)
end
```
* Rails have `callbacks` too, like javascript:
```ruby
# Objective: When zombies age > 20, set rotting to true

# Wrong way:
# controllers/zombies_controller.rb
def update
  @zombie = Zombie.find(params[:id])

  if @zombie.age > 20
    @zombie.rotting = true
  end

  #...
end

# Right way:
# models/zombie.rb
class Zombie < ActiveRecord::Base
  before_save :make_rotting

  def make_rotting
    if age > 20
      self.rotting = true
    end
  end
```
* We should never return `false` in a callback, if this ocurred, the operation will be halt. For example, a `before_save` callback will stop the save operation, in other words, the model will not be saved.
* All callbacks: `before_validation`, `after_validation`, `before_save`, `after_save`, `before_create`, `after_create`, `before_update`, `after_update`, `before_destroy` and `after_destroy`.
* `rails g model brain zombie_id:integer status:string` to generate a model;
* To create relationships we need to add a key on our model:
```ruby
# migration
class CreateBrains < ActiveRecord::Migration
  def change
    create_table :brains do |t|
      t.integer :zombie_id
      #...
    end
    add_index :brains, :zombie_id # add a foreign_key index
  end
end

# app/models/brain.rb
class Brain < ActiveRecord::Base
  belongs_to :zombie
end

# app/models/zombie.rb
class Zombie < ActiveRecord::Base
  has_one :brain
end
```
* We can include the `dependent` option to destroy the child when the father is destroyed:
```ruby
# app/models/zombie.rb
class Zombie < ActiveRecord::Base
  has_one :brain, dependent: :destroy
end
```
* More relationship options: `dependent`, `foreign_key`, `primary_key`, `validate`.
* We should use the `include` option to prevent that server runs 1 query to find each child of an element in the collections:
```ruby
  def index
    # @zombies = Zombie.all # wrong way
    @zombies = Zombie.includes(:brain).all
  end
```
* To create a `many to many` relationship we must create a new table to assign others tables:
```ruby
# migration
class CreateAssignments < ActiveRecord::Migration
  def change
    create_table :assignments do |t|
      t.integer :zombie_id
      t.integer :role_id
    end

    add_index :assignments, :zombie_id
    add_index :assignments, :role_id
  end
end

# app/models/assignment.rb
class Assignment < ActiveRecord::Base
  belongs_to :zombie # singular
  belongs_to :role # singular
end

# app/models/zombie.rb
class Zombie < ActiveRecord::Base
  has_many :assignments # plural
  has_many :roles, through: :assignments
end

# app/models/role.rb
class Role < ActiveRecord::Base
  has_many :assignments # plural
  has_many :zombies, through: :assignments
end
```

<img align='right' src='https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/28/level-3-on-rails-for-zombies-2-0eaaf0109f83459c5aedef30bdf8bd96.png' />
### Chapter 3 - REST IN PIECES
- Understanding REST
- Revisit URL Helpers
- Forms & Input Helpers
- Nested Resources
- View Partials
- Other View Helpers

#### Notes

<img align='right' src='https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/29/level-4-on-rails-for-zombies-2-749b621e3b1d4545acdf0af0b48eb095.png' />
### Chapter 4 - ASSET PACKAGING AND MAILING
- Creating and Sending Mail
- Sending Attachments in Mail
- The Asset Pipeline
- Asset Tags
- CoffeeScript
- SCSS
- Javascript Manifest

#### Notes
* To generate a mailer: `rails g mailer ZombieMailer decomp_change lost_brain`. It will generate an mailer `app/mailers/zombie_mailer.rb` and 2 views, `app/views/zombie_mailer/decomp_change.text.erb`, `app/views/zombie_mailer/lost_brain.text.erb`, on for each mailer action.
* Mailer example:
```ruby
# app/mailers/zombie_mailer.rb
class ZombieMailer < ActionMailer::Base
  default from: "from@example.com"

  def decomp_change(zombie)
    @zombie = zombie
    @last_tweet = @zombie.tweets.last

    # 'z.pdf' is the filename
    attachments['z.pdf'] = File.read("#{Rails.root}/public/zombie.pdf")
    mail to: @zombie.email, subject: 'Your decomp stage has changed'
  end
  # ...
end
```

```erb
<!-- app/views/zombie_mailer/decomp_change.text.erb -->

Greetings <%= @zombie.name %>,

    Your decomposition state is now <%= @zombie.decomp %> and your last tweet was: <%= @last_tweet.body %>

Good Luck!
```
* If we want to send HTML email, just create another file:
```erb
<!-- app/views/zombie_mailer/decomp_change.html.erb -->

<h1>Greetings <%= zombie.name %>, </h1>

<p> Your decomposition state is now <%= @zombie.decomp %> and your last tweet was: <%= @last_tweet.body %>

<%= link_to "View yourself", zombie_url(@zombie) %>
```
* To send the email, we must go to `app/models/zombie.rb` and add an action to send the email:
```ruby
# app/models/zombie.rb
class Zombie < ActiveRecord::Base
  after_save :decomp_change_notification, if: :decomp_changed?

  private

  def decomp_change_notification
    ZombieMailer.decomp_change(self).deliver
  end
end
```
* Rails mailer isn't recommended for mass mailing, `Mad Mimi` is a good alternative.
* to include an asset:
```erb
<%= javascript_include_tag "custom" %>
<!-- Will generate: -->
<script src="/assets/custom.js" type="text/javascript"</script>

<%= stylesheet_link_tag "style" %>
<!-- Will generate: -->
<link src="/assets/style.css" media="screen" rel="stylesheet" type="text/css">

<%= image_tag "rails.png" %>
<!-- Will generate: -->
<img alt="Rails" src="/assets/rails.png" />
```

<img align='right' src='https://d1ffx7ull4987f.cloudfront.net/images/achievements/large_badge/453/level-5-on-rails-for-zombies-2-c2025563150aafdc5e5580d0101eb4aa.png' />
### Chapter 5 - RENDERING EXTREMITIES
In this chapter we gonna be taking look on:
- Controller Rendering Options
- Custom RESTful Routes
- Rendering Custom JSON
- Creating AJAX Links
- AJAXifiede Forms
- Sending Server Javascript
- AJAX using JSON Data

#### Notes
* How to respond to JSON:
```ruby
# app/controllers/zombies_controller.rb

class ZombiesController < ApplicationController
  def show
    @zombie = Zombie.find(params[:id])
    respond_to do |format|
      format.html
      format.json { render json: @zombie }
    end
  end
end
```
* If we just need JSON:
```ruby
# app/controllers/zombies_controller.rb
# ...

def show
  @zombie = Zombie.find(params[:id])
  render json: @zombie
end

# ...
```
* Status `200 - OK` is the default status on rails, to set another code:
```ruby
render json: @zombie.errors, status: :unprocessable_entity

render json: @zombie, status: :created, location: @zombie
```
* We have 2 types of custom routes, `:member` and `:collection`. `:member` acts on a single resource, while `:collection` acts on a collection of resources. Examples:
```ruby
resources :zombies do
  # url: /zombies/:id/decomp | helper: decomp_zombie_path(@zombie)
  get :decomp, on: :member

  # url: /zombies/:id/decay  | helper: decay_zombie_path(@zombie)
  get :decay, on: :member

  # url: /zombies/fresh      | helper: fresh_zombies_path
  get :fresh, on: :collection

  # url: /zombies/search     | helper: search_zombies_path
  post :search, on: :collection
end
```
```ruby
# app/controllers/zombies_controller.rb
# ...

def decomp
  @zombie = Zombie.find(params[:id])

  if @zombie.decomp === 'Dead (again)'
    render json: @zombie, status: unprocessable_entity
  else
    render json: @zombie, status: :ok
  end
end
```
* On JSON response we can set a custom response format for our model, it accept: `include`, `except` and `only`. Example:
```ruby
@zombie.to_json(only: :name) # { "name": "Eric" }
@zombie.to_json(only: [:name, :age]) # { "name": "Eric", "age": 25 }

@zombie.to_json(except: [:created_at, :updated_at, :id, :email, :bio]) # { "age": 25, "decomp": "Fresh", "name": "Eric", "rotting": false }

@zombie.to_json(include: :brain)
```
* We can set a default response:
```ruby
# /app/models/zombie.rb
# ...

def as_json(options = nil)
  super(options || { include: :brain, except: [:created_at, :updated_at, :id] })
end
```
* To make an ajax request we just need add `remote: true`, see below:
```ruby
<%= link_to 'delete', zombie, method: :delete, remote: true %>
```
* After this we need to allow the controller to accept the Javascript call:
```ruby
# app/controllers/zombies_controller.rb
# ...

def destroy
  # ...

  respond_to do |format|
    format.html { redirect_to zombies_url }
    format.json { head: ok }
    format.js # add this one
  end
end
```
* After this we need to write the Javascript to send back to the client
```erb
# app/views/zombies/destroy.js.erb

$('#<%= dom_id(@zombie) %>').fadeOut();
```
