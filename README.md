# Civogo - The Golang client library for Civo

[![GoDoc](https://godoc.org/github.com/civo/civogo?status.svg)](https://godoc.org/github.com/civo/civogo)
[![Build Status](https://github.com/civo/civogo/workflows/Test/badge.svg)](https://github.com/civo/civogo/actions)


Civogo is a Go client library for accessing the Civo cloud API.

You can view the client API docs at [http://godoc.org/github.com/civo/civogo](http://godoc.org/github.com/civo/civogo) and view the API documentation at [https://api.civo.com](https://api.civo.com)


## Install

```sh
go get github.com/civo/civogo
```

## Usage

```go
import "github.com/civo/civogo"
```

From there you create a Civo client specifying your API key and use public methods to interact with Civo's API.

### Authentication

Your API key is listed within the [Civo control panel's security page](https://www.civo.com/account/security). You can also reset the token there, for example, if accidentally put it in source code and found it had been leaked.

You can then use your API key to create a new client:

```go
package main

import (
	"context"
	"github.com/civo/civogo"
)

const (
    apiKey = "mykeygoeshere"
)

func main() {
  client, err := civogo.NewClient(apiKey)
  // ...
}
```

## Examples

To create a new Instance:

```go
config, err := client.NewInstanceConfig()
if err != nil {
  t.Errorf("Failed to create a new config: %s", err)
  return err
}

config.Hostname = "foo.example.com"

instance, err := client.CreateInstance(config)
if err != nil {
  t.Errorf("Failed to create instance: %s", err)
  return err
}
```

To get all Instances:

```go
instances, err := client.ListAllInstances()
if err != nil {
  t.Errorf("Failed to create instance: %s", err)
  return err
}

for _, i := range instances {
    fmt.Println(i.Hostname)
}
```

### Pagination

If a list of objects is paginated by the API, you must request pages individually. For example, to fetch all instances without using the `ListAllInstances` method:

```go
func MyListAllInstances(client *civogo.Client) ([]civogo.Instance, error) {
    list := []civogo.Instance{}

    pageOfItems, err := client.ListInstances(1, 50)
    if err != nil {
        return []civogo.Instance{}, err
    }

    if pageOfItems.Pages == 1 {
        return pageOfItems.Items, nil
    }

    for page := 2;  page<=pageOfItems.Pages; page++ {
        pageOfItems, err := client.ListInstances(1, 50)
        if err != nil {
            return []civogo.Instance{}, err
        }

        list = append(list, pageOfItems.Items)
    }

    return list, nil
}
```

## Contributing

If you want to get involved, we'd love to receive a pull request - or an offer to help over our KUBE100 Slack channel. Please see the [contribution guidelines](CONTRIBUTING.md).