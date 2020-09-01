---
title: GoLang-Protocol Buffer
date: 2020-06-01 22:10:37
tags: 
  - GoLang
  - Protocol Buffer
categories: 
  - GoLang
---

## Protocol Buffer

<!--more-->

<b>Protocol Buffer</b> is like json、xml, but smaller, faster, simplifier.
Only have to define data structure once, and it will generate the file which is match to your programming language automatically, let you use directly.

### Structure is Document
#### Define data structure.
File should be `.proto` as suffix.

```
// Format is Proto v3
syntax = "proto3";

// User
message User {
    string username = 2;
    string password = 3;
}

// Post
message Post {
    int64 id       = 1;
    string content = 2;
}
```

#### What is the number after each field?
In fact, these are protocol buffer's id when using decode and encode.
These will make you remove one of the field without messing up entire data structure encoding or decoding.

### Pros and Cons with Json

* More lightweight
* Confusion: being encoded
* Performance high
* Much more convenient: structure is your data model, don't have to create object to mapping these data
* Clear, no need document: `.proto` is your document


### Installation

1. Install protocol buffer generator
* First, install `protoc`, which is the tool for transforming `.proto` to program.
* https://github.com/protocolbuffers/protobuf/releases install the protocol compiler
* After unzip, put the `bin/protoc` file to `$PATH`, this make us to use in terminal.

2. Install Golang plugin
* ` go get -u github.com/golang/protobuf/{proto,protoc-gen-go}`
* Do `touch .bash_profile`, and `export PATH=$PATH:/Users/pingjing/go/bin` to execute in terminal.

3. Transform proto document
* Create a `example.proto` file
```
// Format is Proto v3
syntax = "proto3";

// 生成的程式在 Golang 中將會是屬於 protobuf 套件
package protobuf;

// User
message User {
    int64 id        = 1;
    string username = 2;
    string password = 3;
}
```

* ` protoc --go_out=. *.proto` transform to golang
* And we will get a file `example.pb.go` which is belongs to `protobuf` plugin.
* Then we can use this structure in golang.

4. Decode and Encode
* Create a folder `/protobuf`, and put `example.pb.go` in it.
* Create `main.go`
```golang=
package main

import (
	"fmt"

	"./protobuf"
	"github.com/golang/protobuf/proto"
)

func main() {
	// Create a User format, and put data into it.
	data := protobuf.User{
		Id:       12345,
		Username: "Yu Wang",
		Password: "Hello",
	}

	// Make data encode to Protocol buffer format (Be aware of using Pointer)
	dataBuffer, _ := proto.Marshal(&data)

	// Make the data which is already encoded, decode to protobuf.User format
	var user protobuf.User
	proto.Unmarshal(dataBuffer, &user)

	// Output the result
	fmt.Println(user.Id, " ", user.Username, " ", user.Password)
}
```

* `go run main.go`
* Output `12345   Yu Wang   Hello`
