# Audited Ranking Parameters

> Public research index extracted from the open-source X recommendation code.
>
> **Source:** `heydjekyll/x-algorithm`  
> **Snapshot:** `home-mixer/params/param.rs`  
> **Last audited:** 2026-08-28  
> **Source sync noted in file:** 2026-08-27T19:41:17Z

This page is an index of notable ranking and recommendation parameters found in the source tree. It is intended to make the audit reproducible and easy to reference.

## Important reading rule

These values are **not raw engagement points**.

The source explicitly states that the action weights multiply the **predicted probability** of an action for a viewer, or a continuous value such as watch time. They do not multiply raw engagement counts.

So:

`20 × P(copy link)`

does **not** mean:

> one copy link = 40 likes

The weights are components of a personalized ranking model.

## Core action weights

| Parameter | Value | Signal |
|---|---:|---|
| `FavoriteWeight` | **0.5** | Favorite |
| `ReplyWeight` | **5.0** | Reply |
| `BidirectionalFollowReplyWeightBoost` | **15.0** | Reply relationship boost |
| `RetweetWeight` | **1.0** | Retweet |
| `QuoteWeight` | **5.0** | Quote |
| `ShareWeight` | **2.0** | Share |
| `ShareViaDmWeight` | **5.0** | DM share |
| `ShareViaCopyLinkWeight` | **20.0** | Copy-link share |
| `FollowAuthorWeight` | **4.0** | Follow author |
| `ClickWeight` | **0.4** | Click |
| `OpenLinkWeight` | **0.2** | Open link |
| `DwellWeight` | **0.05** | Dwell |
| `ContDwellTimeWeight` | **0.004** | Continuous dwell |
| `PhotoExpandWeight` | **0.05** | Photo expand |
| `VideoOpenWeight` | **0.07** | Video open |

## Negative feedback weights

| Parameter | Value | Signal |
|---|---:|---|
| `NotInterestedWeight` | **-43.2** | Not interested |
| `BlockAuthorWeight` | **-31.2** | Block author |
| `MuteAuthorWeight` | **-58.8** | Mute author |
| `ReportWeight` | **-234.0** | Report |
| `NotDwelledWeight` | **-0.02** | Not dwelled |

### Important caveat

The source comments explicitly reject interpreting these values as raw-count cancellation ratios.

It also notes that baseline Report probability is more than 1000× lower than Like probability, which is part of the reason the Report weight is so large.

The source further states that recommendation effects are personalized and that coordinated direct navigation to a post does not count as the same kind of Home Timeline action.

## Author diversity

| Parameter | Value |
|---|---:|
| `EnableAuthorDiversity` | **true** |
| `AuthorDiversityDecay` | **0.5** |
| `AuthorDiversityFloor` | **0.25** |

This is evidence that final recommendation quality is not simply the sum of independent post scores.

Repeated exposure from the same author can be discounted.

## Network and discovery context

| Parameter | Value |
|---|---:|
| `OonWeightFactor` | **0.75** |
| `TopicOonWeightFactor` | **0.5** |
| `EnableOonRescoreForInNetworkRepliesRetweets` | **true** |
| `EnableBidirectionalFollowHydration` | **true** |
| `EnableAllAuthorFollowHydration` | **true** |

These parameters indicate explicit treatment of network position, out-of-network discovery, and relationship context.

## VM reranking

| Parameter | Value |
|---|---:|
| `EnableVMRanker` | **true** |
| `VMRankerDppTheta` | **0.65** |
| `VMRankerDppMaxSelectedRank` | **150** |

The DPP reranking configuration is important because it means the final slate is not necessarily a simple sort by individual candidate score.

The system can optimize the composition of the recommendation set.

## Cold start

| Parameter | Value |
|---|---:|
| `ColdStartImpressionThreshold` | **1000** |
| `ColdStartSlotMin` | **15** |
| `ColdStartSlotMax` | **16** |
| `ColdStartFollowerCap` | **1000** |
| `ColdStartMaxPostAgeSecs` | **86400** |
| `EnableViewerColdStart` | **true** |
| `EnableColdStartThompsonSampling` | **false** |
| `ColdStartBetaAlpha0` | **0.75** |
| `ColdStartBetaBeta0` | **49.25** |
| `ColdStartTsTopK` | **2** |
| `ColdStartImpressionScale` | **1.0** |

These values expose explicit exploration and cold-start machinery. They should not be interpreted as a guarantee that every new account or post receives a fixed distribution pattern.

## Low-impression handling

| Parameter | Value |
|---|---:|
| `LowImpressionsMaxPositionRatio` | **0.85** |

This is another reason to distinguish low-observation content from mature content when analyzing performance.

## Dwell-regret model

Notable parameters include:

| Parameter | Value |
|---|---:|
| `DwellRegretTemperature` | **10.0** |
| `DwellRegretDwellFloor` | **1.0** |
| `DwellRegretAlphaFavorite` | **1.0** |
| `DwellRegretAlphaReply` | **1.0** |
| `DwellRegretAlphaRetweet` | **1.0** |
| `DwellRegretAlphaQuote` | **1.0** |
| `DwellRegretAlphaShare` | **1.0** |
| `DwellRegretAlphaShareViaDm` | **1.0** |
| `DwellRegretAlphaShareViaCopyLink` | **1.0** |
| `DwellRegretNegNotInterested` | **-10000.0** |
| `DwellRegretNegBlockAuthor` | **-8000.0** |
| `DwellRegretNegMuteAuthor` | **-15000.0** |
| `DwellRegretNegReport` | **-60000.0** |
| `DwellRegretGateBias` | **1.033918** |
| `DwellRegretGateThreshold` | **-0.634264** |

These should be treated as model internals rather than simple creator-facing “weights”.

## Retrieval and inference configuration

The same parameter file exposes additional infrastructure signals, including:

- Phoenix source enabled
- SimClusters source enabled
- Phoenix inference clusters
- retrieval inference clusters
- topic filtering configuration
- new-user inference configuration
- fallback controls
- MOE controls
- retrieval/scoring sequence hydration
- explicit and implicit engagement signal controls

Not every parameter is a ranking weight. Some belong to retrieval, inference, hydration, experimentation, or infrastructure.

This distinction matters when interpreting the source.

## Evidence classification

Use the following labels when citing this index elsewhere:

### A — Direct code/config evidence
The parameter or behavior is directly visible in the source.

### B — Documented platform behavior
The repository or official documentation explicitly describes it.

### C — Operational inference
A reasonable interpretation based on A or B, but not directly stated.

### D — Strategy hypothesis
A proposed experiment or practical implication that still requires validation.

**Do not present C or D as A.**

## Reproducibility

For future audits, record:

- repository
- commit
- source file
- extraction date
- relevant configuration
- experiment state when available

The values in this page are a dated research snapshot, not a claim that these constants are permanent production values.

## Primary source

**Repository:** `heydjekyll/x-algorithm`

**Source file:** `home-mixer/params/param.rs`

**Audit date:** 2026-08-28

**Source sync noted in file:** 2026-08-27T19:41:17Z
