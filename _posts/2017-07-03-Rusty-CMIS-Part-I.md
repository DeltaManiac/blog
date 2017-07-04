---
layout: post
title:  "Rusty CMIS Part I"
image: 'https://upload.wikimedia.org/wikipedia/en/f/f8/Cmis_logo.png'
date:   2017-07-03 00:08:06
tags:
description: 'Writing a CMIS Client in Rust'
categories:
- Rust
- CMIS
serie: learn
---

### Objective 
In order to learn Rust I have planned to spend an hour everyday tinkering on a project in Rust. 
Well atleast when I'm not overloaded with my day job over at Javaland. 
Let's get on with it then! 

### CMIS
What is CMIS ? 

*(Obligatory Copy Paste from Wikipedia cause I'm lazy)*

>"Content Management Interoperability Services (CMIS) is an open standard that allows different content management systems to inter-operate over the Internet.Specifically, CMIS defines an abstraction layer for controlling diverse document management systems and repositories using web protocols."

Now that the  "formal definition" is out of the way, in a laymans term I would put it up as Dropbox for Enterprise. That pretty much sums it up, but there are tons of other features/capabilities that are provided as part of the CMIS RFC which would be coming across if this series is not abandoned.  


### Enter Rust-Lang
I did do a small <a href="https://github.com/DeltaManiac/Rust_Proto">POC</a> kind to see if it would work and it surprisingly did.

If you clicked the link above I'm so sorry for what you had to see, but as a true dev at heart I Boldly State that it <b>WORKS</b>!(partially.....)

Since I already did a prototype where everything was dumped into a single file and got it working, I'd start again from scratch but this time as a Library(I have no idea how to do this).


### CMIS Link Compilation
- <a href="https://chemistry.apache.org/">Apache has an awesome collection of Client Libraries and Frameworks and Dev tools.</a>
- <a href="http://docs.oasis-open.org/cmis/CMIS/v1.1/CMIS-v1.1.html">The CMIS Specification Document</a>
- <a href="https://www.manning.com/books/cmis-and-apache-chemistry-in-action">The CMIS and Apache Chemistry in Action book.o
- <a href="https://wikipedia.org/wiki/Content_Management_Interoperability_Service">Wikipedia Entry</a>

### Lets Actually Start!

So in order to create a new library project let's use the cargo command
```
cargo new rusty_cmis
```
Which results in a project structure like below.

<img src="{{"../assets/img/rusty_cmis/i/lib_project.png"}}"/>

Well the lib.rs has file has some code which resembles something like a test already in it. Running the test I get a successful output.

<img src="{{"../assets/img/rusty_cmis/i/cargo_test.png"}}"/>

Let's try to make the test fail then. I think Rust should have an assert function which we can use.

Yes it does have an <a href="https://doc.rust-lang.org/std/macro.assert.html">assert</a> but apparently it is a Macro(We'll figure out what that is later)

~~~
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert!(1==2);
    }
}
~~~
{: .language-rust}

<img src="{{"../assets/img/rusty_cmis/i/cargo_test_fail.png"}}"/>

Yep it fails as expected, lets go a tiny bit further so that the code has 1 passing test and 1 failing test.

~~~
#[cfg(test)]
mod tests {
    #[test]
    fn it_does_not_work() {
        assert!(1==2);
    }
    #[test]
    fn it_does_work() {
        assert!(1==1);
    }
}
~~~
{: .language-rust}

And the result is 

<img src="{{"../assets/img/rusty_cmis/i/cargo_test_pass_fail.png"}}"/>

I think thats it for today and tomorrow we will try and get that nifty little build passing icon via a CI server or something.

:wq

Delta

*DISCLAIMER: I AM IN THE PROCESS OF LEARNING RUST, PLEASE TAKE ALL THE CONTENT WITH A GRAIN OF SALT, AND DO PROVIDE FEEDBACK.*



