---
title: CRUD API with GO Lang
date: 2024-02-01 12:00:00 -5000
categories: [Go, Server, CRUD]
tags: [webserver, website, server, golang, api gateway, google]     # TAG names should always be lowercase
---

## Introduction:

This is used for the server side programming in web development. 

For every programming language when we start with we will learn how to print hello world.  

## First Program in Go:

```go
package main

import "fmt"

func main(){
	fmt.Print("hello world")
}
```

In this language we will be having 3 parts, the first one is declaring the packages we are using, the second one imports and at last the main function. As soon as you run this program it will start running the main function first.

## Comments in Go:

We have 2 types of Comments:

- Single line comments
    
    > Single-line comments start with two forward slashes (`//`).
    > 
- Multiple line comments
    
    > Multi-line comments start with `/*` and ends with `*/`.
    > 
    > 
    > Any text between `/*` and `*/` will be ignored by the compiler
    > 

## Variables:

There are different types of variable when you are declaring:

- int - Stores integers
- float - Store floating point numbers
- String - Stores text
- bool - Stores values of True or False

There are 3 ways in initializing variables, const, var, short declaration.

### Var Declaration:

> var *variablename type* = *value*
**Note:** You always have to specify either `type` or `value` (or both).
> 

### := Declaration:

> *variablename* := *value*
**Note:** It is not possible to declare a variable using `:=`, without assigning a value to it.
> 

### const Declaration:

> const constantname *type* = *value*
The `const` keyword declares the variable as "constant", which means that it is **unchangeable and read-only**
> 

## Output Functions:

There are 3 types of output functions:

- Print() - prints its arguments with their default format.
- Println() - prints with a whitespace is added between the arguments, and a newline is added at the end
- Printf() - formats its argument based on the given formatting verb and then prints them.

Here we will use two formattings:

- `%v` is used to print the **value** of the arguments
- `%T` is used to print the **type** of the arguments

```go
package main

import "fmt"

func main(){
	const tickets int = 20
	var remtickets uint = 20
	name := "Teja Ravipudi"

	fmt.Printf("Welcome %v to the event.\nWe have total of %v tickets and %v are still available", name, tickets, remtickets)
	
	// Getting the user input and storing them to variables
	
	const fname string
	const lname string
	const age int
	const email string
	const nticks uint

	fmt.Println("Enter your first name:")
	fmt.Scanln(&fname)

	fmt.Println("Enter your last name:")
	fmt.Scanln(&lname)

	fmt.Println("Enter your age:")
	fmt.Scanln(&age)

	fmt.Println("Enter your email:")
	fmt.Scanln(&email)

	fmt.Println("Enter no of Tickets:")
	fmt.Scanln(&nticks)

	remtickets = remtickets - nticks
	fmt.Printf("Thank you %v %v for booking. \nYou will recieve a mail of tickets to %v", fname, lname, email)
	fmt.Printf("Tickets remaining: %v", remtickets)
}
```

On Getting some basic knowledge on Syntax in Go. we will start to create a Simple sever.

## Building a Web Server:

The provided Go code is a simple web server that handles two different HTTP routes:

1. **“/hello” Route:**
    1. Responds with "Hello!" when accessed with a GET request.
    2. Returns a 404 Not Found error for any other HTTP method.
2. **"/form" Route:**
    1. Expects a POST request with form data containing "name" and "address" fields.
    2. Parses the form data using `r.ParseForm()`.
    3. If there's an error during form parsing, it writes an error message to the response.
    4. If form parsing is successful, it responds with a "Post Request Successful" message and prints the submitted "name" and "address" to the response.

Additionally, the server serves static files from the "./static" directory using the default file server provided by `http.FileServer(http.Dir("./static"))`. The static file serving is configured for the root ("/") path.

The server starts listening on port 8080, and any incoming requests are handled based on the defined routes.

In summary, this Go program creates a basic web server with two routes ("/hello" and "/form"). The "/hello" route responds with a simple greeting, while the "/form" route expects and processes a POST request containing form data. The server also serves static files from the "./static" directory for the root path ("/").

## Using the Crud Operations

Certainly! Let's go through the provided Go code step by step:

1. **Package and Imports:**
    
    ```go
    package main
    
    import (
       "encoding/json"
       "fmt"
       "log"
       "math/rand"
       "net/http"
       "strconv"
    
       "github.com/gorilla/mux"
    )
    ```
    
    The code starts with defining the `main` package and importing necessary packages/modules. Key packages here include `encoding/json` for JSON encoding and decoding, `fmt` for formatted I/O, `log` for logging, `math/rand` for generating random numbers, `net/http` for handling HTTP requests and responses, and `github.com/gorilla/mux` for routing HTTP requests.
    
2. **Data Structures:**
    
    ```go
    type Movie struct {
       ID       string    `json:"id"`
       Isbn     string    `json:"isbn"`
       Title    string    `json:"title"`
       Director *Director `json:"director"`
    }
    
    type Director struct {
       Firstname string `json:"firstname"`
       Lastname  string `json:"lastname"`
    }
    ```
    
    Two structs are defined: `Movie` and `Director`. `Movie` has fields like ID, Isbn, Title, and a pointer to `Director`. `Director` has fields for the director's first and last names. JSON tags (`json:"..."`) are used to specify the field names when encoding to or decoding from JSON.
    
3. **Global Variables:**
    
    ```go
    var movies []Movie
    ```
    
    A global variable `movies` is declared as a slice of `Movie`. This slice will be used to store the list of movies.
    
4. **HTTP Request Handlers:**
    - `getMovies`: Handles GET requests for fetching all movies.
    - `deleteMovie`: Handles DELETE requests for deleting a movie by ID.
    - `getMovie`: Handles GET requests for fetching a specific movie by ID.
    - `createMovie`: Handles POST requests for creating a new movie.
    - `updateMovie`: Handles PUT requests for updating an existing movie.
    
    Each handler is associated with a specific HTTP endpoint, and they perform operations on the `movies` data.
    
5. **Main Function:**
    
    ```go
    func main() {
       // Initialize the Gorilla Mux router
       r := mux.NewRouter()
    
       // Populate some initial movie data
       movies = append(movies, Movie{ID: "1", Isbn: "12345", Title: "Animal", Director: &Director{Firstname: "SandeepReddy", Lastname: "Vanga"}})
       movies = append(movies, Movie{ID: "2", Isbn: "12346", Title: "Animal2", Director: &Director{Firstname: "SandeepReddy1", Lastname: "Vanga"}})
    
       // Define routes and their corresponding handlers
       r.HandleFunc("/movies", getMovies).Methods("GET")
       r.HandleFunc("/movies/{id}", getMovie).Methods("GET")
       r.HandleFunc("/movies", createMovie).Methods("POST")
       r.HandleFunc("/movies/{id}", updateMovie).Methods("PUT")
       r.HandleFunc("/movies/{id}", deleteMovie).Methods("DELETE")
    
       // Start the HTTP server on port 8000
       fmt.Printf("Starting Sever at port 8000\\n")
       log.Fatal(http.ListenAndServe(":8000", r))
    }
    ```
    
    The `main` function sets up the Gorilla Mux router, initializes some movie data, defines the routes, and starts the HTTP server on port 8000.
    

In summary, this code implements a simple RESTful API for managing movies with basic CRUD operations using Go and the Gorilla Mux router.