---
layout: post
title:  "Rusty CMIS Part III"
image: 'https://upload.wikimedia.org/wikipedia/en/f/f8/Cmis_logo.png'
date:   2017-07-14 00:08:06
tags:
description: 'Writing a CMIS Client in Rust'
categories:
- Rust
- CMIS
- Reqwest
- HTTP
- SerDe
serie: learn
---

### Objective 
In my <a href="https://deltamaniac.github.io/Rusty-CMIS-Part-II">previous post</a> we made our code do builds on code pushes.

I've decided that we should write some code this time and not waste time adding another free CI to do our build [Even though I really really want to].


### CMIS Connectivity 

CMIS Repositories are accessible over the network i.e. via HTTP in order client must be able to talk to the repository in order to prove that our client works.

Rust has reusable modules of code (libraries) known as crates, which makes our job a lot easier because we can reuse them.

They are available from <a href="https://crates.io">crates.io</a>. I used our trusty search engine to find an HTTP Library that would allow help us.


### Reqwest 

Searching for "http" on crates.io gave the following result

<img src="{{"../assets/img/rusty_cmis/iii/cargo_http.png"}}"/>

* http-rust 
    * prealpha version I guess.
* serde_json 
    * Json **SER**ializer **DE**serializer not an HTTP library.
* hyper 
    * Seems like a well used and hence a usable HTTP framework
* reqwest
    * Seems a bit less complicated than hyper.

I choose reqwest over hyper because it seems a bit more easier after a quick glance at the documentation <a href="https://docs.rs/reqwest/0.7.1/reqwest/">here</a>.

To use the library we add the crate name and version to our Cargo.toml.

~~~
[dependencies]
reqwest = "0.7.1"
~~~
{ :.language-toml}

Building out the application now downloads the crate and its dependencies, then compiles them to be used in our application.

<img src="{{"../assets/img/rusty_cmis/iii/reqwest_build.png"}}"/>

### Talking to a Server

Lets us establish a connection to an arbitrary <a href="https://www.rust-lang.org">URL</a>.

~~~
extern crate reqwest;

mod http {
    use reqwest;
    pub fn call_url(url: &str) -> reqwest::StatusCode {
        let response = reqwest::get(&url[..]);
        let result = match response {
            Err(e) => panic!(e),
            Ok(_) => response.unwrap().status(),
        };
        result
    }
}

#[cfg(test)]
mod http_tests {
    use super::*;
    #[test]
    #[should_panic]
    fn test_http_panic() {
        http::call_url("http://invalidhost:4000");
    }
    #[test]
    fn test_http_ok() {
        assert_eq!(reqwest::StatusCode::Ok,
                   http::call_url("https://www.rust-lang.org"));
    }
}

~~~
{: .language-rust}

The above code is pretty straight forward, all it does it create our *"custom"* module that does HTTP calls.

We have supplemented the same with a set of unit tests that should panic and one that should pass.

<img src="{{"../assets/img/rusty_cmis/iii/reqwest_basic.png"}}"/>

### Parsing a JSON Response 

The defacto crate used by most rustacean for json manipulation is the <a href="https://serde.rs/">serde_json crate</a>, luckily for us this crate is used in the reqwest crate that we already imported.

Lets try an http request to a publicly available user list <a href="https://jsonplaceholder.typicode.com/users">service</a>. The *users* endpoint of this service is supposed return a total of 10 users as a json body.

Lets create another method  that does a url invocation and returns the response parsed as a string object. Once the response is recieved we use `serde_json::from_str` method to convert it to a serde_json::Value which was cast into an array and find the length of the array.

~~~
extern crate reqwest;
extern crate serde_json;

mod http {
    use reqwest;
    use serde_json::*;
    use std::io::Read;
    pub fn call_url(url: &str) -> reqwest::StatusCode {
        let response = reqwest::get(&url[..]);
        let result = match response {
            Err(e) => panic!(e),
            Ok(_) => response.unwrap().status(),
        };
        result
    }

    pub fn call_json_url(url: &str) -> String{
        let response = reqwest::get(&url[..]);
        let mut response_string = String::new();
        match response {
            Err(e) => panic!(e),
            Ok(_) => response.unwrap().read_to_string(&mut response_string),
        };
        response_string
    }
}

#[cfg(test)]
mod http_tests {
    use super::*;

    #[test]
    #[should_panic]
    fn test_http_panic() {
        http::call_url("http://invalidhost:4000");
    }
   
    #[test]
    fn test_http_ok() {
        assert_eq!(reqwest::StatusCode::Ok,
                   http::call_url("https://www.rust-lang.org"));
    } 

    #[test]
    fn test_http_json_response() {
        assert_eq!(reqwest::StatusCode::Ok,
                   http::call_url("https://jsonplaceholder.typicode.com/users"));
    }

    #[test]
    fn test_http_json_response_parse() {
        let json_data : serde_json::Value = serde_json::from_str(
                &(http::call_json_url("https://jsonplaceholder.typicode.com/users"))).unwrap();
        assert_eq!(10,json_data.as_array().unwrap().len());
    }
}

~~~
{: .language-rust}


Commit....Done

Push.....Done

Automated Tests....<a href="https://travis-ci.org/DeltaManiac/rusty_cmis/builds/255766092">Passed</a>

Next time we will try communicating with an actual CMIS Server.

:wq

Delta

*DISCLAIMER: I AM IN THE PROCESS OF LEARNING RUST, PLEASE TAKE ALL THE CONTENT WITH A GRAIN OF SALT, AND DO PROVIDE FEEDBACK.*