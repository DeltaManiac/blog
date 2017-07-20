---
layout: post
title:  "Rusty CMIS Part III"
image: 'https://upload.wikimedia.org/wikipedia/en/f/f8/Cmis_logo.png'
date:   2017-07-04 00:08:06
tags:
description: 'Writing a CMIS Client in Rust'
categories:
- Rust
- CMIS
- Hyper
- HTTP
serie: learn
---

### Objective 
In my <a href="https://deltamaniac.github.io/Rusty-CMIS-Part-II">previous post</a> we made our code do builds on code pushes.

I've decided that we should write some code this time and not waste time adding another free CI to do our build.


### CMIS Connectivity 

CMIS Repositories are accessible over the network i.e. via HTTP in order client must be able to talk to the repository in order to say that our client funtions.

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

Lets try an http request to a publicly available user list <a href="https://jsonplaceholder.typicode.com/users">service</a>. The *users* endpoint of this service is supposed retunr a total of 10 users as a json body.

Lets create another method that does a url invocation and returns the parsed json object.



### Onwards My Friend
* Assuming that the github account is linked to Travis CI we select the repo we want to use and enable travis.


<img src="{{"../assets/img/rusty_cmis/ii/travis_on.png"}}"/>

* Navigating to the Travis CI Dashboard will indicate that no build has been triggered. The first build will only be triggered after a push has been done to the repository post the configuration we did in Step 1.

<img src="{{"../assets/img/rusty_cmis/ii/travis_dashboard.png"}}"/>

* Create .travis.yml file with the following content and push it to the repository.

~~~
language: rust
script:
  - cargo build --verbose --all
  - cargo test --verbose --all
~~~
{: .language-yml}
* Refresh the Travis CI page and if everything is configured properly you'll be greated with the expected red color text and failure.

<img src="{{"../assets/img/rusty_cmis/ii/travis_test_fail.png"}}"/>

* Fix the test case.

assert_ne! is a macro in std which asserts for inequality.

~~~
#[cfg(test)]
mod tests {
    #[test]
    fn it_does_not_work() {
        assert_ne!(1,2); 
    }
    #[test]
    fn it_does_work() {
        assert!(1==1);
    }
}
~~~
{: .language-rust}

* Commit and Push.

* Put on your coolest pair of shades
<img src="http://s2.quickmeme.com/img/ac/acdc1c11fb2aacc8f90e45dbe9d7dccc4f3a87e9ce323ee4ef1f007290742f37.jpg"/>
* Watch as the build turns green and passes. YAY!!


<img src="{{"../assets/img/rusty_cmis/ii/travis_test_pass.png"}}"/>


### Displaying Build Info 
Create a readme.md file and paste the snippet below (repo name and user name changed ofcourse)
~~~
# Rusty CMIS [![Build Status](https://travis-ci.org/DeltaManiac/rusty_cmis.svg?branch=master)](https://travis-ci.org/DeltaManiac/rusty_cmis)

A CMIS Client Library in the making.
~~~

And we have a minimal readme.md that looks like this 

<img src="{{"../assets/img/rusty_cmis/ii/build_status.png"}}"/>

### Footnote

The reason why we did this activity is so that we can easily get to know if we push some changes that makes the test cases fail and cause I wanted to do this project with a standard set of tests and mainly because it was BLOODY FUN!

Oh.. and Travis CI has excellent documentation that can be accessed <a href="https://docs.travis-ci.com/user/getting-started/" >here</a>.

Next time we shall actually start coding!

:wq

Delta

*DISCLAIMER: I AM IN THE PROCESS OF LEARNING RUST, PLEASE TAKE ALL THE CONTENT WITH A GRAIN OF SALT, AND DO PROVIDE FEEDBACK.*



