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
alias gac='git add . && git commit -m '
alias r8_next="rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'"
```

## Setup Local `rails app:template` Script

```bash
mkdir -p public/template
touch public/template/rails8.0.rb # Use a paste from URL technique when I am finished
```

## What we will cover in the template

- Authentication
- Tailwind CSS for styling
- RSpec as the default testing framework
- Guard for automatic testing
- RuboCop for code linting
- Hotwire/Stimulus for frontend interactivity

### Add Homepage

Simple home page with a welcome message.

```bash
rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'
bin/rails assets:precompile

gac '1-simple home page'
```

### Apply Basic Layout

Add a basic layout with a menu on the left.

```bash
rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'

gac '2-update layout and a menu'
```

Add simple flash message partial with dynamic style classes using Tailwind CSS.

```bash
rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'
# 3 - Flash
gac '3-add flash message partial'
```

### Add Pages

- About is available anyone
- Status is available to anyone, will show current signed in user if available
- Account requires user to be logged in

```bash
rails app:template LOCATION='http://localhost:3000/template/rails8.0.rb'
gac '4-add top level pages for about, authentication & account'
```

### Authentication

Install the authentication generator:

```bash
rails generate authentication
rails db:migrate && rails db:test:prepare

rails server # restart, if that doesn't work, try assets:precompile
rails assets:precompile
gac 'add Rails 8 authentication'
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
