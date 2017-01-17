# Rails for Zombies 2

### Chapter 1 - FROM THE GROUND UP

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
