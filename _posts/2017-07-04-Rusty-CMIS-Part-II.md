---
layout: post
title:  "Rusty CMIS Part II"
image: 'https://upload.wikimedia.org/wikipedia/en/f/f8/Cmis_logo.png'
date:   2017-07-04 00:08:06
tags:
description: 'Writing a CMIS Client in Rust'
categories:
- Rust
- CMIS
- Travis
- Continuous Integration
serie: learn
commentIssueId: 1
---

### Objective 
In my <a href="https://deltamaniac.github.io/Rusty-CMIS-Part-I">previous post</a> we started with the lowest level possible by creating a simple library and running the test command.

In hindsight I didn't do anything much (sigh), today we wont be doing anything much other than try and integrate our project with Travis CI.

### Travis CI

Travis CI helps us automate unit testing and build of our project.

Obligatory link to <a href="https://en.wikipedia.org/wiki/Test-driven_development">Test Driven Design.</a>


### Onwards My Friend
* Assuming that the github account is linked to Travis CI we select the repo we want to use and enable travis.

<img src="{{"../assets/img/rusty_cmis/ii/travis_off.png"}}"/>

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



