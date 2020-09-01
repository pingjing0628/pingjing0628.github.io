---
title: GoLang-REST API with Mux
date: 2019-12-17 21:17:48
tags: 
  - GoLang
  - Mux
  - Rest API
categories: 
  - GoLang
---

## REST API with Mux

<!--more-->
---

```golang=
package main

import(
    "encoding/json"
    "log"
    "net/http"
    "math/rand"
    "strconv"
    "github.com/gorilla/mux"
)

// Book Struct (Model)
// Struct is kind of like a class, it's used for OOP in Golang
type Book struct {
    ID      string  `json:"id"`
    Isbn    string  `json:"isbn"`
    Title   string  `json:"title"`
    Author  *Author `json:"author"`
}

// Author Struct
type Author struct {
    Firstname  string  `json:"firstname"`
    Lastname   string  `json:"lastname"`
}

// Init books var as a slice Book struct
var books []Book

// Get All Books
func getBooks(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(books)
}

// Get Single Book
func getBook(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    params := mux.Vars(r) // Get params
    // Loop through books and find with id
    for _, item := range books {
        if item.ID == params["id"] {
            json.NewEncoder(w).Encode(item)
            return
        }
    }
    json.NewEncoder(w).Encode(&Book{})
}

// Create a New Book
func createBook(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    var book Book
    _ = json.NewDecoder(r.Body).Decode(&book)
    book.ID = strconv.Itoa(rand.Intn(10000000)) // Mock ID - not safe
    books = append(books, book)
    json.NewEncoder(w).Encode(book)
}

// Update a Book
func updateBook(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    params := mux.Vars(r)
    for index, item := range books {
        if item.ID == params["id"] {
            books = append(books[:index], books[index+1:]...)
            var book Book
            _ = json.NewDecoder(r.Body).Decode(&book)
            book.ID = params["id"]
            books = append(books, book)
            json.NewEncoder(w).Encode(book)
            return
        }
    }
    json.NewEncoder(w).Encode(books)
}

// Delete a Book
func deleteBook(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    params := mux.Vars(r)
    for index, item := range books {
        if item.ID == params["id"] {
            books = append(books[:index], books[index+1:]...)
            break
        }
    }
    json.NewEncoder(w).Encode(books)
}

func main() {
    // Init Router
    r := mux.NewRouter()

    // Mock Data - @todo - implemet DB
    books = append(books, Book{ID: "1", Isbn: "443322", Title: "Section 1", Author: &Author{Firstname: "Yu", Lastname: "Wang"}})
    books = append(books, Book{ID: "2", Isbn: "554433", Title: "Section 2", Author: &Author{Firstname: "Ping", Lastname: "Wang"}})

    // Route Handlers / Endpoints
    r.HandleFunc("/api/books", getBooks).Methods("GET")
    r.HandleFunc("/api/books/{id}", getBook).Methods("GET")
    r.HandleFunc("/api/books", createBook).Methods("POST")
    r.HandleFunc("/api/books/{id}", updateBook).Methods("PUT")
    r.HandleFunc("/api/books/{id}", deleteBook).Methods("DELETE")
    log.Fatal(http.ListenAndServe(":8090", r))
}
```

### In main()
1. `r := mux.NewRouter()`
 **Create a new Router**
Create a new request router. 
The router is the main router for web application and will later be passed as parameter to the server. 
It will receive all HTTP connections and pass it on to the request handlers.

2. Registering a Request Handler
Once have a new router you can register request handlers like usual. The only difference is, that instead of calling `http.HandleFunc(...)`, call HandleFunc on your router like this: `r.HandleFunc(...)`.

3. URL Parameters
To have a request handler match the URL mentioned above you replace the dynamic segments of with placeholders in your URL pattern like so:
```golang=
r.HandleFunc("/books/{title}/page/{page}", func(w http.ResponseWriter, r *http.Request) {
    // get the book
    // navigate to the page
})
```
The last thing is to get the data from these segments. The package comes with the function mux.Vars(r) which takes the http.Request as parameter and returns a map of the segments.
```go=
func(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    vars["title"] // the book title slug
    vars["page"] // the page
}
```

4. manipulate memory space
create and update are manipulate memory space, so when you disconnect, then the data will go back to the default value

### Reference
* [Rest API With Mux](https://www.youtube.com/watch?v=SonwZ6MF5BE)
* [Go Web Examples](https://gowebexamples.com/routes-using-gorilla-mux/)