# 01 Getting Started

## Project setup and dependencies

After setting up a new project in Leiningen with `lein new <project-name>`, you must add the required dependencies in the `project.clj` file: their is an vector just for that. By default, only the clojure language itself will be listed in the dependency vector.

For a web project, we want to use the `ring` package, which map HTTP requests to clojure functions calls (similar to `rake` in ruby or `wsgi` in python). Add it to the dependencies. 

```clojure
:dependencies [[org.clojure/clojure "1.7.0"]
               [ring "1.2.0"]]
```

Calling `ring` is in fact a shortcut for a bundle of libraries:
- `ring-core`: the basic library (functions to handle parameters, cookies...)
- `ring-devel`: the development/debugging functionalities
- `ring-servlet`: the adapter for running ring webapps inside of java servlet containers
- `ring-jetty-adapter`: the adapter to run ring webapps as standalone jetty instance, jetty being a java HTTP web server

Leiningen will download listed dependencies automatically. If you want to force it though, you can type 

```
$ lein deps
```
