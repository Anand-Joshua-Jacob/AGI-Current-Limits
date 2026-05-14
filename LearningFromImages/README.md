# Learning from images Benchmark
> This is a Github repo, documenting the task and results of a Kaggle Benchmark. Link [here](https://www.kaggle.com/benchmarks/anandjoshuajacob/learning-from-images)

- Research shows that the human brain processes visuals 60,000 times faster than text.[1]
- Spatial reasoning, which requires ability to perceive and manipulate spatial relationships in the 3D world, is a fundamental aspect of human intelligence, yet remains a persistent challenge for Multimodal large language models (MLLMs). [2]
- `Learning` is a crucial cognitive faculty that LLMs and MLLMs need to be evaluated on. For broader context, see the main [AGI-Genkai repository](https://github.com/Anand-Joshua-Jacob/AGI-Genkai/tree/main).
- Therefore, creating high quality benchmarks involving visual input and learning is necessary. 
- This benchmark attempts to evaluate how well LLMs can learn conventions from images and apply them to reason about a target image.


## Problem Statement
- For many current systems, learning occurs only during training or in-context. However, for truly robust and adaptive behavior, AI systems should be able to learn (and retain) new knowledge and skills over time. 
- In this benchmark we will be testing in-context learning and reasoning ability of MLLMs on images.

## Overview of this Repository
- Please read further along this `README` for task details and benchmark construction.
- Copy of images used in the Kaggle Datasets are in `./database`
- HTML version of the chat logs can be viewed `./results/html`
- Final Report is in `./reports/Report.pdf`
- Some additional observations in `./reports/All_results`

## Datasets and Tasks

There are 3 Kaggle datasets, each with an associated Kaggle task:
Each image in the Datasets were drawn by me, so they should not appear in any model’s training data.

1. [Dataset 1 – Jumping Task](https://www.kaggle.com/datasets/anandjoshuajacob/task-jumping)
2. [Dataset 2 – No Explicit Scale](https://www.kaggle.com/datasets/anandjoshuajacob/stickfigures-without-explicit-scale)
3. [Dataset 3 – Relabelled Directions](https://www.kaggle.com/datasets/anandjoshuajacob/visual-learning-3)


---

### Core Task Setup (All Datasets)

- Each dataset has 5 subtasks (e.g., `task1_1` to `task1_5`)
- Each subtask has 4 images:
  - **3 example images** (options A, B, C): show basic movements to the right, left and up.
  - **1 target image** (image 4): same for all subtasks in all 3 datasets.  
  - The first 3 images are presented to the LLM as **option A**, **option B**, and **option C**.
  - Target Image:  
    <img src="database/task1/task1_1/4.jpg" alt="Sample Image" width="300" style="margin-left:50px;">  
  - The **fourth image** (target) always shows:
    - Initial stickman position on the **left**
    - Final stickman position on the **right**
    - A **red region** between them that must be avoided
  - The LLM is told:
    - To avoid red.
    - Normal laws of physics apply.
    - It must find the **shortest sequence of moves** (a sequence over {A, B, C}) that reproduces the motion in the target image.
  
  
  ---
  ### Dataset 1 – Task Jumping
  **Link:** [Dataset 1 on Kaggle](https://www.kaggle.com/datasets/anandjoshuajacob/task-jumping)  
  - Contains **5 subtasks**: `task1_1` to `task1_5`. Each subtask has 4 images.
  #### Progressive removal of cues
  - **`task1_1`**:
    - Each of the 4 images shows **two stickmen**:
      - **Gray stickman**: initial position
      - **Black stickman**: final position
    - The 3 images indicating movement have:
      - **Labels** for initial and final positions.
      - **Arrows** indicating direction of motion.
    - Target image:
      - Only shows gray and black stickmen (no labels, no arrows).
    - LLM has to **learn** the gray and black stickman represents initial and final positions.
  - **`task1_2` to `task1_4`**:
    - The first image still has labels and arrows.
    - Labels/arrows are **gradually removed** in images 2 and 3.
    - Target image is same as earlier.
    - The LLM has fewer explicit cues to interpret the motions and target image.
  - **`task1_5`**:
    - Only the **first** image includes labels and arrows.
    - The remaining two images and the target image:
      - Show only gray and black stickmen.
      - No labels or arrows.
    - The LLM has only one image with cues and has to interpret the other images from that one shot example.
  #### Additional Visual Cues
  - A **scale** is shown in both the **X and Y directions**.
  - This allows the LLM to see that the stickman moves specific distances from one position to another and to compare these movements to the target image.
  
  #### Challenge for the LLM
    - **Learn** what the Gray and Black stick man represent.
    - **Infer** what each option (A, B, C) does in the images.
    - **Infer** what kind of motion the target image represents.
    - **Reason** and come up with a minimal action sequence that matches the target.

  #### Correct reasoning:  
  - Moving right first leads the stickman directly into the red region. The shortest valid sequence is:
    1. **Jump (up)** to clear the obstacle.
    2. **Move right** and then fall due to gravity.
  - So the correct sequence in Dataset 1 is `"CA"` (jump, then move right).
  ---
### Dataset 2 – Without Explicit Scale
  **Link:** [Dataset 2 on Kaggle](https://www.kaggle.com/datasets/anandjoshuajacob/stickfigures-without-explicit-scale)
  - Contains **5 subtasks**: `task2_1` to `task2_5`.
  - Structurally the **same as Dataset 1**, but:
    - There is **no explicit X/Y scale** in the images.
  #### Motivation
  In Dataset 1, many LLMs responded by reasoning in terms of exact units, e.g.,  
  “The stick figure moved X units to the right from coordinate (a, b) to (c, d).”
  For Dataset 2:
  - The scale is **removed**.
  - The LLM must:
    - Internally form approximate representations of vertical and lateral movements.
    - Compare these to the target image to determine which sequence recreates the target displacement.  

    
 The **correct sequence** remains:
   - Jump, then move right → `"CA"`.
  ---
### Dataset 3 – Relabelled Directions
  **Link:** [Dataset 3 on Kaggle](https://www.kaggle.com/datasets/anandjoshuajacob/visual-learning-3)
  - Contains **5 subtasks**: `task3_1` to `task3_5`.
  - Structurally the **same as Dataset 1**, but **option meanings are permuted**.
  #### Changed Option Mapping
  - **Option A** → movement up
  - **Option B** → movement left
  - **Option C** → movement right
 #### Motivation
  - Left and right are opposites, so it is relatively easy for an LLM to relate labels and arrows between left/right images.
  - This dataset tests whether the LLM can:
    - Transfer what it learns from an **“up”** image (jump) to **lateral** movement images and to the target image.  


The **correct sequence** is:
  - Jump, then move right → `"AC"`.


### Benchmark Construction and Scoring

- There are 3 Kaggle tasks (one per dataset).
- Each Kaggle task has 5 subtasks.
- For each subtask:
  - The model is run for 5 trials.
  - Each correct final answer sequence irrespective of reasoning quality = 1 point, incorrect = 0.
- Score per Kaggle task:
  - Average over 25 runs (5 subtasks × 5 trials).
- Overall benchmark score:
  - Average of the 3 Kaggle task scores.

---


## Main results and insights
- Claude Opus 4.6 was the best performer
- Task 2 is generally harder than Task 1. Removing the scale seems to make it difficult for the LLMs to understand movement.
- When the benchmark was run on a text only model, it guessed the right answer simply by assuming what the contents of each image mostly was. So maybe the even the MLLMs are not learning purely based on what they see in the images.


## References & citations
1. [Using images in Media - MEC](https://oit.williams.edu/files/2010/02/using-images-effectively.pdf)
2. [Spatial Reasoning in Multimodal Large Language Models: A Survey of Tasks, Benchmarks and Methods, 2025](https://arxiv.org/html/2511.15722v1)