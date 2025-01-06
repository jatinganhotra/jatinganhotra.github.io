---
layout: post
title: "Do SWE-Agents Solve Multi-File Issues Like Humans? A Deep Dive into SWE-Bench Verified"
categories: [blog, swe-agents]
tags: [SWE-Bench_Verified]
---

The year 2024 was remarkable for SWE-Agents, as we celebrated significant advancements in system performance on our cherished [SWE-Bench](http://www.swebench.com) benchmark. This progress was especially notable on the [SWE-Bench Verified](https://openai.com/index/introducing-swe-bench-verified/) benchmark since its release.  

In a [previous post](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html), I discussed why `SWE-Bench Verified` may not accurately represent real-world SWE tasks, primarily due to the low proportion of issues requiring code changes across multiple files. If you haven’t read that post yet, I recommend doing so before continuing with this one. 
<!-- 2024 was a great year for SWE-Agents and we have celebrated the rapid advance in performance of systems on our beloved [SWE-Bench](http://www.swebench.com) benchmark, especially on the [SWE-Bench Verified](https://openai.com/index/introducing-swe-bench-verified/) benchmark since it was released.

In a [prior post](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html), I highlighted why `SWE-Bench Verified` is not a valid representation of real-world SWE tasks given the low percentage of issues which require code changes across multiple files. If you haven't done the post, I suggest reading it first before continuing with this post. -->

<!-- From the previous post, we know that the `SWE-Bench_Verified` dataset (500 instances) can be divided in 2 buckets:
- instances which require code changes in a single-file = `429/500` = `85.8%`, and
- instances which require code changes in multiple files (> 1) = `71/500` = `14.2%`

and we know that the performance of the top-10 systems <code>drops significantly</code> for the multiple files category. -->

As highlighted in the previous post, the `SWE-Bench_Verified` dataset, consisting of 500 instances, can be categorized into two distinct buckets:  

- **Single-file changes**: 429/500 instances (**85.8%**)  
- **Multiple-file changes**: 71/500 instances (**14.2%**)  

Furthermore, it is evident that the performance of the top-10 systems **drops significantly** for tasks requiring changes across multiple files, underscoring a critical area for improvement.

|                      Model                      |Overall %Resolved (count/500)|Multi-file %Resolved (count/71)|
|-------------------------------------------------|-----------------------------|-------------------------|
|  Amazon Q Developer Agent (v20241202-dev) |          55.0 (275)         |       21.13 (15)        |
|                  devlo                 |          54.2 (271)         |       19.72 (14)        |
|  OpenHands + CodeAct v2.1 (claude-3-5-sonnet-20241022) |          53.0 (265)         |       25.35 (18)        |
|               Engine Labs (2024-11-25)               |          51.8 (259)         |       18.31 (13)        |
|Agentless-1.5 + Claude-3.5 Sonnet (20241022)|          50.8 (254)         |       18.31 (13)        |
|                 Solver (2024-10-28)                 |          50.0 (250)         |       18.31 (13)        |
|           Bytedance MarsCode Agent           |          50.0 (250)         |       18.31 (13)        |
|               nFactorial (2024-11-05)               |          49.2 (246)         |       14.08 (10)        |
|     Tools + Claude 3.5 Sonnet (2024-10-22)    |          49.0 (245)         |        11.27 (8)        |
|             Composio SWE-Kit (2024-10-25)            |          48.6 (243)         |        8.45 (6)         |


<!-- In this post, let's dive deeper into the performance of the top-10 systems, on the multiple files subset of `SWE-Bench Verified`, from a different point of view:

- Is there a difference in how a human software engineer (SWE) solved the issue v/s how our current state-of-the-art SWE-Agents?
- do our SWE-Agents modify multiple files to resolve the issue, since these instances require code changes in multiple files (considering the human-generated patch as our golden truth)?

This is the question we are asking today!!! -->
<hr/>

In this post, we’ll take a closer look at the performance of the top-10 systems on the **multiple files subset** of `SWE-Bench Verified` from a fresh perspective:  

1. How does the approach of our current state-of-the-art SWE-Agents compare to that of a human software engineer (SWE) when solving these issues?  
2. Do our SWE-Agents appropriately modify multiple files to resolve the issue, given that these instances inherently require changes across multiple files (using the human-generated patch as the golden standard)?  

This is the question we aim to explore today! 🚀

<!-- Let's begin by identifying how many instances out of 71 instances are resolved by at least one of the top-10 systems?

If we compile the stats from the table above, we get:

- **Multiple-file Instances** (_requiring changes across multiple files_): `71/500`
- Multiple-file Instances resolved by **at-least one system**: `29/71`
- Multiple-file Instances resolved by **at-least one system** _with_ **single-file changes**: `20/29`

This is quite surprising. There are `20` instances which

- required changes across multiple files
- were marked as resolved (i.e. passed both `PASS_TO_PASS` and `FAIL_TO_PASS` tests in the evaluation) by at least one system (out of top-10)
- resolved by modifying only 1 file. -->


Let’s start by analyzing how many of the 71 **multiple-file instances** were resolved by at least one of the top-10 systems. Based on the compiled statistics, we get:  

- Multiple-file Instances (_requiring changes across multiple files_): `71/500`
- Multiple-file Instances resolved by **at least one system**: `29/71`
- Multiple-file Instances resolved by **at least one system** _with_ **single-file changes**: `20/29`

This observation is quite surprising. There are **20 instances** that:  

1. **Required changes across multiple files**, as indicated by the human-generated patch.  
2. Were marked as **resolved** (i.e., passed both `PASS_TO_PASS` and `FAIL_TO_PASS` tests in the evaluation) by at least one of the top-10 systems.  
3. Were resolved by modifying only **one file**, contradicting the multi-file nature of the issue.  

This discrepancy raises important questions about how the systems achieve these resolutions and the robustness of the evaluation process. 😳

<!-- Disclaimer : If you're working on the SWE-Bench benchmark, do not try to reverse engineer the anonymized instance id given the information provided about the gold patch files. Further, do not open the original pull request for any instance on Github or the corresponding gold patch in the dataset to keep the hidden test hidden for ethical purposes. The instance ids are purposefully hidden towards this goal and the tabular analysis lists down file names in the model generated patches to identify any clear pattern, while keeping the actual instance ids anonymized as well as the patch hidden.  -->

> error "Disclaimer"
> If you are working on the SWE-Bench benchmark, please refrain from attempting to reverse engineer the anonymized instance IDs using the information provided about the gold patch files. Additionally, do not access the original pull requests on GitHub or the corresponding gold patches in the dataset. This ensures that hidden tests remain protected, maintaining ethical standards.  
> 
> 
> To achieve this:  
> - Instance IDs are purposefully anonymized.  
> - The tabular analysis focuses only on listing the file names in the model-generated patches to identify any patterns, while keeping both the instance IDs and the patches hidden.  
>
> This approach safeguards the integrity of the benchmark while facilitating responsible and ethical analysis.
>

<!-- ## Disclaimer

**Disclaimer:** If you are working on the SWE-Bench benchmark, refrain from attempting to reverse engineer the anonymized instance IDs using the information provided about the gold patch files. Additionally, do not access the original pull requests on GitHub or the corresponding gold patches in the dataset. This ensures that hidden tests remain protected, maintaining ethical standards.  

To achieve this:  
- Instance IDs are purposefully anonymized.  
- The tabular analysis focuses only on listing the file names in the model-generated patches to identify any patterns, while keeping both the instance IDs and the patches hidden.  

This approach safeguards the integrity of the benchmark while facilitating responsible and ethical analysis. -->

<!-- ## Analysis

Let's dive deeper into these 20 instances. For each instance, the following information is provided:
- instance_id (_anonymized_)
- resolved by _`n`_/10 systems (top-10 systems)
- number of files `K` in gold patch
    - `k1 ⊆ K` modified by _<g>all</g>_ _n_/10 systems
    - `k2 ⊆ K` modified by _<o>some</o>_ systems (a subset of _n_/10)
    - `k3 ⊆ K` _<r>not</r>_ modified by _<r>any</r>_ systems

Click on the instance_id to view additional details, such as:
- the file names from the gold patch (color-encoded)
    - _<g>green</g>_, _<o>orange</o>_ and _<r>red</r>_ show the 3 categories specified above
- the file names in the model generated patches for each of the _n_/10 systems which resolved the instance -->

<style>
r { color: Red }
o { color: Orange }
g { color: Green }
</style>

## Analysis  

Let’s take a closer look at these 20 instances. For each instance, the following information is provided:  

1. **Instance ID** (_anonymized_).  
2. Number of systems (_out of the top-10_) that resolved the instance: _`n`_/10.  
3. Number of files **`K`** in the gold patch, further divided into:  
   - **`k1 ⊆ K`**: Files modified by _<g>all</g>_ _`n`_/10 systems.  
   - **`k2 ⊆ K`**: Files modified by _<o>some</o>_ systems (a subset of _`n`_/10).  
   - **`k3 ⊆ K`**: Files _<r>not</r>_ modified by _<r>any</r>_ systems.  

By clicking on the anonymized **Instance ID**, you can view additional details, such as:  

- The file names in the **gold patch** (_color-encoded_):  
  - _<g>Green</g>_: Files modified by all systems.  
  - _<o>Orange</o>_: Files modified by some systems.  
  - _<r>Red</r>_: Files not modified by any systems.  
- The file names in the **model-generated patches** for each of the _`n`_/10 systems that resolved the instance.  

<!-- Based on the additional details, we can divide the 20 instances in 2 categories:

<hr/>

### Category 1: No file from gold patch was modified
The instances below have model generated patches, where _none_ of the files from gold patch were modified, but a different file was modified by the systems which resolved the issue.


<hr/>

### Category 2: At least 1 file from gold patch was not modified
The instances below have model generated patches, where at least 1 file from gold patch was not modified by the systems which resolved the issue. -->

Based on the additional details, the 20 instances can be categorized as follows:  

---

### Category 1: _No file from the gold patch was modified_  
In these instances, the model-generated patches resolved the issue by modifying files that were not part of the gold patch, while none of the files in the gold patch were altered.  

<i>Note: Click on the anonymized Instance ID to view additional details</i>

<details>
<summary markdown='span'>scikit-learn__scikit-learn-x5x0x<br/>- resolved by `4/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files <i>not</i> modified by <i>any</i> systems</summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_1_instance_1 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-1x9x8<br/>- resolved by `1/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files <i>not</i> modified by <i>any</i> systems</summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_1_instance_2 | markdownify }}

</details>

---

### Category 2: _At least 1 file from the gold patch was not modified_  
In these instances, the model-generated patches resolved the issue, but at least one file from the gold patch was left unmodified by the systems.  

<i>Note: Click on the anonymized Instance ID to view additional details</i>

<details>
<summary markdown='span'>django__django-xxxx3<br/>- resolved by `3/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `3` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by <i>some</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_1 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-x1xxx<br/>- resolved by `7/10` systems<br/>- `5` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `7` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by <i>some</i> systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `3` files <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_2 | markdownify }}

</details>

<details>
<summary markdown='span'>django__django-xxxx5<br/>- resolved by `10/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `10` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_3 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-xxx25<br/>- resolved by `1/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `1` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_4 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-xxxx1<br/>- resolved by `4/10` systems<br/>- `4` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `3` files modified by <i>some</i> systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_5 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-1xxxx<br/>- resolved by `10/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by <i>some</i> systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_6 | markdownify }}

</details>


<details>
<summary markdown='span'>django__django-1x0xx<br/>- resolved by `4/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by <i>some</i> systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_7 | markdownify }}

</details>

<details>
<summary markdown='span'>matplotlib__matplotlib-2xxxx<br/>- resolved by `5/10` systems<br/>- `3` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `5` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files modified by <i>some</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_8 | markdownify }}

</details>


<details>
<summary markdown='span'>pydata__xarray-xx9x<br/>- resolved by `4/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `4` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_9 | markdownify }}

</details>


<details>
<summary markdown='span'>pydata__xarray-xx0x<br/>- resolved by `9/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files modified by <i>some</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_10 | markdownify }}

</details>


<details>
<summary markdown='span'>pydata__xarray-xxx3<br/>- resolved by `5/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `5` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_11 | markdownify }}

</details>


<details>
<summary markdown='span'>pylint-dev__pylint-x5xx<br/>- resolved by `4/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `4` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_12 | markdownify }}

</details>


<details>
<summary markdown='span'>pylint-dev__pylint-x89x<br/>- resolved by `2/10` systems<br/>- `3` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `2` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_13 | markdownify }}

</details>


<details>
<summary markdown='span'>pytest-dev__pytest-xx9x<br/>- resolved by `10/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `10` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_14 | markdownify }}

</details>


<details>
<summary markdown='span'>scikit-learn__scikit-learn-xx6xx<br/>- resolved by `4/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `4` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_15 | markdownify }}

</details>


<details>
<summary markdown='span'>sphinx-doc__sphinx-xx2x<br/>- resolved by `6/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `2` files modified by <i>some</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_16 | markdownify }}

</details>


<details>
<summary markdown='span'>sphinx-doc__sphinx-x59x<br/>- resolved by `3/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `3` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_17 | markdownify }}

</details>


<details>
<summary markdown='span'>sympy__sympy-xxx1x<br/>- resolved by `1/10` systems<br/>- `2` files in gold patch<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file modified by all `1` systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;- `1` file <i>not</i> modified by <i>any</i> systems<br/></summary>

{% include swe-bench-mutliple-files-parts/details.html %}
{{ details_category_2_instance_18 | markdownify }}

</details>

---

<!-- This detailed breakdown helps identify patterns and differences in how the systems resolved these complex multi-file issues.

The detailed analysis is intended only to showcase that our SWE-Agents behave differently than human SWEs and 2. the benchmark does not specifically force the SWE-Agents to ensure that the changes are in the correct place for a long term code maintenance. If these fixes were submitted as PRs, some of these model generated patches would need to be modified so the appropriate files are modified for better maintainable code. -->

<!-- ## Conclusion

Based on our analysis, we can observe that there are discrepancies between human and model approaches. 

Our detailed breakdown serves two key purposes:  

1. Highlights how SWE-Agents approach these complex multi-file issues differently compared to human SWEs.  
2. Emphasizes that the benchmark does not explicitly require SWE-Agents to ensure changes are made in the correct locations, which may be critical for long-term code maintenance.  

If these model-generated patches were submitted as pull requests, some (many?) would likely require adjustments to ensure that the appropriate files are modified, to ensure more maintainable and robust code in the long-term. 

Overall, our analysis reveals a gap between benchmark success and real-world software engineering best practices. -->

The above analysis reveals notable discrepancies between the approaches of human software engineers (SWEs) and model-based SWE-Agents when resolving complex multi-file issues.  

## Key Takeaways from the Analysis  

1. **Differences in Problem-Solving Approaches:**  
   SWE-Agents handle multi-file issues differently compared to human SWEs, often focusing on resolving issues without strictly adhering to the gold standard of modifying the correct files.  
   <!-- _NOTE_: It is plausible that the model generated patches from SWE-Agents might be better than the ones from human SWEs; but that remains to be determined after a review from the Subject Matter Expert (Github repo maintainer) for the given library. -->
    
    _NOTE_: It is plausible that the model-generated patches from SWE-Agents might, in some cases, surpass those created by human SWEs in terms of quality or efficiency.
    - However, this hypothesis remains to be validated through a thorough review by a Subject Matter Expert (such as the GitHub repository maintainer) for the specific library or codebase.  
    - Such an expert review could provide critical insights into the suitability and maintainability of the model-generated patches, helping to assess their long-term value compared to human contributions.

2. **Benchmark Limitations:**  
   The benchmark does not enforce requirements for SWE-Agents to make changes in the appropriate locations, which is crucial for ensuring maintainable and robust code over the long term.  

## Implications  
If these model-generated patches were submitted as pull requests, many would likely need significant adjustments to modify the appropriate files. This step is essential to align with real-world software engineering practices and to produce code that is easier to maintain and extend.  

## Conclusion  
The analysis highlights a **gap between achieving benchmark success and adhering to real-world software engineering best practices**, underscoring the need for benchmarks that better reflect practical requirements and long-term code maintainability.

<!-- * Do not remove this line (it will not be displayed)
{:toc} -->

<!-- useful link for details markdown: https://github.com/jekyll/jekyll/issues/9297#issuecomment-2137865682 -->

<!-- escape __init__.py correctly in table
https://stackoverflow.com/questions/33705722/escaping-character-sequences-with-jekyll-and-liquid -->

<!-- TODO: Calculate the new stats for full SWE-Bench test set.
if the blog gets too big, may just stop at the first level of analysis.
Comments: Post is too big already, skipping. -->

<!-- 2-column layout
<style>
.grid {
  display: flex;
 }
.col-1-2 {
  flex: 1;
}
.cole-1-2:last-child {
  margin-left: 20px;
}
</style>
<div class="grid .col-1-2">
</div> -->
