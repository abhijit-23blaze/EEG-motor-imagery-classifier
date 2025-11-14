# 🧠 Event-Related Desynchronization (ERD): Complete Beginner's Guide

## 📚 Table of Contents
1. [What Is This Project?](#what-is-this-project)
2. [Background Concepts](#background-concepts)
3. [The Dataset](#the-dataset)
4. [How The Algorithm Works](#how-the-algorithm-works)
5. [Code Architecture](#code-architecture)
6. [Step-by-Step Pipeline Explanation](#step-by-step-pipeline-explanation)
7. [Key Files Explained](#key-files-explained)
8. [How to Run Experiments](#how-to-run-experiments)
9. [Performance Metrics](#performance-metrics)

---

## 🎯 What Is This Project?

This project is a **Brain-Computer Interface (BCI)** system that can read brain signals and figure out what movement a person is *imagining* doing. It's like reading someone's mind, but specifically for imagined movements!

### Real-World Application
Imagine a paralyzed person who can't move their arms. With this technology, they could:
- Control a robotic arm just by *thinking* about moving their arm
- Type on a computer by *imagining* hand movements
- Control a wheelchair by *thinking* about different movements

### What This Code Does
This codebase takes brain wave recordings (EEG data) and classifies them into **4 different types of imagined movements**:
1. **Left hand movement** 🤚
2. **Right hand movement** ✋
3. **Both feet movement** 🦶
4. **Tongue movement** 👅

### Achievement
This algorithm achieved **0.46 kappa score** on the BCI Competition IV dataset, which would have placed **3rd in the competition**! That's really impressive for a motor imagery classifier.

---

## 🧠 Background Concepts

### What is EEG (Electroencephalography)?
- **EEG** records electrical activity in your brain using sensors (electrodes) placed on your scalp
- Think of it like listening to the "electrical conversation" your brain neurons are having
- This dataset uses **22 EEG channels** (22 sensors on different parts of the head)
- Recorded at **250 Hz** (250 measurements per second)

### What is Motor Imagery?
- **Motor Imagery** is when you *imagine* doing a movement without actually moving
- When you imagine moving your left hand, similar brain areas activate as when you actually move it
- Different imagined movements create different patterns in your brain waves

### What is Event-Related Desynchronization (ERD)?
- When you imagine a movement, the rhythm of brain waves in certain areas **decreases** (desynchronizes)
- This happens in specific frequency bands:
  - **Mu rhythm** (8-14 Hz) - over motor cortex
  - **Beta rhythm** (15-30 Hz) - also over motor cortex
- Different body parts (left hand vs right hand) cause ERD in different brain areas
- This is the KEY signal we use to detect what movement someone is imagining!

### What is a Brain-Computer Interface (BCI)?
- A **BCI** is a system that lets you control computers or devices directly with your brain
- No need for keyboards, mice, or physical movement
- Just think, and the computer responds!

---

## 📊 The Dataset

### BCI Competition IV - Dataset 2a

**Source:** http://www.bbci.de/competition/iv/#dataset2a

**Participants:** 9 subjects (labeled A01 to A09)

**Structure:**
- **Training data:** Files named like `A01T.gdf`, `A02T.gdf`, etc. (T = Training)
- **Test data:** Files named like `A01E.gdf`, `A02E.gdf`, etc. (E = Evaluation)

**What's in the data:**
- 22 EEG channels recording brain activity
- 3 EOG channels (eye movement - these are filtered out)
- Each person performed 288 trials (72 trials of each of the 4 movement types)
- Each trial: person sits still → sees a cue → imagines the movement for several seconds

**File Format:**
- `.gdf` files (special EEG data format)
- Read using BioSig toolkit (a specialized library for biological signals)

---

## 🔄 How The Algorithm Works

### The Big Picture

```
Raw EEG Data (22 channels, noisy)
        ↓
[1] PREPROCESSING
    - Remove artifacts (eye blinks, noise)
    - Filter signals (7-30 Hz bandpass)
    - Downsample (reduce from 250 Hz to 62.5 Hz)
        ↓
[2] SPATIAL FILTERING (CSP)
    - Find the best way to combine the 22 channels
    - Maximize the difference between movement types
    - Output: transformed signals that emphasize differences
        ↓
[3] FEATURE EXTRACTION
    - Extract Band Power features OR Time-Domain Parameters
    - These are numerical descriptions of the signals
        ↓
[4] CLASSIFICATION
    - Train LDA or SVM classifier
    - Predict which movement was imagined
        ↓
[5] EVALUATION
    - Calculate Kappa score (performance metric)
    - Compare predictions to true labels
```

### The Machine Learning Pipeline

This is a **supervised learning** problem:
1. **Training Phase:** Learn patterns from labeled data
2. **Testing Phase:** Predict labels for new data
3. **Evaluation:** Measure how accurate the predictions are

---

## 🏗️ Code Architecture

```
event-related-desynchronization/
│
├── code/                          # Main codebase
│   ├── biosig_setup.m            # Setup script for BioSig library
│   ├── settings.m                # Configuration file (paths)
│   │
│   ├── experiments/              # Ready-to-run experiments
│   │   ├── lda_bp_experiment.m  # LDA + Band Power [BEST: 0.46 kappa]
│   │   ├── lda_tdp_experiment.m # LDA + Time-Domain Parameters
│   │   ├── svm_bp_experiment.m  # SVM + Band Power
│   │   └── final_evaluation.m   # Main evaluation loop
│   │
│   ├── preprocessing/            # Signal preprocessing
│   │   ├── remove_artifacts.m   # Bandpass filtering (7-30 Hz)
│   │   └── multiclass_csp.m     # Common Spatial Patterns
│   │
│   ├── features/                 # Feature extraction
│   │   ├── get_features.m       # Main feature extraction pipeline
│   │   └── reshape_features.m   # Reshape for classifier
│   │
│   ├── classifier/               # Classification algorithms
│   │   ├── biosig_classify.m    # LDA classifier (BioSig)
│   │   └── shogun_classify.m    # SVM classifier (Shogun)
│   │
│   ├── evaluation/               # Performance evaluation
│   │   ├── evaluate_classifier.m
│   │   └── get_kappa.m          # Calculate kappa score
│   │
│   ├── files/                    # Data loading utilities
│   │   └── load_data.m
│   │
│   └── utils/                    # Helper functions
│       └── get_data_directory.m
│
├── data/                         # EEG data files (you download these)
│   └── 2a/
│       ├── A01T.gdf, A01E.gdf   # Subject 1 training & test
│       ├── A02T.gdf, A02E.gdf   # Subject 2
│       └── ...                   # Subjects 3-9
│
└── full_code/                    # Additional analysis code
```

---

## 🔍 Step-by-Step Pipeline Explanation

### Step 1: Loading and Initial Setup

**File:** `biosig_setup.m`

```matlab
# What happens:
- Loads Octave's signal processing package
- Sets up paths to all code directories
- Installs BioSig library (for reading EEG data)
```

**Why it's needed:** The EEG data is in special `.gdf` format that needs BioSig to read.

---

### Step 2: Data Loading

**File:** `load_data.m`

```matlab
[s, h] = sload('A01T.gdf')
```

**What it returns:**
- `s`: The actual EEG signals (matrix: time_points × channels)
- `h`: Header information (sampling rate, event markers, labels, etc.)

**Example dimensions:**
- `s`: 168000 × 25 (168000 time points, 25 channels)
- First 22 channels are EEG, last 3 are EOG (eye movement)

---

### Step 3: Artifact Removal & Filtering

**File:** `remove_artifacts.m`

```matlab
# Applies a Butterworth bandpass filter (7-30 Hz)
[b, a] = butter(5, [7, 30]/(fs/2));
s_filtered = filter(b, a, s);
```

**Why this range?**
- **Below 7 Hz:** Slow drift, movement artifacts
- **7-30 Hz:** Contains mu (8-14 Hz) and beta (15-30 Hz) rhythms - the key ERD signals!
- **Above 30 Hz:** Mostly muscle activity and noise

**Analogy:** Like using a radio tuner to tune into the exact frequency of your favorite station, filtering out all other frequencies.

---

### Step 4: Downsampling

**In:** `get_features.m`

```matlab
DIV = 4;  # Downsampling factor
s = rs(s, DIV, 1);  # Reduces sampling rate by factor of 4
```

**Why downsample?**
- Original: 250 Hz (250 samples per second)
- After: 62.5 Hz
- **Benefit:** Reduces computational load while keeping all important information (we only care about frequencies up to 30 Hz)

---

### Step 5: Common Spatial Patterns (CSP)

**File:** `multiclass_csp.m`

**What is CSP?**
CSP is a technique to find the **best way to combine the 22 EEG channels** to maximize the difference between different classes.

**Analogy:** 
Imagine you have 22 microphones in a noisy room with 4 people talking. CSP finds the best way to combine the microphone signals so you can distinguish who is speaking.

**How it works mathematically:**
1. Separate trials by class (left hand, right hand, feet, tongue)
2. Calculate covariance matrix for each class
3. Find eigenvectors that maximize variance ratio between classes
4. These eigenvectors become spatial filters

**Result:**
- Input: 22 channels
- Output: 22 transformed channels (now optimally separated!)

**Code flow:**
```matlab
# Extract trials for each class
c1 = trigg(s, h.TRIG(h.Classlabel==1), ...)  # Left hand trials
c2 = trigg(s, h.TRIG(h.Classlabel==2), ...)  # Right hand trials
c3 = trigg(s, h.TRIG(h.Classlabel==3), ...)  # Feet trials
c4 = trigg(s, h.TRIG(h.Classlabel==4), ...)  # Tongue trials

# Calculate covariance matrices
ECM = cat(3, covm(c1,'E'), covm(c2,'E'), covm(c3,'E'), covm(c4,'E'))

# Compute CSP filters
v = csp(ECM)

# Apply spatial filtering
s = s * csp_matrix
```

---

### Step 6: Feature Extraction

**File:** `get_features.m`

Two feature types are available:

#### Option A: Band Power Features (BP) - **BEST PERFORMANCE**

**Parameters in `lda_bp_experiment.m`:**
```matlab
params.feat.type = 'bp';
params.feat.bands = [8,14; 19,24; 24,30];  # Three frequency bands
params.feat.window = 2;  # 2-second smoothing window
```

**What it does:**
- Extracts power (energy) in three frequency bands:
  - **8-14 Hz:** Mu rhythm (main motor imagery signal)
  - **19-24 Hz:** Lower beta
  - **24-30 Hz:** Upper beta
- For each band and each channel, calculates the power using `bandpower()` function
- Uses 2-second sliding window for smoothing

**Why it works:**
During motor imagery, power DECREASES (ERD) in these bands over specific brain areas. Different movements affect different areas.

**Output dimensions:**
- 22 channels × 3 frequency bands = 66 features per time point

#### Option B: Time-Domain Parameters (TDP)

**Parameters in `lda_tdp_experiment.m`:**
```matlab
params.feat.type = 'tdp';
params.feat.subtype = 'log-power';
params.feat.d = 5;      # Decimation factor
params.feat.u = 0.015;  # Update coefficient
```

**What it does:**
Uses an adaptive autoregressive (AAR) model to track time-varying signal properties.

---

### Step 7: Trial Segmentation

**Function:** `trigg()` (from BioSig)

```matlab
pre = ceil(3.5 * h.SampleRate);   # 3.5 seconds before cue
post = ceil(6.0 * h.SampleRate);  # 6 seconds after cue
[train_feats, sz] = trigg(f, h.TRIG, pre, post, gap);
```

**What it does:**
- Cuts the continuous EEG signal into individual **trials**
- Each trial: 3.5 seconds before the cue + 6 seconds after
- Why? The motor imagery happens in the few seconds after the cue

**Timeline of a trial:**
```
      Cue appears
          ↓
|----3.5s----|------6.0s------|
  Baseline    Imagery period
  (pre)       (post)
```

**Output shape:** `(time_points_per_trial, features, num_trials)`

---

### Step 8: Feature Reshaping

**File:** `reshape_features.m`

```matlab
feat = reshape(f, [size(f,1), size(f,2)*size(f,3)])';
```

**What it does:**
Converts 3D feature array to 2D matrix suitable for classifiers.

**Before:** `(time_points, features, trials)`  
**After:** `(time_points × trials, features)`

**Example:**
- Before: (150, 66, 288) → 150 time points, 66 features, 288 trials
- After: (43200, 66) → Each row is one time point from one trial

---

### Step 9: Classification

#### Option A: LDA (Linear Discriminant Analysis)

**File:** `biosig_classify.m`

**What is LDA?**
- **Simple, fast, linear classifier**
- Finds hyperplanes (decision boundaries) that separate classes
- Assumes Gaussian distributions

**How it works:**
```matlab
MODE.TYPE = 'LDA';
cc = train_sc(train_feat, train_labels, MODE);      # Train
r = test_sc(cc, test_feat, MODE, test_labels);      # Test
[~, predictions] = max(r.output, [], 2);            # Get predictions
```

**Why LDA works well here:**
- Motor imagery data is roughly linearly separable after CSP
- Fast training and prediction
- Works well with limited data

#### Option B: SVM (Support Vector Machine)

**File:** `shogun_classify.m`

**What is SVM?**
- **More powerful but slower**
- Finds optimal separating hyperplane with maximum margin
- Can use kernels for non-linear separation

**Parameters:**
```matlab
C = params.C;           # Regularization parameter
kernel = 'gaussian';    # or 'linear'
width = params.kernel_width;
```

---

### Step 10: Evaluation

**File:** `evaluate_classifier.m`, `get_kappa.m`

**Metric: Cohen's Kappa**

**Why not just accuracy?**
- With 4 classes, random guessing gives 25% accuracy
- Kappa accounts for chance agreement
- Better measure for imbalanced or multi-class problems

**Kappa formula:**
```
κ = (observed_accuracy - expected_accuracy) / (1 - expected_accuracy)
```

**Interpretation:**
- **κ = 0:** No better than chance
- **κ = 0.46:** **46% better than chance** ← Our algorithm!
- **κ = 1.0:** Perfect agreement

**What the code does:**
```matlab
# Build confusion matrix
# Calculate observed and expected accuracy
# Compute kappa statistic
[kappa_value, best_timepoint] = max(all_kappas);
```

**Note:** Kappa is calculated for each time point in the trial, and we report the **maximum** kappa (best time point for classification).

---

## 📁 Key Files Explained

### `lda_bp_experiment.m` - Main Experiment File

**Purpose:** Runs the best-performing configuration.

```matlab
# Parameter configuration
params.trim_low = 3.5;      # Start 3.5s before cue
params.trim_high = 6;       # End 6s after cue
params.downsampling = 4;    # Downsample by factor 4
params.feat.type = 'bp';    # Use Band Power features
params.feat.bands = [8,14; 19,24; 24,30];  # Three frequency bands
params.feat.window = 2;     # 2-second window
params.csp = 1;             # Enable CSP
params.classifier.type = 'LDA';  # Use LDA classifier

# Run evaluation
final_evaluation(params)
```

**What happens when you run it:**
1. Loads parameters
2. Calls `final_evaluation()` with these parameters
3. Tests on all 9 subjects
4. Prints mean training and test kappa

---

### `final_evaluation.m` - Main Pipeline

**Purpose:** The orchestrator that runs the entire pipeline for all subjects.

**Structure:**
```matlab
for i = 1:9  # Loop through 9 subjects
    # 1. Load training data
    train_file = sprintf('A0%dT.gdf', i);
    [f, h, xx, csp_matrix] = get_features(train_file, params);
    
    # 2. Segment into trials
    [train_feats, sz] = trigg(f, h.TRIG, pre, post, gap);
    train_feats = reshape(train_feats, sz);
    train_labels = h.Classlabel;
    
    # 3. Load test data and apply SAME CSP matrix
    test_file = sprintf('A0%dE.gdf', i);
    params.csp_matrix = csp_matrix;  # Key: use training CSP!
    [f, h, xx, yy] = get_features(test_file, params, test_labels);
    
    # 4. Segment test trials
    [test_feats, sz] = trigg(f, h.TRIG, pre, post, gap);
    test_feats = reshape(test_feats, sz);
    
    # 5. Classify
    [classifier_train_labels, classifier_test_labels] = 
        biosig_classify(train_feats, test_feats, train_labels, test_labels);
    
    # 6. Evaluate
    [train_kappa, test_kappa] = 
        evaluate_classifier(classifier_train_labels, classifier_test_labels, 
                          train_labels, test_labels);
    
    train_kap = [train_kap train_kappa];
    test_kap = [test_kap test_kappa];
end

# 7. Report results
mean(train_kap)
mean(test_kap)
```

**Key insight:** CSP is computed on TRAINING data only, then applied to test data. This prevents information leakage!

---

### `get_features.m` - Feature Extraction Pipeline

**Purpose:** Main preprocessing and feature extraction function.

**Input:**
- `file`: EEG data file (e.g., 'A01T.gdf')
- `p`: Parameters structure
- `classlabels`: (optional) true labels for test data

**Output:**
- `f`: Extracted features
- `h`: Header with metadata
- `csp_matrix`: CSP transformation matrix

**Pipeline within this function:**
```matlab
# 1. Load data
[s, h, filename] = load_data(file);

# 2. Remove artifacts (bandpass filter)
s = remove_artifacts(s, h);

# 3. Select only EEG channels (remove EOG)
eegchan = 1:22;
s = s(:, eegchan);

# 4. Downsample
s = rs(s, DIV, 1);

# 5. Apply CSP (if enabled)
if p.csp == 1
    csp_matrix = multiclass_csp(s, h, p);
    s = s * csp_matrix;
end

# 6. Extract features
if strcmp(p.feat.type, 'bp')
    f = bandpower(s, h.SampleRate, bands, win);
elseif strcmp(p.feat.type, 'tdp')
    [ff, gg] = tdp(s, d, u);
    f = ff;
end
```

---

### `multiclass_csp.m` - Spatial Filtering

**Purpose:** Compute CSP transformation for 4-class problem.

**How it handles multiple classes:**

Standard CSP is for 2 classes. For 4 classes:
1. Extract trials for each class separately
2. Compute covariance matrix for each class
3. Use multiclass CSP algorithm (from BioSig)

```matlab
# Separate trials by class
c1 = trigg(s, h.TRIG(h.Classlabel==1), ...)  # Left hand
c2 = trigg(s, h.TRIG(h.Classlabel==2), ...)  # Right hand
c3 = trigg(s, h.TRIG(h.Classlabel==3), ...)  # Feet
c4 = trigg(s, h.TRIG(h.Classlabel==4), ...)  # Tongue

# Stack covariance matrices
ECM = cat(3, covm(c1,'E'), covm(c2,'E'), covm(c3,'E'), covm(c4,'E'));

# Compute CSP
v = csp(ECM);
```

---

### `biosig_classify.m` - LDA Classification

**Purpose:** Train and test LDA classifier.

**Why reshape labels?**
```matlab
trial_len = size(train_feat, 2);  # e.g., 32 time points
train_labels = repmat(train_labels, 1, trial_len)'(:);
```

Since features are extracted at multiple time points per trial, we need to replicate labels for each time point.

**Training:**
```matlab
cc = train_sc(train_feat, train_labels, MODE);
```

**Testing:**
```matlab
r = test_sc(cc, test_feat, MODE, test_labels);
[~, predictions] = max(r.output, [], 2);
```

The classifier outputs probabilities for each class; we take the class with maximum probability.

---

## 🚀 How to Run Experiments

### Quick Start

```bash
cd ~/event-related-desynchronization/code/experiments
octave lda_bp_experiment.m
```

### What You'll See

```
====================== SETTING PATHS ======================
====================== LOADING BIOSIG ======================
Running final classifier evaluation experiment
### Loading data from file A01T.gdf...
Input signal size: 168000 x 25
Multiclass CSP calculated for signal, pre = 3.5, post = 6
Features: Bandpower
Feature set size: 168000 x 66
...
Kappa for training set = 0.783245
Kappa for testing set = 0.421156
...
Mean training kappa: 0.7891
Mean test set kappa: 0.4613
```

### Expected Results

| Experiment | Classifier | Features | Training κ | Test κ |
|------------|-----------|----------|------------|--------|
| `lda_bp_experiment` | LDA | Band Power | ~0.79 | ~**0.46** |
| `lda_tdp_experiment` | LDA | TDP | ~0.75 | ~0.43 |
| `svm_bp_experiment` | SVM | Band Power | ~0.85 | ~0.44 |
| `svm_tdp_experiment` | SVM | TDP | ~0.81 | ~0.41 |

**Best: LDA + Band Power → 0.46 test kappa** 🏆

---

## 📊 Performance Metrics

### Understanding Kappa Score

**Cohen's Kappa (κ)** measures agreement beyond chance.

**For our 4-class problem:**
- Random guessing: 25% accuracy → κ ≈ 0
- Our algorithm: **0.46 kappa** → 46% improvement over chance

**Confusion Matrix Example:**

```
              Predicted
           L.Hand R.Hand  Feet  Tongue
True      ┌─────────────────────────────┐
L.Hand    │  45     12      8      7    │
R.Hand    │  10     50      5      7    │
Feet      │   8      6     48     10    │
Tongue    │   9      8     11     44    │
          └─────────────────────────────┘
```

**Interpretation:**
- Diagonal = correct predictions
- Off-diagonal = confusions
- Kappa accounts for expected correct predictions by chance

### Why This Performance is Good

1. **4-class problem:** Harder than binary (2-class)
2. **Motor imagery:** Noisy, variable signal
3. **Individual differences:** Each person's brain is different
4. **Single-trial:** Classification of individual trials (not averaged)

**Context:** 
- Best in competition: κ ≈ 0.57
- This algorithm: κ ≈ 0.46 (would be 3rd place!)
- Random chance: κ ≈ 0.00

---

## 🎓 Key Concepts Summary

### Signal Processing Concepts

1. **Filtering:** Removing unwanted frequencies (like tuning a radio)
2. **Downsampling:** Reducing data rate while keeping information
3. **Spatial Filtering (CSP):** Optimally combining channels
4. **Feature Extraction:** Converting signals to numbers that describe them

### Machine Learning Concepts

1. **Supervised Learning:** Learning from labeled examples
2. **Training Set:** Data used to build the model
3. **Test Set:** New data used to evaluate performance
4. **Cross-Subject Validation:** Testing on all subjects separately
5. **Overfitting:** When model works on training but not test data

### Neuroscience Concepts

1. **Motor Cortex:** Brain area controlling movement
2. **Mu Rhythm:** Brain oscillations (8-14 Hz) over motor cortex
3. **Beta Rhythm:** Brain oscillations (15-30 Hz) over motor cortex
4. **ERD:** Decrease in oscillation power during motor imagery
5. **Somatotopy:** Different body parts map to different brain areas

---

## 🔧 Customization and Experimentation

### Want to try different parameters?

**Edit experiment files or create your own:**

```matlab
params = [];
params.trim_low = 4.0;      # Try different time windows
params.trim_high = 7;
params.downsampling = 8;    # Try more downsampling
params.feat.type = 'bp';
params.feat.bands = [8,12; 12,16; 16,24; 24,30];  # Try 4 bands
params.feat.window = 3;     # Try 3-second window
params.csp = 1;             # Try without CSP: params.csp = 0
params.classifier.type = 'LDA';

final_evaluation(params)
```

### Things to Experiment With

1. **Time window:** When is motor imagery strongest? Try different `trim_low` and `trim_high`
2. **Frequency bands:** Which frequencies matter most?
3. **CSP:** Does it help? Try with and without
4. **Downsampling:** How much can we reduce data?
5. **Classifier:** LDA vs SVM - which works better?

---

## 🐛 Common Issues and Solutions

### Issue 1: "File settings.m does not exist"
**Solution:** 
```bash
cd ~/event-related-desynchronization/code
cp settings_example.m settings.m
# Edit settings.m to set correct paths
```

### Issue 2: "sload function not found"
**Solution:** BioSig not installed properly. Re-run biosig_setup.m

### Issue 3: Data files not found
**Solution:** Download data from BCI Competition website and place in `data/2a/` directory

### Issue 4: Low kappa scores
**Possible causes:**
- Wrong data path
- Data not preprocessed correctly
- Random initialization (try running multiple times)

---

## 📚 Further Reading

### Research Papers
1. Original thesis: "Classification of Motor Imagery for Brain-Computer Interfaces" by Piotr Szachewicz
2. CSP method: "Designing optimal spatial filters for single-trial EEG classification" by Ramoser et al.
3. Motor imagery BCIs: "Motor Imagery: What Is It?" by Jeannerod (neuroscience perspective)

### Concepts to Learn More About
- **Digital Signal Processing:** Filtering, FFT, spectral analysis
- **Pattern Recognition:** Classification, feature extraction, validation
- **Neuroscience:** Motor cortex, oscillations, EEG
- **Linear Algebra:** Eigenvalues, covariance, transformations

---

## 🎯 Quick Reference

### Key Parameters (Best Configuration)

```matlab
params.trim_low = 3.5;           # Trial starts 3.5s before cue
params.trim_high = 6;            # Trial ends 6s after cue
params.downsampling = 4;         # 250 Hz → 62.5 Hz
params.feat.type = 'bp';         # Band Power features
params.feat.bands = [8,14; 19,24; 24,30];  # Mu + Beta bands
params.feat.window = 2;          # 2-second smoothing
params.csp = 1;                  # Enable CSP
params.classifier.type = 'LDA';  # LDA classifier
```

### Directory Structure

```
code/experiments/     → Run experiments here
code/features/        → Feature extraction code
code/preprocessing/   → CSP and filtering
code/classifier/      → LDA and SVM
code/evaluation/      → Kappa calculation
data/2a/              → EEG data files (.gdf)
```

### Commands

```bash
# Setup
cd ~/event-related-desynchronization/code
cp settings_example.m settings.m

# Run best experiment
cd experiments
octave lda_bp_experiment.m

# Run other experiments
octave lda_tdp_experiment.m
octave svm_bp_experiment.m
```

---

## 💡 Conclusion

This codebase implements a complete **Brain-Computer Interface** system that:

1. ✅ Reads raw brain signals (EEG)
2. ✅ Removes noise and artifacts
3. ✅ Extracts meaningful features
4. ✅ Classifies imagined movements
5. ✅ Achieves competitive performance (0.46 kappa)

**The beauty of this approach:**
- Relatively simple (linear methods)
- Fast training and testing
- Works well despite individual brain differences
- Real-world applicable

**You now understand:**
- What the code does (BCI for motor imagery)
- How it works (preprocessing → CSP → features → classification)
- Where each piece fits (file organization)
- How to run it (experiments)
- How to modify it (parameters)

---

## 🙋 Questions to Test Your Understanding

1. Why do we use bandpass filtering (7-30 Hz)?
2. What is the purpose of CSP?
3. Why is the test kappa lower than training kappa?
4. What are the 4 classes being classified?
5. Why use kappa instead of accuracy?
6. What happens during "Event-Related Desynchronization"?
7. Why downsample the signal?
8. Why is CSP computed only on training data?

<details>
<summary>Click for answers</summary>

1. This frequency range contains mu (8-14 Hz) and beta (15-30 Hz) rhythms that show ERD during motor imagery
2. CSP finds optimal linear combinations of EEG channels to maximize class separability
3. Overfitting - model adapts to training data noise that doesn't generalize
4. Left hand, right hand, feet, and tongue imagined movements
5. Kappa accounts for chance agreement, more appropriate for multi-class problems
6. Brain oscillation power decreases in motor-related frequency bands over relevant cortical areas
7. Reduces computational cost while retaining all frequencies of interest (< 30 Hz)
8. To prevent information leakage from test to training data (proper validation)

</details>

---

**Happy Brain Hacking! 🧠⚡**

