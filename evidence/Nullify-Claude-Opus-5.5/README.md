# Nullify autofix evaluation with Claude Opus 5.5

Nullify resolved 117 of the 190 LiveCVEBench leaderboard cases (61.58%) with the `user_report` input. The maintained verified subset accounted for all 117 successes across 183 cases (63.93%). [Browse the 190 cases and their test logs](CASES.md) or [open the submitted result file](../../submissions/Claude-Opus-5.5_Nullify.json).

## How to inspect a result

Choose a CVE in the [case index](CASES.md). The **Delivered fix** column links to the patch the harness accepted, including unsuccessful fixes. The **Official test log** gives the final grading run, delivery status, Terminal-Bench verdict, failure mode, test timestamps and individual parser outcomes. Its last section contains filtered terminal output in recorded order. Unavailable environments have a setup record instead.

Of 185 attempted cases, 179 had a delivered patch and six ended with an agent error before the harness accepted a fix. All 117 successes have a patch. For 174 deliveries the published diff is byte-for-byte identical to the sealed patch. The other five publish the implementation changes only. Their logs identify the omitted regression test files and provide both the original and published SHA-256 values.

The test log export keeps pytest outcome lines and numeric summaries. It strips terminal formatting, omits assertion bodies and shell commands, and replaces test parameter values with short hashes. The official parser results are listed separately because the parser can retain a verdict even when terminal output is incomplete. The source for 184 cases was the saved test session log. For CVE-2025-55741 the retained source was the post-test terminal pane. Four sessions had no matching terminal verdict or summary lines. Their records retain the timeout or parse error. The logs include digests of the source output and the sealed grade record. These hashes support file comparisons when the original records are available. [Public file checksums](SHA256SUMS)

## From user report to official grade

| Stage | What the harness did |
| --- | --- |
| Prepare the case | Started the task environment, allowed services to settle, and captured a working source copy plus the runtime inventory. The fixer received the user report and worked on that copy. The harness withheld reference fixes and hidden grading tests. |
| Locate the issue | Nullify's SAST localization step selected the relevant source for its production autofix state machine. Claude Opus 5.5 handled localization, recipe discovery, patching, sandbox review and test writing. Claude Haiku 5.5 handled context compaction. |
| Build and check a candidate | Experimental recipe discovery identified commands to build or exercise the project. The harness formatted candidate changes, replayed the recipe in a sandbox and returned review feedback to the fixer for another round when needed. Recipe commands could reach package registries through a controlled proxy. Search and retrieval were disabled. |
| Check candidate regression tests | For eligible projects, the test writer checked a proposed test on the original and modified source before retaining it. Unsupported languages skipped this stage. A source tree exceeding 8,192 paths or 128 MiB used a fallback that skipped recipe discovery, sandbox validation and test generation. |
| Deliver and grade | The harness applied the final patch to the task environment, repeated declared build steps and restarted the source service. Terminal-Bench ran the task's official tests. A submitted success required both an accepted delivery and a true official `is_resolved` verdict. Sandbox checks informed the repair loop. The official grader supplied the benchmark verdict. |

## Evaluation settings and coverage

- The run used dataset revision `b11057ac5288aa4ed595452f7e5f1df9919dbc00`, Nullify harness revision `e8118e18431d933985367ac2ec9a38c04ce0a25c` and Terminal-Bench 0.2.18. The model identifiers were `claude-opus-5-5` and `claude-haiku-5-5`.
- Each available case received one model attempt. The per-attempt agent limit was 6,840 seconds, including up to 1,080 seconds for recipe discovery and 2,700 seconds for repair. Official grading used each task's declared test timeout. The remaining cases after the 48-case pilot ran with up to 12 concurrent task workers.
- Infrastructure failures on CVE-2025-46346 and CVE-2025-9394 led to model-free regrades of the same saved attempts. Each case links the original delivery to the final regrade log. Both remain single entries in the denominator.
- The no-fix control passed on zero of 185 available environments. Reference fixes passed on 178 of 185. The seven reference failures were CVE-2025-24359, CVE-2025-48957, CVE-2025-52904, CVE-2025-55742, CVE-2025-6272, CVE-2025-9157 and CVE-2025-9796.
- Five unavailable environments count as unsuccessful with zero turns and tokens. CVE-2025-62416 was in the verified set. CVE-2025-12276, CVE-2025-68129, CVE-2025-9136 and CVE-2025-9670 were outside it. Of the three other cases outside that set, the original tests reported CVE-2025-49844 and CVE-2025-5644 unresolved, and CVE-2025-5501 ended with an agent error.
- Turns count model calls across roles. Tokens include recorded input and output, including cache usage. Counts are lower bounds for CVE-2025-14967, CVE-2025-46346, CVE-2025-46551, CVE-2025-61915, CVE-2025-8521 and CVE-2025-9772 due to incomplete provider usage records.
- The environment depended on upstream images and package availability at run time. Planning recorded ten unavailable cached references for auxiliary services. These tasks used the harness's normal checks for official auxiliary services. Their observed outcomes are included.
