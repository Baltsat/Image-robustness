# Image-robustness: adversarial robustness of multimodal models — research plan and reports

Adversarial attacks on multimodal LLMs (CLIP, LLaVA family), measured end to end: attack generation, Attack Success Rate, cross-implementation generalisation, validation on 200+ real social-media videos, and a token-probability method for detecting attacks.

**Headline results:** ASR 7%–26.5% on LLaVA OneVision · 5 model architectures × 5 patch classes × 10 intensity levels · acc@1 46%–87%, acc@5 88%–100% · transition-score detection over a 37.11%–100.00% token-probability range.

## Contents

-   [2025 roadmap and results by quarter](#roadmap2025)
-   [Main expected outcome](#mainresult)
-   [Risks and threats](#risks)
-   [Stage 1 report (Q1 2025)](#q1_2025_report)
-   [Stage 2 report (Q2 2025)](#q2_2025_report)
-   [Stage 3 report (Q3 2025)](#q3_2025_report)
-   [Stage 4 report (Q4 2025)](#q4_2025_report)
-   [Summary](#summary)

---

<h2 id="roadmap2025">2025 roadmap and expected results by quarter</h2>

<details>
<summary><strong>Q1 2025: analysis and preparation</strong></summary>

**Planned activity:**

-   Review existing adversarial-attack methods against multimodal models, including Universal Adversarial Perturbation (UAP).
-   Collect and prepare a test dataset for evaluating model vulnerabilities (including video processing and filename standardisation).
-   Build a baseline pipeline for testing attacks on pretrained models (e.g. CLIP).
-   Run initial experiments and compute Attack Success Rate (ASR).

**Expected results:**

-   Completed analysis of current attack methods, including UAP.
-   Assembled and prepared test dataset.
-   Working prototype pipeline for generating attacks against CLIP and comparable models.
-   Effectiveness of standard attacks measured via ASR.
-   Preliminary report with first observations.

</details>

<details>
<summary><strong>Q2 2025: methodology improvement and testing</strong></summary>

**Planned activity:**

-   Improve attack-testing methodology, including experiment automation.
-   Run a series of tests across different attack-patch settings (size, shape, intensity).
-   Analyse and interpret the results.
-   Optimise the code for reproducibility and experiment scaling.
-   Prepare reporting and initial material for publication.

**Expected results:**

-   Identification of the key parameters driving attack success.
-   Improved methodology for generating attack patches.
-   Automated attack-testing process for faster experiments.
-   Interim report with a detailed analysis of attack effectiveness.

</details>

<details>
<summary><strong>Q3 2025: attack optimisation and defence research</strong></summary>

**Planned activity:**

-   Optimise attack patches for higher effectiveness (higher ASR, shorter generation time).
-   Analyse candidate strategies for defending models against the studied attacks.
-   Run additional experiments to check how well attacks generalise across models and data types.
-   Develop recommendations for improving multimodal model robustness.

**Expected results:**

-   Improved attack patches with high ASR.
-   Proposed baseline defence strategies.
-   Initial validation of the proposed defences.
-   Report with practical recommendations for developers of multimodal models.

</details>

<details>
<summary><strong>Q4 2025: defence validation and finalisation</strong></summary>

**Planned activity:**

-   Test the proposed defence strategies on real data and across different models.
-   Comprehensive testing and final optimisation of both attacks and defences.
-   Systematise all experimental results and prepare final project documentation.
-   Assess the feasibility of deploying the proposed solutions in production systems.
-   Prepare a scientific publication with the results.

**Expected results:**

-   Final assessment of multimodal model robustness and of the effectiveness of the proposed defences.
-   Complete final documentation covering the full research cycle.
-   Publication materials and recommendations for industrial adoption.
-   Scientific paper prepared and submitted.

</details>

---

<h2 id="mainresult">Main expected outcome at the end of year one</h2>

-   **Development and testing** of a baseline set of adversarial attacks on multimodal models (including UAP and attacks on CLIP).
-   **Proposal and initial validation** of defence strategies against the identified attacks.
-   **Scientific publications** (paper, reports) and final project reporting.
-   Identification of the key vulnerabilities of current MLLMs and of ways to reduce them.
-   **Verification of results** through documented Jupyter notebooks demonstrating the full research and optimisation cycle.

---

<h2 id="risks">Risks and threats to the expected results</h2>

<details>
<summary><strong>Show/hide risks and mitigation strategies</strong></summary>

-   **Risk: not enough relevant data for testing.**
    -   _Mitigation:_ combine private and public datasets (e.g. ImageNet, COCO, LAION) and apply data-augmentation techniques.
-   **Risk: the developed attacks are not effective enough (low ASR).**
    -   _Mitigation:_ iterative hyperparameter optimisation, research into adaptive attacks, use of attack ensembles.
-   **Risk: results and metrics are hard to interpret.**
    -   _Mitigation:_ focus on standardised, legible metrics (ASR, transferability, imperceptibility); visualise patches and attack effects; discuss results regularly with the team.
-   **Risk: insufficient compute.**
    -   _Mitigation:_ optimise code for resource use, apply distributed-computing techniques, plan and use cloud GPU/TPU capacity for heavy experiments.

</details>

---

<h2 id="q1_2025_report">📑 Stage 1 report (Q1 2025)</h2>

### Stage summary

-   **Period:** January – April 2025
-   **Focus:** analysis of existing attacks, infrastructure preparation, baseline adversarial attacks on multimodal models (CLIP).
-   **Key results:** working prototype attack pipeline, prepared test dataset, ASR computed for baseline attacks, initial experiments.

<details>
<summary><strong>Work carried out</strong></summary>

-   Studied current adversarial-attack and defence methods for multimodal models (per the literature review).
-   Collected, processed and validated a test dataset for the experiments.
-   Built and debugged an experimental pipeline for generating adversarial examples and automatically evaluating Attack Success Rate (ASR).

</details>

<details>
<summary><strong>Survey of attack and defence methods (literature review)</strong></summary>

**Current attack methods:**

-   **Patch / UAP / physical:** universal and targeted patches remain highly effective. They transfer well between models (CLIP, LLaVA, BLIP, ImageBind), and physical realisability is confirmed.
-   **Embedding alignment:** attacks on cross-modal embedding alignment (CrossFire, VLAttack) are effective even in black-box settings.
-   **Jailbreak / prompt injection:** the principal threat to current Large Vision-Language Models (LVLMs). These attacks successfully bypass most built-in safety mechanisms.
-   **FGSM / PGD / AutoAttack:** baseline gradient methods; on complex multimodal tasks they are often less effective than specialised attacks such as patches and UAP.
-   **Pipeline / data poisoning / backdoor:** attacks on data-processing stages or training sets (BadEncoder, VLTrojan). A long-term threat that plants hidden vulnerabilities.

**Current defence methods:**

-   **Adversarial training:** improves robustness to known attack types, but often degrades overall performance and remains vulnerable to new, unseen attacks.
-   **Robust encoders / input denoising:** attempts to make models less sensitive to small perturbations, or to clean the input. Limited effectiveness against strong attacks.
-   **Detection and rejection:** mechanisms for spotting adversarial examples or suspicious queries. Can be bypassed by adaptive attacks.
-   **Ensemble methods:** combining several models or defences. Raises the cost of an attack but does not guarantee protection.

**Key conclusion from the review:**

-   There is currently no universal defence against all types of adversarial attack on MLLMs. New attack types — jailbreak and cross-modal ones especially — appear faster than reliable defences are developed. Jailbreak and embedding-alignment attacks pose the greatest danger to current models. Standardised benchmarks and metrics for measuring real robustness are needed.

</details>

<details>
<summary><strong>Evidence and artefacts</strong></summary>

-   The full pipeline code, data processing and initial experimental results are available in a Jupyter notebook:
    -   **[main.ipynb](main.ipynb)**
        <sub>_The notebook contains: problem statement, implementation of baseline attacks, data-processing code, experiment runs, ASR computation, visualisations and stage-one conclusions._</sub>

</details>

### Quick link to the main result of this stage

-   ▶️ [Open the Q1 experiments notebook (main.ipynb)](main.ipynb)

<details>
<summary><strong>Structure of main.ipynb</strong></summary>

-   **Introduction:** problem statement and the methodology chosen for Q1.
-   **Data preparation:** loading, preprocessing and preparation of the test dataset.
-   **Attack implementation:** code for generating baseline adversarial attacks (e.g. FGSM, PGD, patch attack).
-   **Experiments:** running the attack pipeline against the target model (CLIP) and the test dataset.
-   **Evaluation:** computing Attack Success Rate (ASR) and other relevant metrics.
-   **Visualisation:** examples of adversarial images, plots of ASR against attack parameters.
-   **Conclusions:** short conclusions from the stage-one experiments.

</details>

---

<h2 id="q2_2025_report">📑 Stage 2 report (Q2 2025)</h2>

### Stage summary

-   **Period:** April – June 2025
-   **Focus:** improving the testing methodology, automating experiments, and analysing attack effectiveness on video data.
-   **Key results:** automated pipelines for video processing and patch application, key attack parameters identified, code prepared for scaling experiments.

<details>
<summary><strong>Work carried out</strong></summary>

1.  **Improving the attack-testing methodology:**

    -   Designed and implemented functions for automatically overlaying attack patches onto video streams. This moved the work from manual testing to batch experiments and substantially accelerated analysis.
    -   Extended the methodology to support different attack settings such as patch size, shape and intensity (application frequency).

2.  **Automation and experiment optimisation:**

    -   Built a pipeline for automatic processing of video datasets (`for_video_fix_names.ipynb`) covering filename standardisation and data preparation. This improved reproducibility and made tests easier to scale.
    -   Developed a script (`patch_video.ipynb`) that automates patch application to video, allowing hypotheses to be tested systematically.

3.  **Analysis and interpretation of results:**
    -   Ran a series of tests that identified the key parameters driving attack success on video. Patch position and application frequency were established as critical factors.
    -   The collected data formed the basis of the initial material for a scientific publication summarising the findings on attack effectiveness against video content.

</details>

<details>
<summary><strong>Evidence and artefacts</strong></summary>

-   **Automated video-data processing:**

    -   **[for_video_fix_names.ipynb](for_video_fix_names.ipynb)**
        <sub>_Code for automatically standardising and preparing video datasets (TikTok, Dzen) — a key step for reproducibility and experiment scaling._</sub>
    -   <img src="imgs/for_video_fix_names_1.jpg" width="400"><br>
        <sub>_Example of quantitative analysis: heatmap of attacked-frame activation against the percentage of attacked frames and the frame index._</sub>

-   **Automated patch application to video:**
    -   **[patch_video.ipynb](patch_video.ipynb)**
        <sub>_Demonstrates the improved testing methodology, including the `apply_patch_to_frame()` function for automatic patch application and batch tests across different settings._</sub>
    -   <img src="imgs/81681.jpeg" width="400"><br>
        <sub>_Example result: an attack patch automatically overlaid on a video frame during an experiment._</sub>

</details>

### Quick links to the main results of this stage

-   ▶️ [Open the video-processing automation notebook (for_video_fix_names.ipynb)](for_video_fix_names.ipynb)
-   ▶️ [Open the patch-application automation notebook (patch_video.ipynb)](patch_video.ipynb)

---

<h2 id="q3_2025_report">📑 Stage 3 report (Q3 2025)</h2>

### Stage summary

-   **Period:** July – September 2025
-   **Focus:** optimising attack patches for higher effectiveness; analysing how well attacks generalise across models and data types.
-   **Key results:** optimised attack parameters reaching ASR up to 26.5%, confirmed generalisation across alternative model implementations, validation on a real dataset of 200 videos.

<details>
<summary><strong>Work carried out</strong></summary>

1.  **Optimising attack patches for LLaVA OneVision:**

    -   Developed specialised attacks against LLaVA OneVision aimed at increasing adversarial effectiveness on multimodal systems.
    -   Implemented a custom model-loading function `my_load_pretrained_model()` with parameters optimised for attack experiments.
    -   Ran experiments applying optimised patches to video content, supporting both single videos and batch processing of multiple files.
    -   Performed systematic Attack Success Rate (ASR) analysis via regex matching to quantify attack effectiveness on the Dzen and TikTok datasets.

2.  **Testing attack generalisation across implementations:**

    -   Tested the developed attacks against an alternative LLaVA OneVision implementation through the HuggingFace API, verifying that the attacks do not depend on a specific implementation.
    -   Configured a chat template so video content is handled in a format compatible with production systems.
    -   Implemented generation with intermediate scores (`output_scores=True`), allowing analysis of the model's confidence in the generated tokens.
    -   Analysed transition scores to quantify how attacks affect the model's confidence in its own predictions.

3.  **Working with real datasets from social platforms:**
    -   Worked with real datasets from social platforms (TikTok, Dzen) to check generalisation across different types of user content.
    -   Built a pipeline converting frame sets into MP4 video via `convert_frames_to_video()`, ensuring uniform data handling.
    -   Validated dataset structure by counting `label_dict` entries and checking the correspondence between video IDs and text labels.
    -   Prepared the infrastructure for large-scale experiments across different types of social-media video content.

</details>

<details>
<summary><strong>Stage results</strong></summary>

**1. Attack-effectiveness optimisation:**

-   Obtained optimised attack-patch parameters with a measured Attack Success Rate (ASR) between **7% and 26.5%** depending on configuration (parameter N from 1 to 10).
-   Developed a methodology for automatically computing ASR on the Dzen and TikTok datasets via regex matching, enabling systematic evaluation of attack success.
-   Identified LLaVA OneVision vulnerabilities to adversarial patches when processing video content, which supports concrete defence recommendations.

**2. Attack generalisation:**

-   Confirmed that attacks generalise when moving from the native LLaVA implementation to the HuggingFace version.
-   Obtained transition scores and token-generation probabilities (range **37.11% to 100.00%**), enabling analysis of the model's confidence in its predictions.
-   Developed a method for quantifying attack impact through token-probability analysis, which can be applied to detecting adversarial interference.
-   Established that the attacks work regardless of how the model is loaded and used, indicating fundamental architectural vulnerabilities.

**3. Cross-platform validation:**

-   Successfully processed **200 videos** from the TikTok/Dzen datasets, converted from frames into a single MP4 format at 30 FPS.
-   Confirmed the applicability of the attacks to diverse user content from social networks.
-   Identified the video characteristics (resolution, FPS, content type) that affect attack effectiveness, which supports optimising defence strategies.

**Overall achievements:**

1. **Attack optimisation:** ASR up to 26.5% via parameters specialised for LLaVA OneVision
2. **Generalisation:** attacks work independently of the model implementation (native LLaVA vs HuggingFace)
3. **Cross-platform reach:** applicability confirmed on real social-media content (200 videos)
4. **Evaluation metrics:** methods developed for quantification via transition scores and token probabilities (37.11–100%)

</details>

<details>
<summary><strong>Evidence and artefacts</strong></summary>

-   **Specialised attacks for LLaVA OneVision:**

    -   **[llava_onevision_attacks.ipynb](llava_onevision_attacks.ipynb)**
        <sub>_Implementation of optimised attacks for LLaVA OneVision, including custom model loading, batch video processing and systematic ASR computation via regex matching for the Dzen and TikTok datasets._</sub>

-   **Generalisation testing through the HuggingFace API:**

    -   **[llava_hf_generalization.ipynb](llava_hf_generalization.ipynb)**
        <sub>_Testing the attacks against an alternative model implementation via the HuggingFace API, including transition-score analysis and quantification of the attacks' effect on model confidence._</sub>

-   **Working with real social-media datasets:**
    -   **[video_datasets_conversion.ipynb](video_datasets_conversion.ipynb)**
        <sub>_Pipeline converting TikTok dataset frames into MP4, data-structure validation and preparation of the infrastructure for cross-platform experiments._</sub>

</details>

### Quick links to the main results of this stage

-   ▶️ [Open the optimised-attacks notebook (llava_onevision_attacks.ipynb)](llava_onevision_attacks.ipynb)
-   ▶️ [Open the generalisation-testing notebook (llava_hf_generalization.ipynb)](llava_hf_generalization.ipynb)
-   ▶️ [Open the dataset-conversion notebook (video_datasets_conversion.ipynb)](video_datasets_conversion.ipynb)

---

<h2 id="q4_2025_report">📑 Stage 4 report (Q4 2025)</h2>

### Stage summary

-   **Period:** October – December 2025
-   **Focus:** comprehensive testing, validation of defences on real data, systematisation of results, preparation for publication.
-   **Key results:** ASR up to 26.5%, testing across 5 model architectures, validation on 200+ real videos, a transition-score system for attack detection.

<details>
<summary><strong>Work carried out</strong></summary>

1.  **Comprehensive robustness testing:**

    -   Ran large-scale testing across the LLaVA family (v0, v1.5-7B, v1.5-13B, OneVision-0.5B, OneVision-7B) using the developed adversarial patches.
    -   Implemented Perplexity (PPY) computation to quantify how attacks affect the quality of model-generated text.
    -   Computed acc@1 (46%–87%) and acc@5 (88%–100%) for 5 classes of adversarial patch (cake, homer, unicorn, random, control).

2.  **Validation on real social-media data:**

    -   Processed over 200 videos from TikTok and Dzen through the automated pipeline.
    -   Tested 10 configurations of attacked-frame masks (from 10% to 100%).
    -   Confirmed the practical applicability of the attacks across diverse user content.

3.  **Developing an attack-detection methodology:**

    -   Implemented transition-score (token-probability) analysis over the 37.11%–100.00% range.
    -   Identified the dependence of target-token probabilities on the share of attacked frames.
    -   Proposed a method for detecting adversarial interference based on anomalies in the probability distribution.

4.  **Systematisation and publication preparation:**
    -   Assembled complete documentation across the four quarters of research.
    -   Prepared a "Multimodal Attacks" presentation visualising the results.
    -   Ensured reproducibility of all experiments through documented Jupyter notebooks.

</details>

<details>
<summary><strong>Stage results</strong></summary>

**1. Quantitative robustness metrics:**

-   **Attack Success Rate (ASR):** 7%–26.5% depending on parameter N (1–10)
-   **Perplexity:** values systematised for clean and attacked images
-   **Accuracy:** acc@1 from 46% to 87%, acc@5 from 88% to 100%
-   **Coverage:** 5 model architectures × 5 patch classes × 10 intensity levels

**2. Validation on real data:**

-   200+ videos from TikTok/Dzen processed through the automated pipeline
-   Attack effectiveness confirmed on social-media content
-   Video characteristics affecting attack success identified

**3. Detection system based on transition scores:**

-   Probability range: 37.11%–100.00% for individual tokens
-   The method is applicable to detecting anomalous model behaviour
-   Plots of probability against the share of attacked frames

**4. Final documentation:**

-   Results presentation (PDF)
-   4 documented Jupyter notebooks
-   Full README describing the methodology

</details>

<details>
<summary><strong>Evidence and artefacts</strong></summary>

-   **Comprehensive testing and metric computation:**

    -   **[llava_ppy_comprehensive_testing.ipynb](llava_ppy_comprehensive_testing.ipynb)**
        <sub>_Contains: Perplexity computation for LLaVA v0/v1.5, acc@1 and acc@5 metrics, result visualisation for 5 patch classes, comparison of clean and attacked images._</sub>

-   **Token-probability analysis (transition scores):**

    -   **[q4_transition_scores_analysis.ipynb](q4_transition_scores_analysis.ipynb)**
        <sub>_Demonstrates: batch video processing via the HuggingFace API, transition-score computation, plots of probability against attack parameters._</sub>

-   **Video-processing automation:**

    -   **[q4_video_batch_processing.ipynb](q4_video_batch_processing.ipynb)**
        <sub>_Contains: the `VideoProcessor` class for batch processing, configurable frame masks, handling of the Dzen and TikTok datasets._</sub>

</details>

### Quick links to the main results of this stage

-   ▶️ [Comprehensive testing and Perplexity (llava_ppy_comprehensive_testing.ipynb)](llava_ppy_comprehensive_testing.ipynb)
-   ▶️ [Transition-score analysis (q4_transition_scores_analysis.ipynb)](q4_transition_scores_analysis.ipynb)
-   ▶️ [Batch video processing (q4_video_batch_processing.ipynb)](q4_video_batch_processing.ipynb)

### Result visualisations (extracted from the notebooks)

| File | Description |
|------|-------------|
| [histogram_acc1_acc5_all_classes.png](imgs/q4_results/histogram_acc1_acc5_all_classes.png) | Histogram of acc@1 and acc@5 across all patch classes |
| [perplexity_5classes_llava_v1.5.png](imgs/q4_results/perplexity_5classes_llava_v1.5.png) | Perplexity across the 5 attack classes |
| [token_prob_cake_05b_vs_7b.png](imgs/q4_results/token_prob_cake_05b_vs_7b.png) | Comparison of the 0.5B and 7B models |
| [video_frame_with_patch.png](imgs/q4_results/video_frame_with_patch.png) | Example of a video frame carrying a patch |

---

<h2 id="summary">Summary: the state of adversarial attacks and defences on MLLMs (2022–2025)</h2>

> **Research context:**
>
> -   Multimodal models (MLLMs) underpin many current AI applications, but their vulnerability to adversarial attacks remains a serious problem.
> -   The absence of standardised robustness evaluation, combined with the rapid emergence of new attack vectors, makes genuinely reliable systems hard to build.
> -   This research systematises knowledge about attacks and defences, develops testing methodologies, and proposes practical recommendations.

<details>
<summary><strong>🗂️ Summary table of attack and defence types</strong></summary>

| Attack / defence type   | Examples and methods                  | Characteristics          | Target models / vulnerability | Defence status and limitations                            |
| ----------------------- | ------------------------------------- | ------------------------ | ----------------------------- | --------------------------------------------------------- |
| **Attacks**             |                                       |                          |                               |                                                           |
| Patch / UAP / physical  | Universal and targeted patches, physical | Effective, transferable | CLIP, LLaVA, BLIP, etc.       | Adversarial training (partial), denoising (limited)       |
| Embedding alignment     | CrossFire, VLAttack                   | Cross-modal              | All MLLMs                     | Few specific defences, hard to detect                     |
| Jailbreak / prompt inj. | Safety bypass, prompt manipulation    | High ASR                 | LVLMs (GPT-4V, Gemini)        | Prompt filtering, targeted training (easily bypassed)     |
| FGSM / PGD / AutoAttack | Gradient methods                      | Baseline                 | All, but less effective       | Adversarial training (relatively reliable)                |
| Data poisoning / backdoor | BadEncoder, VLTrojan, AnyDoor       | Hidden, persistent       | All, including pretrained     | Hard to detect; unlearning under research                 |
| Physical attacks        | Real objects and scenes               | Safety threat            | Autonomous systems, robots    | Practically no reliable real-world defences               |
| Exotic                  | MAA, CAD, FAP, Con Instruction        | New vectors              | Various                       | Defences under development or absent                      |
| **Defences**            |                                       |                          |                               |                                                           |
| Adversarial training    | Training on attacks                   | Raises robustness        | ---                           | Accuracy loss, vulnerable to new attacks                  |
| Input transformation    | Denoising, feature squeezing          | Preprocessing            | ---                           | Accuracy loss, bypassed by adaptive attacks               |
| Detection mechanisms    | Anomaly / attack detection            | Identification           | ---                           | Bypassed by adaptive attacks, false positives             |
| Robust architectures    | Use of robust components              | Model design             | ---                           | Hard to build, not universal                              |
| Ensemble methods        | Combining models or defences          | Raises attack cost       | ---                           | Higher resource cost, no guarantee                        |

</details>

<details>
<summary><strong>📋 Key theses on the state of the field</strong></summary>

-   **MLLM vulnerability:** current multimodal models are fundamentally vulnerable to a wide range of adversarial attacks, particularly patches, UAP, jailbreak and cross-modal manipulation.
-   **Arms race:** attack development outpaces the creation of effective defences. Most existing defence mechanisms are partial and can be bypassed by new or adaptive attacks.
-   **Need for standardisation:** there are no commonly accepted benchmarks or metrics for comprehensive multimodal adversarial-robustness evaluation, which makes models and defences hard to compare.
-   **Practical significance:** MLLM vulnerabilities pose real safety risks in critical applications such as autonomous driving, medicine and finance.
-   **This work:** provides a structured basis for further analysis, for building testing tools, and for creating more robust models.

</details>

<details>
<summary><strong>🔎 Research details and links</strong></summary>

-   Carried out a systematic analysis and classification of adversarial attacks along key axes: model lifecycle phase (training/inference), attack goal (classification, generation, jailbreak), modality (uni-/multi-), and attacker access level (white/gray/black-box).
-   Compared the vulnerability of different MLLM architectures to different attack types based on the literature review.
-   Analysed current defence approaches (adversarial training, input filtering, robust encoders, detection methods, ensembles) and their limitations.
-   The detailed literature review, classification and analysis of attacks and defences are in the companion document: [adversarial_attacks_report.md](adversarial_attacks_report.md)

</details>

---
