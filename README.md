Forked มาจาก [Go Web Examples](https://gowebexamples.com/) 

# Go Web Examples

[Go Web Examples](https://gowebexamples.urairat.com/) provides easy to understand snippets on how to do web development in Go and is inspired by [Go By Example](https://gobyexample.com/), which has a great introduction into the fundamentals of this language.

Go Web Examples is hosted on: https://gowebexamples.urairat.com/

## Examples

There are currently tutorials and examples of the following topics:

- [Hello World](https://gowebexamples.urairat.com/hello-world/)
- [HTTP Server](https://gowebexamples.urairat.com/http-server/)
- [Routing (using gorilla/mux)](https://gowebexamples.urairat.com/routes-using-gorilla-mux/)
- [Templates](https://gowebexamples.urairat.com/templates/)
- [Requests and Forms](https://gowebexamples.urairat.com/forms/)
- [Assets and Files](https://gowebexamples.urairat.com/static-files/)
- [Basic Middleware](https://gowebexamples.urairat.com/basic-middleware/)
- [Advanced Middleware](https://gowebexamples.urairat.com/advanced-middleware/)
- [Sessions](https://gowebexamples.urairat.com/sessions/)
- [JSON](https://gowebexamples.urairat.com/json/)
- [Websockets](https://gowebexamples.urairat.com/websockets/)
- [Password Hashing (bcrypt)](https://gowebexamples.urairat.com/password-hashing/)

## Building

The site is built using the [Hugo](https://github.com/spf13/hugo) static site generator.
The source codes to the examples can be found in the `content` directory.
When you build the site using the `hugo` command, the output files will be generated into the `public` directory.

To build this site Hugo and Python (with `pygments` installed) will be required.

```console
$ git clone https://github.com/gowebexamples/gowebexamples.git
$ cd gowebexamples
$ hugo
```

## Thanks

Thanks to [Go By Example](https://gowebexamples.urairat.com/) for the inspiration to this project.

## License

MIT
