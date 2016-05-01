# Go, Defer and Recover

I was watching a Go talk from Google I/O 2011 (Building Web Apps in Go) which introduces a simple web server that allows you to upload an image - the script then makes a copy of the image and saves it onto the server, ready to be served/shares/whatever you want to do with it. You can find the video here: https://www.youtube.com/watch?v=-i0hat7pdpk

In the initial version, all errors are handled in the `upload` handler, making the following call:

```go
if err != nil {
    http.Error(w, err.String(), 500)
    return
}
```

This takes place on a few occasions in the handler (at different points), which is quite repetitive.

So, the first step is to replace all these blocks with a call to a check function. In this check function, if there is an error, call `panic`.

```go
check(err)

func check(err error) {
    if err != nil {
        panic(err)
    }
}
```

If you run this now, and there is an error (I forced an error by introducing a typo into the `enctype` attribute of the form in the `upload.html` file), the program will not send a response to the client, and the error stack trace will be added onto the console.

```
2016/05/01 16:24:06 http: panic serving 127.0.0.1:37848: request Content-Type isn't multipart/form-data
goroutine 19 [running]:
net/http.(*conn).serve.func1(0xc82011e200)
	/usr/lib/go-1.6/src/net/http/server.go:1389 +0xc1
```

So, the next concept introduced is to defer an anonymous function. Inside this function, `recover()` is called. Recover returns control when a panic is called, returning the value passed into panic. It's worth noting `recover` is only useful inside deferred functions, such as when you want to regain control from a panic.

```go
func errorHandler(fn http.HandlerFunc) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if e, ok := recover().(error); ok {
                w.WriteHeader(500)
                fmt.Fprintf(w, e.Error())
                //errorTemplate.Execute(w, e)
            }
        }()
        //call the actual handler
        fn(w, r)
    }
}
```

A new concept here is the syntax `recover().(error)`. This is known as [type assertion](https://golang.org/ref/spec#Type_assertions). `Recover` returns dynamic type (the value passed into `panic`) thus returns `Interface {}`. The latter part of the expression `.(error)`, asserts that the return value is of type `error`. The asserted type must implement the type of what `recover()` returns. If all that passes (it will for `string`s), the type will then become an `error`.

In the example which assigns two values (e, ok), ok becomes true if the assertion holds.

Another common approach would be:

```go
if e := recover(); e != nil {
    //doStuff
}
```

Full source.

Template html (html, head, etc ommitted):

```html
<p>Upload an image ot moustachify:</p>
<form action="/" method="POST" enctype="multipart/form-data">
<input type="file" name="image">
<input type="submit" value="Upload">
</form>
```

Program:

```go
package main

import (
    "net/http"
    "html/template"
    "io"
    "io/ioutil"
    "fmt"
)

var uploadTemplate,_ = template.ParseFiles("upload.html")
var errorTemplate,_ = template.ParseFiles("error.html")

func main() {
    http.HandleFunc("/", errorHandler(upload))
    http.HandleFunc("/view", errorHandler(handleView))
    http.ListenAndServe(":8080", nil)
}

func upload(w http.ResponseWriter, r *http.Request){

    if r.Method != "POST" {
        uploadTemplate.Execute(w, nil)
        return
    }

    f, _, err := r.FormFile("image")
    check(err)

    defer f.Close()
    t, err := ioutil.TempFile(".", "image-")
    check(err)
    defer t.Close()

    _, err = io.Copy(t, f)
    check(err)
    http.Redirect(w, r, "/view?id="+t.Name()[6:], 302)
}

func handleView(w http.ResponseWriter, r *http.Request){

    w.Header().Set("Content-Type", "image")
    http.ServeFile(w, r, "image-" + r.FormValue("id"))

}

func check(err error) {

    if err != nil {
        panic(err)
    }

}

func errorHandler(fn http.HandlerFunc) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if e, ok := recover().(error); ok {
                w.WriteHeader(500)
                fmt.Fprintf(w, e.Error())
                //errorTemplate.Execute(w, e)
            }
        }()
        //call the actual handler
        fn(w, r)
    }
}
```

Other resources:

* https://blog.golang.org/defer-panic-and-recover
* https://www.youtube.com/watch?v=-i0hat7pdpk
