# 01 Getting Started

## Project setup and dependencies

After setting up a new project in Leiningen with `lein new <project-name>`, you must add the required dependencies in the `project.clj` file: there is an vector just for that. By default, only the clojure language itself will be listed in the dependency vector.

### Adding ring as a dependency

For a web project, we want to use the `ring` package, which map HTTP requests to clojure functions calls (similar to `rake` in ruby or `wsgi` in python). Add it to the dependencies. 

```clojure
...
:dependencies [[org.clojure/clojure "1.7.0"]
               [ring "1.2.0"]])
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

### Adding the ring plugin

When writing a ring project, there are some common tasks:
- Pointing to default Ring handler function
- Defining functions to be called upon creation and destruction of our web app instance, like a database connection opening / closing
- Starting and configuring standalone Ring web server for development (usually via the jetty adapter)
- Building war or uberjar package
- Reloading the Namespace

Leiningen has a bunch of plugins that can be installed to simplify development and there is one for Ring project, named [Lein-ring](https://github.com/weavejester/lein-ring).

Plugins are also added to the `project.clj` file, under a `:plugin` key. There is none by default. 

```clojure
...
:dependencies [[org.clojure/clojure "1.7.0"]
               [ring "1.2.0"]]
:plugins [[lein-ring "0.8.7"]])
```

The functionalities added by the plugin are configured just below it.

```clojure
...
:dependencies [[org.clojure/clojure "1.7.0"]
               [ring "1.2.0"]]
:plugins [[lein-ring "0.8.7"]]
:ring {:handler server.core/example-handler ;; function called upon request [name it as you like]
       :init    server.core/on-init         ;; function called upon creation [name it as you want]
       :destroy server.core/on-destroy})    ;; function called upon destruction [name it as you wish]
```

The `:handler` function will map HTTP requests to our Clojure code and give us access to request data. It will also help us to create adequate response. 

## A simple server

A super simple handler function could just send back the string "Hello Clojure" as response for all requests.

```clojure
(def example-handler [request]  ;; receives request as argument from Ring
  {:body "Hello Clojure"})      ;; respond with a map with key body.
```

Super simple `:init` and `:destroy` functions could just inform us that the server has been launched/destroyed.

```clojure
(defn on-init []
  (println "Initializing sample webapp!"))

(defn on-destroy []
  (println "Destroying sample webapp!"))
```

To launch a development server, type:

```
$ lein ring server-headless
```

A nice goodie of clojure/ring is included live-code-reloading. We don't have to stop and restart the server everytime we change the code. 

## Additional config options

Ring has lots of config options that can be setup in `project.clj`:

```clojure
:ring {:handler server.core/example-handler ;; function called upon request (name it as you like)
       :port    4004                        ;; favorite port
       :init    server.core/on-init         ;; function called upon creation (name it as you want)
       :destroy server.core/on-destroy}     ;; function called upon destruction (name it as you wish)
```

## Packaging the app
There are several formats to package an app.

### Uberjar
When the app is simple and doesn't require advanced functionalities, the uberjar format can be used. It packages the app and its dependencies into one file. 

The Lein ring plugin provide a helper function to easily create an uberjar.

```
$ lein ring uberjar
```

The uberjar file can be used directly with java.

```
$ java -jar <uberjar-file>
```

### Uberwar
If we want more complex functionalities (e.g running multiple web apps on the same port), we probably need to run the app behind a web application container like Apache Tomcat. For this, the uberwar format is fitter than uberjar.

The Uberwar format also combines app and dependencies into one file.

```
$ lein ring uberwar project1.war
```

We can then launch a tomcat instance and copy the uberwar file into tomcat's `/webapp` directory to trigger deployement. 

```
$ cd ~/apache-tomcat-8.0.0-RC3 ./bin/startup.sh
$ cp project1.war ~/apache-tomcat-8.0.0-RC3/webapps/
```

The app will be available under its name subdomain: `localhost:8080/project1/`. You can monitor the output of your webapp in the catalina log:

```
$ tail -F  ~/apache-tomcat-8.0.0-RC3/logs/catalina.out
```


