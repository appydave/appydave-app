# Rails 8 Setup Guide for AppyDaveApp

This guide will walk you through setting up your Rails 8 application called **AppyDaveApp**, hosted at **app.appydave.com**. We'll cover every step, from installation to creating an executable script, with relevant code snippets.

The demonstratable capabilites at the end of this guide will include:

- Tailwind CSS for styling
- PostgreSQL as the database
- RSpec as the default testing framework
- Guard for automatic testing
- RuboCop for code linting
- Hotwire/Stimulus for frontend interactivity

## Set Up GitHub Repository

Go to GitHub and create a new repository under the `appydave` organization named **appydave-app**. 

When creating the repository, choose the **Mozilla Public License 2.0 (MPL-2.0)** as the license. 

We chose MPL-2.0 because it balances openness and protection, allowing public access to the code while safeguarding intellectual property, with a requirement to share modifications. 

**GitHub Repository Description**: Automation Tools for the @appydave YouTube channel

Once the repository is created, you can proceed with the following steps to set it up locally.

## 1. Install Ruby and Rails 8

Ensure you have **Ruby 3.2 or higher** installed. To check your Ruby version:

```bash
ruby -v
```

Next, install Rails 8 (beta version):

```bash
gem install rails --pre
```

## 2. Intialize Rails 8 Application

Create the Rails application with following capabilites:

- Tailwind CSS for styling
- PostgreSQL as the database
- No testing framework, as we will set up RSpec later

```bash
rails new appydave-app -T --css tailwind --database=postgresql
```

The `-T` flag prevents Rails from generating files for MiniTest, as we will use RSpec for testing.
- The `--css tailwind` flag sets up Tailwind CSS for styling.
- The `--database=postgresql` flag configures PostgreSQL as the database.

Navigate into the project directory:

```bash
cd appydave-app
```

## Ensure the Rails Server is Running

Create new database and start the Rails server:

```bash
rails db:create
rails server
```

## Initial Commit

Combine the remote repository with the local repository.

`git init` has already been run during the Rails setup, so you can add your code:

Connect and merge the Remote Repository:

```bash
git remote add origin git@github.com:appydave/appydave-app.git
git branch -M main
git fetch origin
git merge --allow-unrelated-histories origin/main
```

If there are any conflicts, resolve them, then commit the changes:

```bash
git add .
git commit -m "Initial commit for AppyDaveApp"
git push
```

Setting up an alias to simplify git add/commit

```bash
alias rs='rails server'
alias gac='git add . && git commit -m '
alias r8_next="rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'"
alias run="bin/rails assets:precompile && rs"
```

## Setup Local `rails app:template` Script

```bash
mkdir -p public/template
touch public/template/rails8.0.rb # Use a paste from URL technique when I am finished
touch setup.md
gac 'setup documentation and template created' && git push && ghb
```

## Other Docs
- https://medium.com/@ronakabhattrz/whats-new-in-ruby-on-rails-8-3f44d2d361a7
- https://fly.io/ruby-dispatch/the-plan-for-rails-8/
- https://medium.com/@rail_to_rescue/ruby-on-rails-8-a-comprehensive-guide-a316e842872d

## What we will cover in the template

- RSpec as the default testing framework
- RuboCop for code linting
- Guard for automatic testing
- FactoryBot for test data
- Tailwind CSS for styling
- Authentication using Rails 8 generator
- Hotwire/Stimulus for frontend interactivity
- Solid Cable
  - REWRITE: Solid Cable is Rails’ new default Action Cable adapter in production, allowing you to drop the common dependency on Redis. It acts as the pub/sub server, relaying messages between the app and connected clients using fast polling through SQLite. Despite polling, Solid Cable’s performance is comparable to Redis in most situations.
- Solid Cache
  - REWRITE: Solid Cache replaces the need for Redis by using disk storage instead of RAM for caching. This approach allows for much larger, cost-effective caches that persist longer and handle more requests without sacrificing performance. Solid Cache also supports encrypted storage and retention policies to meet privacy requirements.
- Solid Queue:
  - REWRITE: Solid Queue replaces Redis for Active Job background processing, utilizing the FOR UPDATE SKIP LOCKED mechanism for efficient job handling (compatible with PostgreSQL, MySQL, or SQLite). It includes essential features like concurrency control, retries, and recurring jobs. Solid Queue has proven its reliability at HEY, managing 20 million jobs per day.
- These three adapters are designed around a simple idea: modern SSDs and NVMe drives are fast enough to handle many tasks that previously required in-memory solutions. By leveraging these speedy drives, Rails eliminates the need for separate RAM-based tools like Redis.

- Where I deviate: I will use PostgreSQL instead of SQLite for the database
Look into:
- Support for PostgreSQL table inheritance and native partitioning has been added.
- Bulk inserts of fixtures are now supported to improve data seeding performance.
- Kamal 2 + Thruster
- Sprockets with Propshaft
- Scrept Generator
- Rails 8 Language Server

https://rubyonrails.org/2024/8/10/Rails-7-2-0-has-been-released
- DevContainer
- PWA
- GitHub CI
- Brakeman

Example Use Cases
- Real-time chat: Use Action Cable to build a real-time chat application where messages are instantly displayed to all users.
- Collaborative editing: Implement a collaborative editing tool using Action Cable to allow multiple users to edit a document simultaneously.
- Notifications: Send real-time notifications to users based on specific events or actions.


### Setup common gems

Gems that are commonly used in Rails applications:

```bash
run
r8_next
gac '1-setup common gems' && git push && ghb
```

> Notice that `RuboCop` fixed style issues after code generation.

### Add Homepage

Simple home page with a welcome message.

```bash
run
r8_next
gac '2-simple home page' && git push && ghb
```

> Notice that `RSpec` is now the default testing framework and has failed on the home page test.

Manual modification on: spec/requests/home_spec.rb

# Run tests then change the route

```ruby
  get "/home/index"
  # to
  get "/"
```

### Apply Basic Layout

Add a basic layout with a menu on the left.

```bash
run
r8_next
gac '3-update layout and a menu' && git push && ghb
```

> Notice that `bin/rails assets:precompile && rails server` has been run to ensure that CSS is working.

### Add Flash Message Partial

Add simple flash message partial with dynamic style classes using Tailwind CSS.

```bash
r8_next

gac '4-add flash message partial' && git push && ghb
```


### Application Setings

Since we are using RSpec, we will alter the generator configuration to use RSpec instead of MiniTest.

```bash
run
r8_next
gac '5-application settings' && git push && ghb
```

> Notice in the next step that unit test generation may change?

### Add Pages

- About is available anyone
- Status is available to anyone, will show current signed in user if available
- Account requires user to be logged in

```bash
run
r8_next
gac '6-add top level pages for about, authentication & account' && git push && ghb
```

> Notice that all pages are accessible, we start adding different levels of restrictions in the future steps.

### Lets refactor password mailer test using CursorAI

```pair-programmer
A test for a Rails Mailer was generated based on MiniTest.
But I use RSpec, can you keep the functionality but refactor for RSpec?

# Refactor:     test/mailers/previews/passwords_mailer_preview.rb
# to:           spec/mailers/previews/passwords_mailer_preview_spec.rb

Reference the following when thinking through your refactor:

test/mailers/previews/passwords_mailer_preview.rb
Gemfile
rails_helper.rb
spec_helper.rb
```

```bash
gac '7-refactor mini-test to rspec-test using AI pair programmer (CursorAI)' && git push && ghb
```

### Check CI

Take a look and fix Linting Issues if any.

### Authentication

Install the authentication generator:

```bash
run
r8_next
gac '8-rails generate authentication' && git push && ghb
```

> Notice that request specs will start to fail because of the authentication restrictions.

### Relax Authentication Restrictions

Uncomment the following line PagesController to allow unauthenticated access to the about and authentification pages.

```ruby
allow_unauthenticated_access only: [ :about, :authentification ]
```

# before_action :resume_session, only: [:authentification]

Uncomment the following line in the PagesController when you want provide access to session data on the authentication page.

```ruby
before_action :resume_session, only: [:authentification]
```

```bash
run
r8_next
gac '9-authentication relax restrictions' && git push && ghb
```

### Authentication Enhancement

```bash
run
r8_next
gac '10-authentication enhancement' && git push && ghb
```

> Notice that the register page is now available to anyone.

### Authentication Info

```bash
run
r8_next
gac '11-authentication info' && git push && ghb
```

> If you cannot see the signed in user, it is becaused the session has not been resumed.

```ruby
  # Uncomment the following line in the PagesController when you want provide access to session data on the authentication page.
  before_action :resume_session, only: [ :authentification ]
```

### Authentication Email

```bash
run
r8_next
gac '12-authentication email' && git push && ghb
```



## Setup Letter Opener so you can preview registration emails in development

Add the following gems to your `Gemfile`:

Letter Opener will be used to preview registration emails in development

```ruby
group :development do
  gem 'letter_opener'
  gem 'letter_opener_web'
end
```

## Setup RuboCop for linting

Check and fix code style issues with RuboCop. Add the following to your `Gemfile`:

```ruby
group :development do
  gem 'rubocop', require: false
end
```

Configure **Guard** to automatically run RSpec tests and RuboCop when files are modified. Run the following commands to initialize Guard and add configurations to the Guardfile:

```bash
guard init rspec
```

Next, add RuboCop to the Guardfile to run after RSpec tests. Open the generated **Guardfile** and modify it as follows:

```ruby
# Guardfile

# Existing RSpec configuration
guard :rspec, cmd: "bundle exec rspec" do
  watch(%r{^spec/.+_spec\.rb$})
  watch(%r{^lib/(.+)\.rb$}) { |m| "spec/lib/\#{m[1]}_spec.rb" }
end

# Add RuboCop configuration
guard :rubocop, cmd: "bundle exec rubocop" do
  watch(%r{.+\.rb$})
end
```

If you encounter an error that states 'No Guardfile found', it means the Guardfile hasn't been created yet. To resolve this, make sure to run `guard init rspec` to generate the necessary configuration file.

Add **RSpec**, **FactoryBot**, **Guard**, and **RuboCop** to your `Gemfile` under the appropriate groups:

```ruby
# Gemfile
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end

group :development do
  gem 'guard'
  gem 'guard-rspec'
  gem 'rubocop', require: false
end
```

Run `bundle install` to install the gems:

```bash
bundle install
```

RuboCop is a code linter that helps enforce consistent code style in your project. It will be run automatically by Guard after RSpec tests.

Next, run the RSpec generator to set up testing:

```bash
rails generate rspec:install
```

This creates the `spec` directory with the necessary configuration files.

## 6. Create a Sample API Endpoint

Create a standard Rails controller for your API. Generate a controller named **api/v1/base**:

```bash
rails generate controller api/v1/base
```

Update the generated controller to provide a simple API response. Edit **app/controllers/api/v1/base_controller.rb** like so:

```ruby
# app/controllers/api/v1/base_controller.rb
module Api
  module V1
    class BaseController < ApplicationController
      def hello
        render json: { message: 'Hello, AppyDaveApp! Visit our YouTube channel for more updates: https://www.youtube.com/@AppyDave/videos' }
      end

      def services
        services = Service.all
        render json: services
      end
    end
  end
end
```

## 7. Mount the API in Rails

Mount the API in **routes.rb** to make it accessible:

```ruby
# config/routes.rb
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do
      get 'hello', to: 'base#hello'
      get 'services', to: 'base#services'
    end
  end
end
```

After making changes, be sure to restart your Rails server to ensure everything is properly reloaded:

```bash
rails server
```

You can access the API endpoint at `http://localhost:3000/api/v1/hello`.

## 9. Create a Model and Add FactoryBot

Generate a **Service** model to store some data about services:

```bash
rails generate model Service name:string description:text
```

Run the migration:

```bash
rails db:migrate
```

Create a factory for the **Service** model to use in tests. Add the following to **spec/factories/services.rb**:

```ruby
# spec/factories/services.rb
FactoryBot.define do
  factory :service do
    name { "Test Service" }
    description { "This is a test service." }
  end
end
```

## 10. Create Some Seed Data

Add initial data in **db/seeds.rb**:

```ruby
# db/seeds.rb
Service.create(name: 'API Service', description: 'Provides API functionality')
Service.create(name: 'Billing Service', description: 'Handles billing')
```

Run the seeds to populate your database:

```bash
rails db:seed
```



## 12. Run the API, Set Up Tests, and Push Final Changes

Run the Rails server:

Push any remaining changes to the repository:

```bash
git add .
git commit -m "Final updates before running the server"
git push
```

Run the RSpec tests to make sure everything is working as expected:

```bash
bundle exec rspec
```

You can also run Guard to automatically run tests and check code quality when files are changed:

```bash
guard
```

```bash
rails server
```

Test your API endpoints:
- **GET** `http://localhost:3000/api/v1/hello`
- **GET** `http://localhost:3000/api/v1/services`

## 14. Update README with License Information

To help others understand why the **Mozilla Public License 2.0 (MPL-2.0)** was chosen, add a section to the README file explaining the reasoning:

Create or update **README.md** with the following content:

```markdown
# AppyDaveApp

AppyDaveApp is a Rails 8 application aimed at providing services with an open yet protective license.

## License

This project is licensed under the **Mozilla Public License 2.0 (MPL-2.0)**. The MPL-2.0 license was chosen to balance openness and protection for AppyDaveApp. It allows public access to the code while safeguarding intellectual property, and requires modifications to be shared, thus encouraging both community collaboration and responsible usage.
```

Push the updated README to the repository:

```bash
git add README.md
git commit -m "Add license explanation to README"
git push
```

## Summary of Commands

### Install Rails 8:

```bash
gem install rails --pre
```

### Create a New Rails App:

```bash
rails new appydave-app
cd appydave-app
```

### Generate a Controller:

```bash
rails generate controller api/v1/base
```

### Generate a Model:

```bash
rails generate model Service name:string description:text
rails db:migrate
```
### Generate a Model:

```bash
rails generate model Service name:string description:text
rails db:migrate
```

Now you have a working API that can be expanded as your project grows!
