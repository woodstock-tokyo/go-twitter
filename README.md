# go-twitter

Twitter API(V2) Client

## Install

```bash
go get -u github.com/woodstock-tokyo/go-twitter
```

## Example

Check `examples` directory

## Tweet Lookup Example

```go
import (
  "encoding/json"
  "log"
  "flag"
  "fmt"
  "net/http"
  "context"
  "strings"

  twitter "github.com/woodstock-tokyo/go-twitter"
)

type authorize struct {
  Token string
}

func (a authorize) Add(req *http.Request) {
  req.Header.Add("Authorization", fmt.Sprintf("Bearer %s", a.Token))
}

/**
  In order to run, the user will need to provide the bearer token and the list of ids.
**/
func main() {
  token := flag.String("token", "", "twitter API token")
  ids := flag.String("ids", "", "twitter ids")
  flag.Parse()

  client := &twitter.Client{
    Authorizer: authorize{
      Token: *token,
    },
    Client: http.DefaultClient,
    Host:   "https://api.twitter.com",
  }
  opts := twitter.TweetLookupOpts{
    Expansions:  []twitter.Expansion{twitter.ExpansionEntitiesMentionsUserName, twitter.ExpansionAuthorID},
    TweetFields: []twitter.TweetField{twitter.TweetFieldCreatedAt, twitter.TweetFieldConversationID, twitter.TweetFieldAttachments},
  }

  fmt.Println("Callout to tweet lookup callout")

  tweetDictionary, err := client.TweetLookup(context.Background(), strings.Split(*ids, ","), opts)
  if err != nil {
    log.Panicf("tweet lookup error: %v", err)
  }

  enc, err := json.MarshalIndent(tweetDictionary, "", "    ")
  if err != nil {
    log.Panic(err)
  }
  fmt.Println(string(enc))
}

```

## Twitter Academic Research API

APIs which is only available via Academic Research product track are not implemented. Including [Twitter Full-archive search](`https://developer.twitter.com/en/docs/twitter-api/tweets/search/introduction`)
