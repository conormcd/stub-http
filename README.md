# fake-http

A Clojure library designed to fake HTTP responses regardless of which library used to actually make the HTTP requests.
  
There are several library specific "http faking" libraries out there such as [clj-http-fake](https://github.com/myfreeweb/clj-http-fake) and 
[ring-mock](https://github.com/ring-clojure/ring-mock) but they won't work unless you're using a specific library. I couldn't find a library agnostic library for 
faking HTTP responses so I sat out to write one myself based on [nanohttpd](https://github.com/NanoHttpd/nanohttpd). This is useful
if you want to test your app against a "real" HTTP server with actual HTTP requests. And even if you don't _want_ to this it may be your only
option if you're (for example) is using a Java library that makes HTTP requests and you want to fake its responses.

More docs and implementation is coming soon.

## Usage

```clojure
(with-routes! 
	{"something" {:status 200 :content-type "application/json" :body (json/generate-string {:hello "world"})}
	 {:path "/y" :query-params {:q "something")}} {:status 200 :content-type "application/json" :body  (json/generate-string {:hello "brave new world"})}}
	 ; Do actual HTTP request
	 )
```

## Full Examples

### Example 1
Example demonstrating integration with clojure test and [clj-http-lite](https://github.com/hiredman/clj-http-lite). 
This example matches path "/something" and returns the json document
     
```javascript
{ "hello" : "world" }
```

as response: 

```clojure
(ns fake-http.example1
  (:require [clojure.test :refer :all]
            [fake-http.fake :refer :all]
            [cheshire.core :as json]
            [clj-http.lite.client :as client]))

(deftest Example1  
    (with-routes!
      {"/something" {:status 200 :content-type "application/json"
                     :body   (json/generate-string {:hello "world"})}}
      (let [response (client/get (str uri "/something"))
            json-response (json/parse-string (:body response) true)]
        (is (= "world" (:hello json-response))))))
```

## License

Copyright © 2016 Johan Haleby

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
