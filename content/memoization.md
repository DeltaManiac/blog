+++
title = "A rod of made of metals"
description= "Trying out a memoization in Rust"
date = 2017-08-12 
in_search_index = true
[taxonomies]
tags = ["memoization","rust"]
categories = ["Rust"]
+++

Try and solve a Dynamic Programming Problem in Rust.
<!-- more -->

# The Problem 

Bob wants to create a rod made of metals. The metals can be made of either Iron, Chromium, Aluminium, Silver or Gold (he need not use all five types of metals).

Each metal has a value associated with it's strength as follows:

    -2 for Iron
    -1 for Chromium
    0 for Aluminium
    1 for Silver
    2 for Gold

Each metal also has a cost given by I, C, A, S and G for the respective metal.    

You are given q queries. In each query, find the minimum cost for creating a rod using exactly M metals and overall strength of N.

Overall strength N is defined as the sum of strength of each metal used in the rod.

Input:

First line contains 5 space separated integers I, C, A, S, G, the respective cost of metals. Next line contains q the number of queries next q lines had two integers M and N, the number of metals to be used and the overall strength.

Output:

For each query, output the answer in a new line. Output the minimum cost if it exists otherwise print -1.

Constraints:

    1 ≤ q ≤5 * 10^5
    0 ≤ I, C, A, S, G ≤ 10^6
    0 ≤ M ≤ 100
    −200 ≤ N ≤ 200


Input:

    2 2 3 4
    3
    0
    -2
    6

Output:

    4
    1
    2

# Solution 

The solution given below solves the problem in 3 different ways

* Recursive Approach
* Bottom Up Approach
* Top Down Apprroach

~~~rust
 use std::collections::HashMap;
 
 fn main() {
     let metal_str = vec![-2, -1, 0, 1, 2];
     let metal_cost = vec![1, 2, 2, 3, 4];
     assert_eq!(
         12,
         get_min_cost(metal_str.as_slice(), metal_cost.as_slice(), 6, 3, 0, 0, 0)
     );
     assert_eq!(
         4,
         get_min_cost(metal_str.as_slice(), metal_cost.as_slice(), 0, 2, 0, 0, 0)
     );
     assert_eq!(
         1,
         get_min_cost(metal_str.as_slice(), metal_cost.as_slice(), -2, 1, 0, 0, 0)
     );
     assert_eq!(
         std::i32::MAX,
         get_min_cost(metal_str.as_slice(), metal_cost.as_slice(), 22, 1, 0, 0, 0)
     );
     let mut dp_table: HashMap<i32, HashMap<i32, HashMap<i32, i32>>> = HashMap::new();
     assert_eq!(
         12,
         get_min_cost_top_down(
             metal_str.as_slice(),
             metal_cost.as_slice(),
             6,
             3,
             &0,
             &0,
             &0,
             &mut dp_table,
         )
     );
     dp_table = HashMap::new();
     assert_eq!(
         4,
         get_min_cost_top_down(
             metal_str.as_slice(),
             metal_cost.as_slice(),
             0,
             2,
             &0,
             &0,
             &0,
             &mut dp_table,
         )
     );
     dp_table = HashMap::new();
     assert_eq!(
         1,
         get_min_cost_top_down(
             metal_str.as_slice(),
             metal_cost.as_slice(),
             -2,
             1,
             &0,
             &0,
             &0,
             &mut dp_table,
         )
     );
     dp_table = HashMap::new();
     assert_eq!(
         std::i32::MAX,
         get_min_cost_top_down(
             metal_str.as_slice(),
             metal_cost.as_slice(),
             22,
             1,
             &0,
             &0,
             &0,
             &mut dp_table,
         )
     );
     assert_eq!(
         12,
         get_min_cost_btm_up(metal_str.as_slice(), metal_cost.as_slice(), 6, 3)
     );
     assert_eq!(
         4,
         get_min_cost_btm_up(metal_str.as_slice(), metal_cost.as_slice(), 0, 2)
     );
     assert_eq!(
         1,
         get_min_cost_btm_up(metal_str.as_slice(), metal_cost.as_slice(), -2, 1)
     );
     assert_eq!(
         std::i32::MAX,
         get_min_cost_btm_up(metal_str.as_slice(), metal_cost.as_slice(), 22, 1)
     );
 }

 //===========================//
 fn get_min_cost_btm_up(
     metal_str: &[i32],
     metal_cost: &[i32],
     target_str: i32,
     target_count: i32,
 ) -> i32 {
     let mut dp_table: HashMap<i32, HashMap<i32, i32>> = HashMap::new();
     dp_table.insert(0, HashMap::new());
     dp_table.get_mut(&0).unwrap().insert(0, 0);
 
     for i in 1..target_count + 1 {
         dp_table.insert(i, HashMap::new());
         let mut prev_str_keys = get_keys_as_vec(&dp_table, i);
         for k in prev_str_keys {
             for j in 0..metal_str.len() {
                 let curr_cost = *dp_table.get(&(i - 1)).unwrap().get(&k).unwrap();
                 let new_cost = curr_cost + metal_cost[j];
                 if dp_table.contains_key(&i)
                     && !dp_table.get(&i).unwrap().contains_key(&(metal_str[j] + k))
                 {
                     let idx = metal_str[j] + k;
                     dp_table.get_mut(&i).unwrap().insert(idx, new_cost);
                 } else if new_cost < *dp_table.get(&i).unwrap().get(&(metal_str[j] + k)).unwrap() {
                     {
                         let idx = metal_str[j] + k;
                         dp_table.get_mut(&i).unwrap().insert(idx, new_cost);
                     }
                 }
             }
         }
     }
 
     if dp_table.contains_key(&target_count) && dp_table
         .get(&target_count)
         .unwrap()
         .contains_key(&target_str)
     {
         return *dp_table
             .get(&target_count)
             .unwrap()
             .get(&target_str)
             .unwrap();
     } else {
         return std::i32::MAX;
     }
 }
 
 fn get_keys_as_vec(dp_table: &HashMap<i32, HashMap<i32, i32>>, idx: i32) -> Vec<i32> {
     let mut c: Vec<i32> = vec![];
     for i in dp_table.get(&(idx - 1)).unwrap().keys() {
         c.push(*i);
     }
     return c;
 }
 
 //===========================//
 fn update_dp_table(
     dp_table: &mut HashMap<i32, HashMap<i32, HashMap<i32, i32>>>,
     curr_str: &i32,
     curr_cost: &i32,
     curr_count: &i32,
     value: i32,
 ) {
     if !dp_table.contains_key(curr_str) {
         dp_table.insert(*curr_str, HashMap::new());
     }
     if !dp_table[curr_str].contains_key(curr_cost) {
         dp_table
             .get_mut(curr_str)
             .unwrap()
             .insert(*curr_cost, HashMap::new());
     }
     if !dp_table[curr_str][curr_cost].contains_key(curr_count) {
         dp_table
             .get_mut(curr_str)
             .unwrap()
             .get_mut(curr_cost)
             .unwrap()
             .insert(*curr_cost, value);
     }
 }
 
 fn get_min_cost_top_down(
     metal_str: &[i32],
     metal_cost: &[i32],
     target_str: i32,
     target_count: i32,
     curr_str: &i32,
     curr_cost: &i32,
     curr_count: &i32,
     mut dp_table: &mut HashMap<i32, HashMap<i32, HashMap<i32, i32>>>,
 ) -> i32 {
     if dp_table.contains_key(curr_str)
         && dp_table[curr_str].contains_key(curr_cost)
         && dp_table[curr_str][curr_cost].contains_key(curr_count)
     {
         return dp_table[curr_str][curr_cost][curr_count];
     } else {
         if *curr_count == target_count {
             if *curr_str == target_str {
                 update_dp_table(&mut dp_table, curr_str, curr_cost, curr_count, *curr_cost);
                 return *curr_cost;
             } else {
                 update_dp_table(
                     &mut dp_table,
                     curr_str,
                     curr_cost,
                     curr_count,
                     std::i32::MAX,
                 );
                 return std::i32::MAX;
             }
         } else {
             let mut costs: Vec<i32> = vec![];
             for i in 0..metal_str.len() {
                 costs.push(get_min_cost_top_down(
                     metal_str,
                     metal_cost,
                     target_str,
                     target_count,
                     &(curr_str + metal_str[i]),
                     &(curr_cost + metal_cost[i]),
                     &(curr_count + 1),
                     dp_table,
                 ));
             }
             update_dp_table(
                 &mut dp_table,
                 curr_str,
                 curr_cost,
                 curr_count,
                 *costs.iter().min().unwrap(),
             );
             return *costs.iter().min().unwrap();
         }
     }
 }
 
 //===========================//
 fn get_min_cost(
     metal_str: &[i32],
     metal_cost: &[i32],
     target_str: i32,
     target_count: i32,
     curr_str: i32,
     curr_cost: i32,
     curr_count: i32,
 ) -> i32 {
     if curr_count == target_count {
         if curr_str == target_str {
             return curr_cost;
         } else {
             return std::i32::MAX;
         }
     } else {
         let mut costs: Vec<i32> = vec![];
         for i in 0..metal_str.len() {
             costs.push(get_min_cost(
                 metal_str,
                 metal_cost,
                 target_str,
                 target_count,
                 curr_str + metal_str[i],
                 curr_cost + metal_cost[i],
                 curr_count + 1,
             ));
         }
         return *costs.iter().min().unwrap();
     }
 }

~~~

:wq

Delta