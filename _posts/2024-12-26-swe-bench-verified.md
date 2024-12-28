---
layout: post
title: "SWE-Bench Verified ⊊ real-world SWE tasks"
categories: [SWE-Bench, SWE-Bench_Verified, SWE]
---

The title "SWE-Bench Verified `⊊` real-world SWE tasks", conveys 2 points:
- SWE-Bench Verified `!=` real-world SWE tasks, and
- SWE-Bench Verified `⊆` real-world SWE tasks (i.e. a subset)

Let's dive in.

The year 2024 has been a thrilling time for SWE Agents, with the SWE-Bench leaderboard buzzing with activity throughout the year.
SWE-Bench [[1]](#1) is a well known benchmark in the community for software engineering. Some notable events are:
- Devin[[2]](#2) was announced on March 12, 2024 and set a new state-of-the-art, on a random 25% subset of the dataset, and
- release of the updated [Claude 3.5 Sonnet](https://x.com/OfirPress/status/1858567863788769518) model setting a [new state-of-the-art](https://www.anthropic.com/research/swe-bench-sonnet) on SWE-Bench_Verified on Oct 30, 2024.

## SWE-Bench Introduction

There are 3 benchmarks when we talk about SWE-Bench:
- SWE-Bench[[3]](#3)
- SWE-Bench_Lite[[4]](#4)
- SWE-bench_Verified[[5]](#5)

The SWE-Bench authors[[6]](#6) introduce `SWE-Bench` as:
> an evaluation framework consisting of 2,294 software engineering problems drawn from real GitHub issues and corresponding pull requests across 12 popular Python repositories. Given a codebase along with a description of an issue to be resolved, a language model is tasked with editing the codebase to address the issue.

Since the initial performance on the full SWE-Bench was quite low, the authors created a Lite subset of 300 instances, `SWE-Bench_Lite`, that have been sampled to be more self-contained, with a focus on evaluating functional bug fixes. Full details of the Lite split and filtering details are included in Appendix A.7 in the paper.

On August 13, 2024[[7]](#7), OpenAI released a human-validated subset of 500 instances from SWE-bench, `SWE-bench_Verified`, that
> more reliably evaluates AI models’ ability to solve real-world software issues.

For additional details on the SWE-Bench_Verified benchmark, please refer to [[7]](#7).

## Leaderboard

Since its release, SWE-bench_Verified has become the de-facto benchmark given OpenAI released it after human-validation.
The current leaderboard for SWE-bench_Verified (top-10 systems) looks like (as of Dec 25, 2024):

| Model                                                | % Resolved |
|------------------------------------------------------|------------|
| Amazon Q Developer Agent (v20241202-dev)      | 55.00      |
| devlo                                         | 54.20      |
| OpenHands + CodeAct v2.1 (claude-3-5-sonnet-20241022) | 53.00      |
| Engine Labs (2024-11-25)                         | 51.80      |
| Agentless-1.5 + Claude-3.5 Sonnet (20241022)  | 50.80      |
| Solver (2024-10-28)                                 | 50.00      |
| Bytedance MarsCode Agent                         | 50.00      |
| nFactorial (2024-11-05)                          | 49.20      |
| Tools + Claude 3.5 Sonnet (2024-10-22)             | 49.00      |
| Composio SWE-Kit (2024-10-25)                | 48.60      |

As we can observe, the top-10 systems are quite close with only ~1% difference between each rank.

## One Issue with the SWE-Bench_Verified Leaderboard

However, `SWE-Bench` was created with the following intent:

> Resolving issues in SWE-bench frequently requires _**understanding and coordinating changes across multiple functions, classes, and even files simultaneously**_, calling for models to interact with execution environments, process extremely long contexts and perform complex reasoning that goes far beyond traditional code generation tasks. 

Let's take a deeper dive into the results while keeping in mind the following statement:

> understanding and coordinating changes across multiple functions, classes, and even files simultaneously

If we look at the number of files per Github issue which must be modified to resolve it for `SWE-Bench_Verified`, we get:

|   # files  | # instances |
|------------|-------------|
|     1      |     429     |
|     2      |      49     |
|     3      |      12     |
|     4      |       7     |
|     5      |       1     |
|     6      |       1     |
|    7-20    |       0     |
|     21     |       1     |
|     21+    |       0     |

For simplicity, let's divide the `SWE-Bench_Verified` dataset in 2 buckets:
- instances which require code changes in a single-file = `429/500` = `85.8%`, and
- instances which require code changes in multiple files (> 1) = `71/500` = `14.2%`

If we divide the results between these 2 buckets, we get:

|                      Model                      |Overall %Resolved (count/500)|Single-file %Resolved (count/429)|Multi-file %Resolved (count/71)|
|-------------------------------------------------|-----------------------------|----------------------------|-------------------------|
|  Amazon Q Developer Agent (v20241202-dev) |          55.0 (275)         |         60.61 (260)        |       21.13 (15)        |
|                  devlo                 |          54.2 (271)         |         59.91 (257)        |       19.72 (14)        |
|  OpenHands + CodeAct v2.1 (claude-3-5-sonnet-20241022) |          53.0 (265)         |         57.58 (247)        |       25.35 (18)        |
|               Engine Labs (2024-11-25)               |          51.8 (259)         |         57.34 (246)        |       18.31 (13)        |
|Agentless-1.5 + Claude-3.5 Sonnet (20241022)|          50.8 (254)         |         56.18 (241)        |       18.31 (13)        |
|                 Solver (2024-10-28)                 |          50.0 (250)         |         55.24 (237)        |       18.31 (13)        |
|           Bytedance MarsCode Agent           |          50.0 (250)         |         55.24 (237)        |       18.31 (13)        |
|               nFactorial (2024-11-05)               |          49.2 (246)         |         55.01 (236)        |       14.08 (10)        |
|     Tools + Claude 3.5 Sonnet (2024-10-22)    |          49.0 (245)         |         55.24 (237)        |        11.27 (8)        |
|             Composio SWE-Kit (2024-10-25)            |          48.6 (243)         |         55.24 (237)        |        8.45 (6)         |

The performance of the top-10 systems drops significantly for the multiple files category.

## SWE-Bench test set

If we look at the distribution of number of files per Github issue which must be modified to resolve it for the (full) SWE-Bench test set, we get:

<!-- |   # files   | # instances |
|-------------|-------------|
|     1       |     1723    |
|     2       |     308     |
|     3       |     108     |
|     4       |     50      |
|     5       |     31      |
|     6       |     18      |
|     7       |     14      |
|     8       |     7       |
|     9       |     7       |
|     10      |     3       |
|     11      |     1       |
|     12      |     3       |
|     13      |     1       |
|     14      |     2       |
|     15      |     1       |
|     16      |     4       |
|     17      |     4       |
|     18      |     3       |
|     21      |     2       |
|     22      |     1       |
|     23      |     2       |
|     31      |     1       | -->

|   # files  | # instances |
|------------|-------------|
|     1      |     1723     |
|     2      |     308     |
|     3      |     108     |
|     4      |     50     |
|     5      |     31     |
|     6      |     18     |
|     7      |     14     |
|     8      |     7     |
|     9      |     7     |
|     10      |     3     |
|     11+      |     25     |

For simplicity, let's divide the SWE-Bench test set in the same 2 buckets:
- instances which require code changes in a single-file = `1723/2294` = `75.11%`, and
- instances which require code changes in multiple files (> 1) = `571/2294` = `24.89%`

If we look at the leaderboard for SWE-Bench full test set (top-5 systems), as of Dec 25, 2024, for the 2 buckets, we get:

| Model                                              | Overall %Resolved (count/2294) | Single-file %Resolved (count/1723)       |  Multi-file %Resolved (count/571)           |
|----------------------------------------------------|------------|---------------------|-------------|
| OpenHands + CodeAct v2.1 (claude-3-5-sonnet-20241022) | 29.38 (674)     |         34.94 (602)         |         12.61 (72)        |
| AutoCodeRover-v2.0 (Claude-3.5-Sonnet-20241022) | 24.89 (571)     |         30.12 (519)         |         9.11 (52)         |
| Honeycomb                                       | 22.06 (506)     |          26.7 (460)         |         8.06 (46)         |
| Amazon Q Developer Agent (v20240719-dev)          | 19.75 (453)     |         24.38 (420)         |         5.78 (33)         |
| Factory Code Droid                                | 19.27 (442)     |         23.74 (409)         |         5.78 (33)         |

We observe the same trend. The performance of the top-5 systems drops significantly for the multiple files category.

Perhaps we don't need to worry about the multiple files category, if we assume that the 75%-25% ratio in the SWE-Bench test set is reflective of the real-world Github issues.
To validate our assumption, let's take a look at the SWE-Bench train set.

## SWE-Bench train set

If we look at the distribution of number of files per Github issue which must be modified to resolve it for the full `SWE-Bench train` set, we get:

<!-- |   # files  | # instances |
|------------|-------------|
|     1      |     8783     |
|     2      |     4023     |
|     3      |     1641     |
|     4      |     836     |
|     5      |     564     |
|     6      |     390     |
|     7      |     253     |
|     8      |     190     |
|     9      |     128     |
|     10      |     104     |
|     11      |     95     |
|     12      |     82     |
|     13      |     62     |
|     14      |     46     |
|     15      |     40     |
|     16      |     47     |
|     17      |     32     |
|     18      |     26     |
|     19      |     19     |
|     20      |     20     |
|     21      |     28     |
|     22      |     21     |
|     23      |     10     |
|     24      |     8     |
|     25      |     15     |
|     26      |     9     |
|     27      |     8     |
|     28      |     11     |
|     29      |     7     |
|     30      |     10     |
|     31      |     6     |
|     32      |     6     |
|     33      |     4     |
|     34      |     9     |
|     35      |     4     |
|     36      |     10     |
|     37      |     9     |
|     38      |     3     |
|     39      |     9     |
|     40      |     1     |
|     41      |     2     |
|     43      |     1     |
|     44      |     5     |
|     45      |     3     |
|     46      |     1     |
|     47      |     2     |
|     48      |     2     |
|     49      |     4     |
|     50      |     1     |
|     51      |     1     |
|     52      |     5     |
|     54      |     4     |
|     55      |     3     |
|     57      |     3     |
|     58      |     2     |
|     59      |     2     |
|     60      |     1     |
|     63      |     1     |
|     64      |     2     |
|     65      |     2     |
|     66      |     1     |
|     69      |     1     |
|     70      |     1     |
|     71      |     1     |
|     74      |     1     |
|     76      |     2     |
|     78      |     3     |
|     79      |     2     |
|     80      |     1     |
|     81      |     1     |
|     82      |     2     |
|     87      |     1     |
|     88      |     2     |
|     89      |     1     |
|     91      |     1     |
|     98      |     1     |
|     99      |     1     |
|     101      |     1     |
|     102      |     1     |
|     110      |     1     |
|     111      |     1     |
|     112      |     1     |
|     114      |     1     |
|     125      |     1     |
|     126      |     1     |
|     127      |     1     |
|     130      |     1     |
|     134      |     1     |
|     135      |     1     |
|     138      |     1     |
|     161      |     1     |
|     178      |     1     |
|     198      |     1     |
|     204      |     1     |
|     264      |     1     |
|     265      |     1     |
|     292      |     1     |
|     328      |     1     | -->

|   # files  | # instances |
|------------|-------------|
|     1      |     8783     |
|     2      |     4023     |
|     3      |     1641     |
|     4      |     836     |
|     5      |     564     |
|     6      |     390     |
|     7      |     253     |
|     8      |     190     |
|     9      |     128     |
|     10      |     104     |
|     11-20      |     469     |
|     21-30      |     127     |
|     31-100      |     131     |
|     101-200      |     16     |
|     201+      |     5     |

There are 19008 instances in the train set and unidiff [[8]](#8) parser (v0.7.5) was unable to parse 1348 instances, so we have the statistics for 17660 instances.
But, the above distribution paints an entirely different picture.

For simplicity, let's divide the `SWE-Bench train` set in the same 2 buckets as before:
- instances which require code changes in a single-file = `8783/17660` = `49.73%`, and
- instances which require code changes in multiple files (> 1) = `8877/17660` = `50.27%`

If we assume that the instances which require > than 5 files are not representative of most day-to-day SWE tasks and exclude them, we get:

|   # files  | # instances |
|------------|-------------|
|     1      |     8783     |
|     2      |     4023     |
|     3      |     1641     |
|     4      |     836     |
|     5      |     564     |

The above subset of instances which require <= than 5 files gives us 15847 instances. Dividing the above subset of SWE-Bench train set in the same 2 buckets as before:
- instances which require code changes in a single-file = `8783/15847` = `55.42%`, and
- instances which require code changes in multiple files (> 1) = `7064/15847` = `44.58%`

## Conclusion

Aggregating the above statistics for the percentage of Github issues which require editing >1 file, we get:

|              Dataset               |   % issues >1 file |
|------------------------------------|--------------------|
|  SWE-Bench train set               |     50.27          |
|  SWE-Bench train set (<= 5 files)  |     44.58          |
|  SWE-Bench test set                |     24.89          |
|  SWE-Bench_Verified test set       |     14.2           |

If we consider the SWE-Bench train set as a representative of real-world, then by the same definition, we can not consider the SWE-Bench_Verified test set as the true representative of our progress on SWE tasks given the much lower percentage of issues which require changes across >1 file for resolution.

## Takeaways
- We have made tremendous progress on the `SWE-Bench_Verified` task, but there's much more to be done when it comes to building systems which are good at  _**understanding and coordinating changes across multiple functions, classes, and even files simultaneously**_. Perhaps the current systems (and models) are really good; and we need to curate better evaluation benchmarks. 

- The leaderboard should break down the performance of each new entry across both buckets (single file and multiple files), in addition to the overall benchmark performance.

- We need a larger human-validated dataset which captures changes across multiple files, given 71 instances (SWE-Bench_Verified) is a very small number (even smaller than the HumanEval benchmark of 164 hand-crafted programming challenges, designed to evaluate an LLM’s code generation capabilities).


## References
<a id="1">[1]</a>
http://www.swebench.com

<a id="2">[2]</a>
https://www.cognition.ai/blog/introducing-devin

<a id="3">[3]</a>
https://huggingface.co/datasets/princeton-nlp/SWE-bench

<a id="4">[4]</a>
https://huggingface.co/datasets/princeton-nlp/SWE-bench_Lite

<a id="5">[5]</a>
https://huggingface.co/datasets/princeton-nlp/SWE-bench_Verified

<a id="6">[6]</a>
SWE-bench: Can Language Models Resolve Real-world Github Issues?
Jimenez, Carlos E and Yang, John and Wettig, Alexander and Yao, Shunyu and Pei, Kexin and Press, Ofir and Narasimhan, Karthik R
The Twelfth International Conference on Learning Representations

<a id="7">[7]</a>
https://openai.com/index/introducing-swe-bench-verified/

<a id="8">[8]</a>
https://pypi.org/project/unidiff/

