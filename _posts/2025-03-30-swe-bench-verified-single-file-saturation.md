---
layout: post
title: "The Multi-File Frontier: Why SWE-Bench Verified Doesn't Reflect Real-World Programming Challenges"
categories: [blog, swe-agents]
tags: [SWE-Bench_Verified]
---

The SWE-Bench-Verified leaderboard has witnessed remarkable progress with submissions from leading AI companies, research laboratories, and emerging startups. As [I highlighted in my previous analyses](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html), this benchmark has become a focal point for evaluating AI's capabilities in resolving software engineering tasks, but with important caveats about its real-world applicability.

## Understanding the Dataset Distribution

SWE-Bench-Verified's 500 instances fall into two distinct categories:

- **Single-file changes**: 429 instances (85.8%)
- **Multiple-file changes**: 71 instances (14.2%)

This distribution reveals a critical insight: the performance of top systems deteriorates significantly when tackling tasks requiring changes across multiple files—exposing a fundamental limitation in current AI programming approaches.

## The Leap Forward in 2025

Since January 2025, we've seen substantial progress. The previous leader, Amazon Q Developer Agent, has been surpassed by twelve new systems, with "Augment Agent v0" now claiming the top position.

### Performance Across the Spectrum

Below is a representative sample of systems across the performance spectrum, highlighting the consistent gap between single-file and multi-file performance:

| Model | Overall %Resolved | Single-file %Resolved | Multi-file %Resolved |
|-------|-------------------|----------------------|---------------------|
| **Top Performers** |  |  |  |
| Augment Agent v0 | 65.4% (327) | 71.56% (307) | 28.17% (20) |
| W&B Programmer O1 crosscheck5 | 64.6% (323) | 70.4% (302) | 29.58% (21) |
| AgentScope | 63.4% (317) | 69.93% (300) | 23.94% (17) |
| **Mid-Range Systems** |  |  |  |
| Emergent E1 (v2024-12-23) | 57.2% (286) | 62.24% (267) | 26.76% (19) |
| Amazon Q Developer Agent (v20241202-dev) | 55.0% (275) | 60.61% (260) | 21.13% (15) |
| Agentless-1.5 + Claude-3.5 Sonnet | 50.8% (254) | 56.18% (241) | 18.31% (13) |
| **Earlier Systems** |  |  |  |
| SWE-agent + Claude 3.5 Sonnet | 33.6% (168) | 37.3% (160) | 11.27% (8) |
| SWE-agent + GPT 4o (2024-05-13) | 23.2% (116) | 25.87% (111) | 7.04% (5) |
| SWE-agent + Claude 3 Opus | 18.2% (91) | 21.21% (91) | 0.0% (0) |
| **Baseline Approaches** |  |  |  |
| RAG + Claude 3 Opus | 7.0% (35) | 8.16% (35) | 0.0% (0) |
| RAG + SWE-Llama 13B | 1.2% (6) | 1.17% (5) | 1.41% (1) |
| RAG + ChatGPT 3.5 | 0.4% (2) | 0.47% (2) | 0.0% (0) |

*Note: The full table includes 64 systems. This shortened version highlights representative systems across the performance spectrum.*

The current leader has pushed performance boundaries significantly:
- **Overall resolution**: Improved from 55.0% to 65.4%
- **Single-file resolution**: Increased from 60.61% to 71.56%
- **Multi-file resolution**: Advanced from 21.13% to 28.17%

While these gains are impressive, they're predominantly in simpler, single-file scenarios. Even the best systems struggle with multi-file issues, with no system exceeding 30% resolution rate for these more complex problems.

## A Collective View: The Upper Bound

When we combine the capabilities of all top systems, an interesting picture emerges:

- **Single-file issues**: ~90% resolution rate (386/429)
- **Multi-file issues**: Only ~54% resolution rate (38/71)

This reveals that:

1. Single-file challenges are approaching saturation—the collective intelligence of these systems resolves nearly all such issues.

2. Multi-file problems remain a frontier challenge—even with all systems combined, nearly half remain unsolved.

## The Reality Gap: Benchmark vs. Real-World

As [I argued in my December 2024 post](https://jatinganhotra.github.io/blog/swe-agents/2024/12/26/swe-bench-verified.html) titled "SWE-Bench Verified ⊊ real-world SWE tasks," the distribution in SWE-Bench-Verified significantly underrepresents the complexity of real-world programming tasks. Comparing datasets reveals this stark contrast:

| Dataset | % issues >1 file |
|---------|------------------|
| SWE-Bench train set | **50.27%** |
| SWE-Bench test set | 24.89% |
| SWE-Bench-Verified test set | *14.2%* |

This discrepancy is significant and highlights a critical area for improvement in benchmark design. If we use file count as a complexity proxy, SWE-Bench-Verified presents a dramatically simplified view compared to enterprise codebases, where approximately half of all issues require multi-file changes.

## Expert Voices Agree on the Benchmark's Limitations

This concern isn't just mine. In March 2024, AI expert Andrej Karpathy [tweeted about the evaluation crisis](https://x.com/karpathy/status/1896266683301659068) in AI, specifically highlighting SWE-Bench Verified's limitations:

> "My reaction is that there is an evaluation crisis. I don't really know what metrics to look at right now... SWE-Bench Verified (real, practical, verified problems) I really like and is great but itself too narrow."

Karpathy's assessment aligns with my analysis—while SWE-Bench Verified offers valuable insights through practical, verified problems, its narrow scope fails to capture the true complexity of real-world software engineering tasks, particularly those requiring multi-file changes.

## The Multi-File Challenge: A Different Cognitive Task

Solving multi-file issues requires sophisticated capabilities that go beyond what current AI systems excel at:

- Cross-file dependency tracking
- Contextual understanding across modules
- Architectural comprehension
- Interface consistency management
- Impact analysis across the codebase

Current systems excel as "patch generators" for localized changes but struggle to function as holistic developers who understand the broader implications of their modifications.

## Conclusion

While we've made impressive strides in addressing isolated coding challenges, our progress with interconnected, complex issues remains limited. The current SWE-Bench-Verified benchmark understates the complexity of real-world software engineering by presenting a distribution of challenges that skews heavily toward single-file edits—only 14.2% of issues requiring multi-file changes compared to 50.27% in real-world scenarios.

For meaningful advancement in AI-assisted programming, future versions of SWE-Bench-Verified should rebalance to reflect realistic multi-file ratios, providing a more accurate measure of practical capability. As [I argued in my January 2025 post](https://jatinganhotra.github.io/blog/swe-agents/2025/01/05/swe-bench-mutliple-files.html), the current impressive performance numbers on the leaderboard must be interpreted with this significant caveat in mind.

> error ""
> We should recognize that SWE-Bench-Verified, while valuable, presents an overly optimistic view of AI's programming capabilities in real-world scenarios.
