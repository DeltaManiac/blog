+++
title = "Neural Nets an Initiation"
description= "A short expirement with Neural Networks"
date =   2016-03-12 
in_search_index = true
[taxonomies]
tags = ["AI","rust","neural network"]
categories = ["Rust"]
+++

This blog post is a knee jerk reaction after reading <a href="https://twitter.com/iamtrask">@iamtrask</a>'s excellent <a href="http://iamtrask.github.io/2015/07/12/basic-python-network/">blog post</a>.
It provides a good entry point into neural networks and how it works (Treasure trove for the uninitiate like me). Most of this articles content is what _(I think)_ I have understood after reading his post and a much more simpler write up of the same <a href="https://medium.com/technology-invention-and-more/how-to-build-a-simple-neural-network-in-9-lines-of-python-code-cc8f23647ca1">here</a>.
<!-- more -->
So with out much further ado let us begin,

This is the python code snippet which is explained in trasks blog post (I will not be walking through this code as he does it way better than me).


~~~py
 import numpy as np
 
 # sigmoid function
 def nonlin(x,deriv=False):
     if(deriv==True):
         return x*(1-x)
     return 1/(1+np.exp(-x))
     
 # input dataset
 X = np.array([  [0,0,1],
                 [0,1,1],
                 [1,0,1],
                 [1,1,1] ])
     
 # output dataset            
 y = np.array([[0,0,1,1]]).T
 
 # seed random numbers to make calculation
 # deterministic (just a good practice)
 np.random.seed(1)
 
 # initialize weights randomly with mean 0
 syn0 = 2*np.random.random((3,1)) - 1
 
 for iter in xrange(10000):
 
     # forward propagation
     l0 = X
     l1 = nonlin(np.dot(l0,syn0))
 
     # how much did we miss?
     l1_error = y - l1
 
     # multiply how much we missed by the 
     # slope of the sigmoid at the values in l1
     l1_delta = l1_error * nonlin(l1,True)
 
     # update weights
     syn0 += np.dot(l0.T,l1_delta)
 
 print "Output After Training:"
 print l1
~~~

The above code runs iterations and adjusts the  synapses so as to make an informed decision for a given new input.

Lets try that out by adding the following lines to the end of the script and running it.


~~~py
 print "Output After Training:" 
 print l1
 print "Testing the Neural Net" 
 print "Input: 1,0,0"
 print  nonlin(np.dot(np.array([[1,0,0]]),syn0))
 print "Input: 0,1,0"
 print nonlin(np.dot(np.array([[0,1,0]]),syn0))
 print "Input: 0,0,1"
 print nonlin(np.dot(np.array([[0,0,1]]),syn0))
~~~

The output is as below

~~~
 Output After Training:
 [[ 0.00301758]        
  [ 0.00246109]        
  [ 0.99799161]        
  [ 0.99753723]]       
 Testing the Neural Net
 Input: 1,0,0          
 [[ 0.99999391]]       
 Input: 0,1,0          
 [[ 0.4490743]]        
 Input: 0,0,1          
 [[ 0.00301757]]       
~~~

We get the output which shows that our Network has learnt that if the first input value is a '1' then the output is a '1'_(0.999999)_.

I ported the code <a href="https://gist.githubusercontent.com/miloharper/c5db6590f26d99ab2670/raw/bff459fbd922a31bc3988632cafb1687699a8d11/main.py">here </a> into Rust and it looks like this.

~~~rust
 extern crate rand;
 use rand::Rng;
 
 fn main() {
 
     let mut rng = rand::thread_rng();
     let mut syn_weight: Vec<f64> = Vec::new();
     for _ in 0..3 {
         syn_weight.push(rng.gen_range::<f64>(-1.0, 1.0));
     }
 
     let input_set: Vec<Vec<f64>> = vec![vec![0.0, 0.0, 1.0],
                                         vec![1.0, 1.0, 1.0],
                                         vec![1.0, 0.0, 1.0],
                                         vec![0.0, 1.0, 1.0]];
     let output_set: Vec<Vec<f64>> = vec![vec![0.0], vec![1.0], vec![1.0], vec![0.0]];
 
     for _ in 0..10000000 {
         vec_dot(&input_set, &syn_weight);
         let output = vec_sigmoid(&vec_dot(&input_set, &syn_weight));
         let mut error: Vec<f64> = Vec::new();
         for (idx, val) in output_set.iter().enumerate() {
             error.push(val[0] - output[idx]);
         }
         let derivative = vec_sigmoid_derivative(&output);
         let mut result: Vec<f64> = Vec::new();
         for (idx, val) in derivative.iter().enumerate() {
             result.push(val * error[idx]);
         }
         syn_weight = vec_add(&(vec_dot(&(vec_transpose(&input_set)), &result)),
                              &syn_weight);
     }
     let test_input: Vec<Vec<f64>> = vec![vec![1.0, 0.0, 0.0]];
     println!("Output");
     println!("{:?}", vec_sigmoid(&vec_dot(&test_input, &syn_weight)));
 }
 
 fn vec_transpose(input: &Vec<Vec<f64>>) -> Vec<Vec<f64>> {
     let mut outer: Vec<Vec<f64>> = Vec::new();
     for z in 0..input[0].len() {
         let mut inner: Vec<f64> = Vec::new();
         for y in 0..input.len() {
             inner.push(input[y][z]);
         }
         outer.push(inner);
     }
     outer
 }
 
 fn vec_add(x: &Vec<f64>, y: &Vec<f64>) -> Vec<f64> {
     x.into_iter()
         .zip(y.into_iter())
         .map(|(a, b)| a + b)
         .collect::<Vec<f64>>()
 }
 
 fn sigmoid(x: &f64) -> f64 {
     1.0 / (1.0 + ((-1.0 * x).exp()))
 }
 
 fn vec_sigmoid(x: &Vec<f64>) -> Vec<f64> {
     x.into_iter().map(sigmoid).collect::<Vec<f64>>()
 }
 
 fn sigmoid_derivative(x: &f64) -> f64 {
     x * (1.0 - x)
 }
 
 fn vec_sigmoid_derivative(x: &Vec<f64>) -> Vec<f64> {
     x.into_iter()
         .map(sigmoid_derivative)
         .collect::<Vec<f64>>()
 }
 
 fn vec_dot(x: &Vec<Vec<f64>>, y: &Vec<f64>) -> Vec<f64> {
     let mut result: Vec<f64> = Vec::new();
     for val in x.iter() {
         let mut sum: f64 = 0.0;
         for (idx_inner, value) in y.iter().enumerate() {
             sum += val[idx_inner] * value;
         }
         result.push(sum);
     }
     result
 }
~~~


Since I am just a rust initiate I had my code reviewed by people on the internet <a href="https://www.reddit.com/r/rust/comments/65h97i/review_and_feedback_of_first_rust_code_for_a/">here</a>. The people over at the <a href="https://www.reddit.com/r/rust/">Rust</a> subreddit are quite friendly folk, I'd encourage you to head on over there for more exciting info about rust.


## Benchmarking Performance
Rust boasts about being a blazingly fast language powered by LLVM. 

Lets checkout that claim.

I bumped up the iteration to teach my network to 10 million and did a simple benchmark with the <a href="https://en.wikipedia.org/wiki/Time_(Unix)">time</a> command.

First the Python code 

![](/images/neural_net/python_benchmark.png)

It takes about 110 seconds to the completely train over 10 million iterations and predict an output for the unkown case of [1,0,0]. Not bad at all!

Next the Rust code

![](/images/neural_net/rust_debug_benchmark.png)

It takes about 135 seconds to the completely train over 10 million iterations and predict an output for the unkown case of [1,0,0]. I was not impressed at the numbers at all. We can quickly figure out that this was run on a debug + unoptimized version of the binary.

Next the Rust code Release Build

![](/images/neural_net/rust_release_benchmark.png)

![](/images/neural_net/jaw_drop.png)


^^ That was my reaction literally follow by a few dirty words after seeing the result. Just approximately 4 seconds to do the entire 10 million iteration and compute a result. Boy am I Impressed!

I am not doing the math as the % of improvement is definitely high up than I expected. I am sold on Rust at this point and will definitely try out more stuff in Rust.

Guess thats all that I have for now and feel free to drop me a mail with your thoughts and comments.

PS: I know that time is not the right benchmarking tool, and about cache misses and so... This not the actual performance comparison but just my observation. Take it with a grain of salt.

Cheers

Delta