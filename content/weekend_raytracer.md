+++
title ="Weekend Ray Tracer in Rust"
description= "Following along Peter Shirely's excellent book"
date =   2019-10-29
in_search_index = true
[taxonomies]
tags = ["rust","gfx","ray tracing"]
categories = ["Rust","gfx"]
+++

I spent 7 days going through <a href="https://github.com/petershirley/raytracinginoneweekend">Ray Tracing in One Weekend</a> and learnt a few things.

<!-- more -->

I have never done serious graphics programming in my life, yes I have tinkered with OpenGL and drawn multi colored triangles but thats about it. So I sat down and spent some time writing one in rust and its been a great experience.



## Things I learnt
1. Operator Overloading

    Operator overloading in rust is suprisingly straight forward and trait based. The ``std::ops`` define the traits that can be overloaded or implemented for your type.

    To implement the `+` operator and `+=` for a custom struct Vec3 we have to implement the `std::ops::Add` and the `std::ops::AddAssign` traits repectively.

    ~~~rust
    pub struct Vec3 {
       e: [f64; 3],
    }

    impl Add for Vec3 {
        type Output = Vec3;

        fn add(self, other: Vec3) -> Vec3 {
          Vec3 {
              e: [
                  self.e[0] + other.e[0],
                  self.e[1] + other.e[1],
                  self.e[2] + other.e[2],
              ],
           }
        }
    }   
    
    impl AddAssign for Vec3 {
       fn add_assign(&mut self, other: Vec3) {
            *self = Vec3 {
                e: [
                    self.e[0] + other.e[0],
                    self.e[1] + other.e[1],
                    self.e[2] + other.e[2],
                ],
            };
        }
    }
    ~~~

2. Ray tracing is hard ?!

   ![](/images/weekend_raytracer/render.jpg)

   May be... ¯\\\_(ツ)_/¯

Cheers

Delta