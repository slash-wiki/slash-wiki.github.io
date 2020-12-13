The code for this document is available [here](https://github.com/slash-wiki/slash-wiki-lambda-go).

## Go (Lambda)

### Introduction

Please follow [this guide](https://golang.org/doc/install) to get Go installed on your machine.

Next, let's create a Go project:

1. Run `$ mkdir api/`.
2. Run `$ cd api/`.
1. Run `$ go mod init github.com/USERNAME/slash-wiki-lambda-go`.
3. Run `$ touch index.go`.

### Dependencies

This project will use [Schema](https://github.com/gorilla/schema) for decoding form data. Add the following after `go 1.15` in your `go.mod` file:

```
require github.com/gorilla/schema v1.2.0
```

### Code

1. Defines the package as handler.
2. Imports the project dependencies.
3. Creates a new Schema decoder.
4. Defines empty Message struct.
5. Parses form and retrieve error if it exists.
6. Defines empty Parameters struct.
7. Decodes form data into Parameters struct using Schema.
8. Fills Message struct with data depending on if error exists or not.
9. Writes header to set Content-Type to application/json.
10. Encodes Message type to JSON and writes it to the response.
11. Creates type called Parameters and contains all payload fields [from the “Preparing your app to receive Commands” section in Slack’s documentation](https://api.slack.com/interactivity/slash-commands#app_command_handling).
12. Creates type called Message and contains the two fields needed for sending a message back to Slack.

```go
package handler

import (
	"github.com/gorilla/schema"
	"encoding/json"
	"net/http"
	"time"
	"fmt"
)

func Handler(writer http.ResponseWriter, request *http.Request) {
	var decoder = schema.NewDecoder()
	var message Message
	err := request.ParseForm()
	
	var parameters Parameters
	_ = decoder.Decode(&parameters, request.PostForm)

	if nil != err {
		message = Message{
			ResponseType: "ephemeral",
			Text:         "The form could not be parsed.",
		}
	} else {
		message = Message{
			ResponseType: "in_channel",
			Text:         fmt.Sprintf("Hello %s, the date and time is %s.", parameters.UserId, time.Now().Format(time.RFC850)),
		}
	}

	writer.Header().Add("Content-Type", "application/json")
	json.NewEncoder(writer).Encode(message)
}

type Parameters struct {
	Token       string `schema:"token"`
	TeamId      string `schema:"team_id"`
	TeamDomain  string `schema:"team_domain"`
	ChannelId   string `schema:"channel_id"`
	ChannelName string `schema:"channel_name"`
	UserId      string `schema:"user_id"`
	UserName    string `schema:"user_name"`
	Command     string `schema:"command"`
	Text        string `schema:"text"`
	ResponseUrl string `schema:"response_url"`
	TriggerId   string `schema:"trigger_id"`
	ApiAppId    string `schema:"api_app_id"`
}

type Message struct {
	ResponseType string `json:"response_type"`
	Text         string `json:"text"`
}
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
