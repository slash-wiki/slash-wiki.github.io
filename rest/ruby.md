The code for this document is available [here](https://github.com/slash-wiki/slash-wiki-rest-ruby).

## Ruby (Rest)

### Introduction

Ruby comes installed on most machines. Please run `$ which ruby` and `$ ruby --version` to confirm 2.6.3 or newer is installed. If no Ruby version is installed, please follow [this guide](https://www.ruby-lang.org/en/documentation/installation/) to install Ruby on your machine.

Next let's create the Ruby project:

1. Run `$ touch Gemfile`.
2. Run `$ touch app.rb`.
3. Run `$ touch config.ru`.

### Dependencies

This project will use [Sinatra](https://github.com/sinatra/sinatra) for routing Add the following in your `Gemfile` file:

```
gem "sinatra"
```

### Code

This project has two files: `app.rb`, and `config.ru`. The first is where we implement the app and the second is where the app is initialized.

#### Contents of `app.rb`:

1. Imports `sinatra/base`.
2. Creates a class called Slash that inherits `Sinatra::Base`.
3. Creates `/` post route.
4. Sets response status to `200`.
5. Sets response content type to `application/json`.
6. Sets response body to “Hello Uxxxxxxxxxx, the date and time is 2020-12-12 23:59:08 +0000.” filling in the user_id and current date.

```ruby
require 'sinatra/base'

class Slash < Sinatra::Base
  post "/" do
    status 200
    content_type 'application/json'
    body JSON[response_type: 'in_channel', text: "Hello #{params[:user_id]}, the date and time is #{Time.new}."]
  end
end

run Slash.run!
```

#### Contents of `config.ru`:

1. Imports the app.rb file.
2. Runs the Slash application.

```ruby
require File.expand_path('app', File.dirname(__FILE__))

run Slash
```

### Deployment

Rest-based Slack slash commands can be deployed to a number of different services. This guide will use Heroku:

To deploy this app on Heroku:

1. Create a Heroku account.
2. Create a Heroku app.
3. Choose Ruby as your Buildpack in Settings.
4. Deploy the app in the Deploy tab.

And voilà.

## Do you need help?

Please feel free to open an issue on GitHub [here](https://github.com/slash-wiki/slash-wiki.github.io/issues) if you have any questions.
