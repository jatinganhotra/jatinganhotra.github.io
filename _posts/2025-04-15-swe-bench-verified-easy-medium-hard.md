---
layout: post
title: "Cracking the Code: How Difficult Are SWE-Bench-Verified Tasks Really?"
categories: [blog, swe-agents]
tags: [SWE-Bench_Verified]
---

When OpenAI released [SWE-Bench-Verified](https://openai.com/index/introducing-swe-bench-verified/), they included human annotations estimating how long each coding task would take an experienced software engineer to solve. This gives us a unique lens through which to analyze AI coding performance. As [I've discussed in previous analyses](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html), understanding the true complexity distribution of these tasks is critical to properly interpreting benchmark results.

### How Human Experts Judge Task Difficulty

OpenAI asked human annotators to estimate completion times for each task, assuming the engineer had "a few hours to familiarize themselves with the codebase." They used four time-based categories:

- **< 15 minutes**: Trivial changes like adding assertions to a function
- **15 minutes – 1 hour**: Small changes requiring some thought
- **1 – 4 hours**: Substantial rewrites affecting functions or multiple files
- **> 4 hours**: Esoteric issues requiring significant research and changing 100+ lines of code

While these estimates aren't used for dataset filtering, they provide valuable insight into the perceived difficulty distribution.

### Breaking Down SWE-Bench-Verified by Difficulty

When we analyze the 500 issues in SWE-Bench-Verified using these time-based difficulty metrics, the distribution is revealing:

| Difficulty Category | Count | Percentage |
|---------------------|-------|------------|
| `<15` minutes        | 194   | 38.80%     |
| `15` minutes - `1` hour | 261   | 52.20%     |
| `1-4` hours         | 42    | 8.40%      |
| `>4` hours           | 3     | 0.60%      |

**Key Insight**: The vast majority (91%) of issues are estimated to take less than an hour for a human expert to solve, with only a tiny fraction (0.60%) requiring more than 4 hours.

### Standardizing Difficulty Terminology

More recently, the "Multi-SWE-bench" paper ([Zhu et al., 2024](https://arxiv.org/abs/2504.02605)) simplified these four categories into three difficulty levels:

- **Easy**: ≤ 15 minutes (194 issues, 38.80%)
- **Medium**: 15 minutes – 1 hour (261 issues, 52.20%)
- **Hard**: ≥ 1 hour (45 issues, 9.00%)

This classification provides a clearer framework for evaluating model performance across difficulty levels.

### Quantifying Difficulty: Beyond Time Estimates

To gain deeper insights into what makes tasks difficult, we can examine objective metrics across difficulty levels:

|Difficulty|Count|Avg. #Files|Avg. #Hunks|Avg. #Lines|
|----------|-----|-----------|-----------|-----------|
|   Easy   | 194 |   1.03    |   1.37    |   5.04    |
|  Medium  | 261 |   1.28    |   2.48    |   14.1    |
|   Hard   | 45  |    2.0    |   6.82    |   55.78   |
| Overall  | 500 |   1.25    |   2.44    |   14.33   |

#### Key Observations

- **Scaling Relationship:** All metrics (files, hunks, lines) increase with difficulty level, but at different rates.
- **Lines Changed:** Shows the most dramatic increase from Easy to Hard (11x increase), highlighting that hard patches involve significantly more code changes.
- **Files Modified:** Shows a more modest increase (2x from Easy to Hard), suggesting that difficulty isn't just about the number of files.
- **Hunks:** Increases 5x from Easy to Hard, indicating more separate code blocks need modification in harder tasks.

### The Complexity of Single vs. Multi-File Issues

When we combine difficulty levels with file count, we see striking patterns:

| Difficulty | Total Issues | Single-file      | Multi-file       | Best Model % | Combined % |
|------------|--------------|------------------|------------------|--------------|------------|
| Easy       | 194          | 188 (96.91%)     | 6 (3.09%)        | 81.44%       | 95.36%     |
| Medium     | 261          | 221 (84.67%)     | 40 (15.33%)      | 62.07%       | 84.29%     |
| Hard       | 45           | 20 (44.44%)      | 25 (55.56%)      | 26.67%       | 42.22%     |
| **Total**  | **500**      | **429 (85.8%)**  | **71 (14.2%)**   | **65.4%**    | **84.8%**  |

**Critical Observation**: As difficulty increases, the proportion of multi-file issues rises dramatically—from just 3.09% of easy issues to 55.56% of hard issues. This suggests multi-file complexity as a significant factor in what makes programming challenges difficult.

Looking deeper at the metrics for single vs. multi-file tasks:

|File Count|Count|Avg. #Files|Avg. #Hunks|Avg. #Lines|
|----------|-----|-----------|-----------|-----------|
|  Single  | 429 |    1.0    |   1.78    |   10.05   |
|  Multi   | 71  |   2.73    |   6.42    |   40.23   |
| Overall  | 500 |   1.25    |   2.44    |   14.33   |


This table reveals that multi-file tasks require, on average:
- Nearly 4x as many code hunks (separate code blocks)
- 4x as many lines of code changed
- More complex edits across multiple files

These metrics substantiate what the performance data shows: multi-file tasks represent a significant complexity jump.

### Performance Across the Spectrum

| <div style="width:170px">Model</div> | <div style="width:110px">Overall (% Resolved)</div> | <div style="width:135px">Easy (194)<br>— 188 single<br>—  6 multi</div> | <div style="width:135px">Medium (261)<br>— 221 single<br>—  40 multi</div> | <div style="width:135px">Hard (45)<br>— 20 single<br>—  25 multi</div> |
|-------|-------------------------|------------------------------------------|---------------------------------------------|-----------------------------------------|
| **Combined Systems** |  |  |  |
| Combined Systems | 84.8% (424/500) | 95.36% (185/194)<br>— 180/188 single<br>— 5/6 multi | 84.29% (220/261)<br>— 190/221 single<br>— 30/40 multi | 42.22% (19/45)<br>— 10/20 single<br>— 9/25 multi |
| **Top Performers** |  |  |  |
| Augment Agent v0 | 65.4% (327/500) | 80.4% (156/194)<br>— 155/188 single<br>— 1/6 multi | 62.1% (162/261)<br>— 145/221 single<br>—  17/40 multi | 20.0% (9/45)<br>— 7/20 single<br>—  2/25 multi |
| W&B Programmer O1 crosscheck5 | 64.6% (323/500) | 77.3% (150/194)<br>— 149/188 single<br>—  1/6 multi | 62.1% (162/261)<br>— 144/221 single<br>—  18/40 multi | 24.4% (11/45)<br>— 9/20 single<br>—  2/25 multi |
| AgentScope | 63.4% (317/500) | 81.4% (158/194)<br>— 157/188 single<br>—  1/6 multi | 56.7% (148/261)<br>— 134/221 single<br>—  14/40 multi | 24.4% (11/45)<br>— 9/20 single<br>—  2/25 multi |
| **Mid-Range Systems** |  |  |  |
| Emergent E1 (v2024-12-23) | 57.2% (286/500) | 74.7% (145/194)<br>— 144/188 single<br>—  1/6 multi | 50.6% (132/261)<br>— 116/221 single<br>—  16/40 multi | 20.0% (9/45)<br>— 7/20 single<br>—  2/25 multi |
| Amazon Q Developer Agent (v20241202-dev) | 55.0% (275/500) | 72.2% (140/194)<br>— 139/188 single<br>—  1/6 multi | 49.4% (129/261)<br>— 115/221 single<br>—  14/40 multi | 13.3% (6/45)<br>— 6/20 single<br>—  0/25 multi |
| Agentless-1.5 + Claude-3.5 Sonnet (20241022) | 50.8% (254/500) | 70.6% (137/194)<br>— 137/188 single<br>—  0/6 multi | 42.5% (111/261)<br>— 101/221 single<br>—  10/40 multi | 13.3% (6/45)<br>— 3/20 single<br>—  3/25 multi |
| **Earlier Systems** |  |  |  |
| SWE-agent + Claude 3.5 Sonnet | 33.6% (168/500) | 47.9% (93/194)<br>— 92/188 single<br>—  1/6 multi | 28.0% (73/261)<br>— 67/221 single<br>—  6/40 multi | 4.4% (2/45)<br>— 1/20 single<br>—  1/25 multi |
| SWE-agent + GPT 4o (2024-05-13) | 23.2% (116/500) | 36.6% (71/194)<br>— 71/188 single<br>—  0/6 multi | 16.9% (44/261)<br>— 39/221 single<br>—  5/40 multi | 2.2% (1/45)<br>— 1/20 single<br>—  0/25 multi |
| SWE-agent + Claude 3 Opus | 18.2% (91/500) | 27.3% (53/194)<br>— 53/188 single<br>—  0/6 multi | 10.0% (26/261)<br>— 26/221 single<br>—  0/40 multi | 0.0% (0/45)<br>— 0/20 single<br>—  0/25 multi |

Examining representative systems across the performance spectrum reveals consistent patterns:

1. **The Easy Category Is Largely Solved**
   - Combined resolution rate: 95.36%
   - Even top individual systems solve ~80% of easy tasks
   - The remaining gap is closing with each new LLM release

2. **The Medium Category Shows Progress**
   - Combined resolution rate: 84.29%
   - Top systems solve 56-62% individually
   - Significant improvement from earlier systems (<30%)

3. **The Hard Category Remains Challenging**
   - Combined resolution rate: only 42.22%
   - Best individual systems solve just 20-25%
   - Multi-file hard issues are particularly difficult (only 9/25 solved by any system)

### Key Takeaways

This analysis reveals several important insights:

1. **The Easy-Hard Gap Persists**: Even top systems show a dramatic performance drop from easy (80%+) to hard tasks (20-25%).

2. **Multi-File Issues Present a Frontier**: The correlation between task difficulty and multi-file complexity is striking. As complexity increases along all metrics (files, hunks, lines), performance drops precipitously.

3. **Lines of Code as a Key Indicator**: The 11x increase in average lines changed from Easy to Hard tasks (5.04 → 55.78) appears to be the strongest predictor of task difficulty, far outpacing the increase in file count (2x) or hunks (5x).

4. **Combined Performance Ceiling**: The gap between individual and combined system performance suggests that different approaches excel at different tasks — no single system can yet solve all problem types.

5. **Hard Multi-File Issues Remain Unsolved**: With only 9/25 hard multi-file issues solved by any system, this represents a clear frontier for improvement.

6. **Scale of Changes Matters**: Multi-file tasks require 4x more lines of code and 4x more hunks than single-file tasks, highlighting that the scope of required changes significantly impacts task difficulty.


### Relating to the Reality Gap

As [I argued in my December 2024 post](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html), the distribution in SWE-Bench-Verified significantly underrepresents the complexity of real-world programming tasks. Comparing datasets reveals this stark contrast:

| Dataset | % issues >1 file |
|---------|------------------|
| SWE-Bench train set | **50.27%** |
| SWE-Bench test set | 24.89% |
| SWE-Bench-Verified test set | *14.2%* |

This discrepancy is significant and highlights a critical area for improvement in benchmark design. If we use file count as a complexity proxy, SWE-Bench-Verified presents a dramatically simplified view compared to real-world codebases, where approximately half of all issues require multi-file changes.

The data clearly shows that as we move from simple, localized fixes (Easy) to complex, multi-file, multi-hunk patches (Hard), AI performance drops dramatically. Future research should focus on improving coordination across multiple files and handling larger, more complex code changes that span multiple distinct code blocks.

This finding reinforces my previous analysis in [The Multi-File Frontier](https://jatinganhotra.github.io/blog/swe-agents/2025/01/05/swe-bench-mutliple-files.html), where I emphasized that truly robust AI programming systems must be capable of coordinating changes across multiple files in an interconnected codebase.

### Conclusion

While impressive progress has been made in solving isolated, single-file coding challenges, the frontier of multi-file, complex issues remains largely unexplored. For AI programming systems to truly match human capabilities in real-world software engineering, they must evolve beyond generating localized patches to understand the rich, interconnected nature of modern codebases.

> error ""
> Until benchmarks like SWE-Bench-Verified more accurately reflect the distribution of tasks in real-world development—particularly the proportion of multi-file changes—we should interpret leaderboard results with appropriate caution, recognizing they represent an optimistic view of AI's current programming capabilities.

