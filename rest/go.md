The code for this document is available [here](https://github.com/slash-wiki/slash-wiki-rest-go).

## Go (Rest)

### Introduction

Please follow [this guide](https://golang.org/doc/install) to get Go installed on your machine.

Next, let's create a Go project:

1. Run `$ go mod init github.com/USERNAME/slash-wiki-rest-go`.
2. Run `$ mkdir -p cmd/slash-wiki-rest-go`.
3. Run `$ touch cmd/slash-wiki-rest-go/main.go`.

### Dependencies

This project will use [Mux](https://github.com/gorilla/mux) for routing and [Schema](https://github.com/gorilla/schema) for decoding form data. Add the following after `go 1.15` in your `go.mod` file:

```mod
require (
	github.com/gorilla/mux v1.8.0
	github.com/gorilla/schema v1.2.0
)
```

### Code

This project has two packages: `main`, and `structures`. This is not necessary but makes the code more organized.

#### Contents of `main.go`:

1. Defines the package as `main`.
2. Imports the `structures` package and project dependencies.
3. Creates a new Schema decoder.
4. Creates a new Mux router.
5. Handles the `/` endpoint with function.
6. Defines empty Message struct.
7. Parses form and retrieve error if it exists.
8. Defines empty Parameters struct.
9. Decodes form data into Parameters struct using Schema.
10. Fills Message struct with data depending on if error exists or not.
11. Writes header to set `Content-Type` to `application/json`.
12. Encodes Message type to JSON and writes it to the response.
13. Creates the server.

```go
package main

import (
	Structures "github.com/slash-wiki/slash-wiki-rest-go/cmd/slash-wiki-rest-go/structures" 
	"github.com/gorilla/schema"
	"github.com/gorilla/mux"
	"encoding/json"
	"time"
	"net/http"
	"fmt"
	"os"
)

var decoder = schema.NewDecoder()

func main() {
	router := mux.NewRouter()
	router.HandleFunc("/", func(writer http.ResponseWriter, request *http.Request) {
		var message Structures.Message
		err := request.ParseForm()
		
		var parameters Structures.Parameters
		_ = decoder.Decode(&parameters, request.PostForm)

		if nil != err {
			message = Structures.Message{
				ResponseType: "ephemeral",
				Text:         "The form could not be parsed.",
			}
		} else {
			message = Structures.Message{
				ResponseType: "in_channel",
				Text:         fmt.Sprintf("Hello %s, the date and time is %s.", parameters.UserId, time.Now().Format(time.RFC850)),
			}
		}

		writer.Header().Add("Content-Type", "application/json")
		json.NewEncoder(writer).Encode(message)
	})

	server := &http.Server{
		Handler:      router,
		Addr:         fmt.Sprintf(":%s", os.Getenv("PORT")),
		WriteTimeout: 15 * time.Second,
		ReadTimeout:  15 * time.Second,
	}

	server.ListenAndServe()
}
```

#### Contents of `structures/structures.go`:

1. Defines the package as structures.
2. Creates type called Parameters and contains all payload fields [from the "Preparing your app to receive Commands" section in Slack's documentation](https://api.slack.com/interactivity/slash-commands#app_command_handling).
3. Creates type called Message and contains the two fields needed for sending a message back to Slack.

```go
package structures

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

Rest-based Slack slash commands can be deployed to a number of different services. This guide will use Heroku:

To deploy this app on Heroku:

1. Create a Heroku account.
2. Create a Heroku app.
3. Choose Go as your Buildpack in Settings.
4. Deploy the app in the Deploy tab.

And voilà.

## Do you need help?

Please feel free to open an issue on GitHub [here](https://github.com/slash-wiki/slash-wiki.github.io/issues/new) if you have any questions.
