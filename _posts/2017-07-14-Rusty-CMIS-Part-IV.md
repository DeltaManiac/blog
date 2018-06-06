---
layout: post
title:  "Rusty CMIS Part IV"
image: 'https://upload.wikimedia.org/wikipedia/en/f/f8/Cmis_logo.png'
date:   2017-08-01 00:00:06
tags:
description: 'Writing a CMIS Client in Rust'
categories:
- Rust
- CMIS
- Modules 
serie: learn
---

### Objective 

Try to get the hang of the module systems in rust.

### Rust Modules

As any programming language would have, rust provides a mechanism for us to split codes into logical units and manage their visibilty called modules.

Rust's module systems are depended on the file/directory hierarchy of the project.

### Client Module

I went ahead and removed all of the content in the HTTP test module that we had implemented earlier in favour of a newer, more compartmentalized layout rather than smashing all our code into a single Rust file.

<img src="{{"../assets/img/rusty_cmis/iv/module_struct.png"}}"/>

We have a folder called Client which has a mod.rs and repository.rs file. In rust whenver we use the `mod mod_name` keyword it looks for a `mod_name.rs` or a for a folder named `mod_name` and then the mod.rs file inside the folder as the module.

~~~
mod Client;

 #[cfg(test)]
 mod client_tests {
     use super::*;
     use Client::*;
     #[test]
     fn test_client_username(){
         let client = Client::CMIS_Client::new(String::from("https://cmis.alfresco.com/alfresco/api/-default-/public/cmis/versions/1.1/browser"),String::from("admin"),String::from("admin"));
         assert_eq!(String::from("admin"), client.get_username());
     }

     #[test]
     fn test_client_password(){
         let client = Client::CMIS_Client::new(String::from("https://cmis.alfresco.com/alfresco/api/-default-/public/cmis/versions/1.1/browser"),String::from("admin"),String::from("admin"));
         assert_eq!(String::from("admin"), client.get_password());
     }
 }
~~~
{: .language-rust}


 The first line `mod Client` in lib.rs finds the the mod.rs file in the Client folder which has our client module that holds the CMIS Client that we will be creating.


~~~

mod repository;
use Client::repository::*;

#[derive(Debug)]
pub struct CMIS_Client {
    repository : Option<Repository>,
    base_url : String,
    username : String,
    password : String
}


impl CMIS_Client {

    pub fn new(base_url:String, username:String, password:String) -> CMIS_Client {
    
        CMIS_Client {
            base_url: base_url,
            username: username,
            password:password,
            repository :None 
        }
    }

    pub fn get_password(self) -> String {
        self.password
    }
    pub fn get_username(self) -> String {
        self.username
    }
    
}

~~~
{: .language-rust}

The above `mod.rs` file contains a `mod repository` which is looking for a repository folder or a repository.rs file, we have in the same location a repository.rs file which will be used. Once the module is imported from the repository.rs we can use the Repository struct decalred in the module in our CMIS_client struct.

~~~

#[derive(Debug)]
pub struct Repository {
    repository_id : String,
    repository_name : String,
    root_folder_id : String,
    changelog_token : String,
    cmis_version : String
}

~~~
{: .language-rust}


The CMIS_Client struct holds the authentication information, and the repository object, the `Option<Repository>` allows that field in the struct to have `None` value or a `Repository` object which we shall be doing in the next post.


:wq

Delta

*DISCLAIMER: I AM IN THE PROCESS OF LEARNING RUST, PLEASE TAKE ALL THE CONTENT WITH A GRAIN OF SALT, AND DO PROVIDE FEEDBACK.*

*Update : This series has been discontinued as I feel CMIS is no longer an actively used protocol on dropbox/googledrive*
