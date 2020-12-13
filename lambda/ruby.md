The code for this document is available [here](https://github.com/slash-wiki/slash-wiki-lambda-ruby).

## Ruby (Lambda)

### Introduction

Ruby comes installed on most machines. Please run `$ which ruby` and `$ ruby --version` to confirm 2.6.3 or newer is installed. If no Ruby version is installed, please follow [this guide](https://www.ruby-lang.org/en/documentation/installation/) to install Ruby on your machine.

### Dependencies

Ruby includes almost everything that is needed. The only dependency that is used is the core library `uri` which is already installed and just needs to be imported.

### Code

1. Imports `uri` dependency.
2. Sets response status to `200`.
3. Sets response `Content-Type` to `application/json`.
4. Sets response body to "Hello Uxxxxxxxxxx, the date and time is 2020-12-12 23:59:08 +0000." filling in the `user_id` and current date.

```ruby
require 'uri'

Handler = Proc.new do |request, response|
    response.status = 200
    response['Content-Type'] = 'application/json'
    response.body = JSON[response_type: 'in_channel', text: "Hello #{URI.decode_www_form(request.body).to_h['user_id']}, the date and time is #{Time.new}."]
end
```

### Deployment

Lambda-based Slack slash commands are more limited in where they can be deployed but still have options like AWS Lambda, Azure Functions, or Vercel. This guide will use Vercel:

To deploy this app on Vercel:

1. Create a Vercel account.
2. Create a Vercel app (leave everything as Other and default settings).
3. Let Vercel deploy the app.

And voilà.

## Do you need help?

Please feel free to open an issue on GitHub [here](https://github.com/slash-wiki/slash-wiki.github.io/issues/new) if you have any questions.
