# 📋 Quick Reference Card

## 🎯 One-Line Summary
**Brain-computer interface that classifies 4 types of imagined movements from EEG signals using ERD patterns.**

---

## 🏃 Quick Start (3 Commands)

```bash
cd ~/PR/event-related-desynchronization/code
cp settings_example.m settings.m
cd experiments && octave lda_bp_experiment.m
```

---

## 📊 The 4 Classes

| Class | Movement | Brain Area Affected |
|-------|----------|-------------------|
| 1 | 🤚 Left Hand | Right motor cortex |
| 2 | ✋ Right Hand | Left motor cortex |
| 3 | 🦶 Both Feet | Central motor cortex |
| 4 | 👅 Tongue | Motor cortex (tongue area) |

---

## 🔄 Pipeline (8 Steps)

1. **Load** EEG data (.gdf files)
2. **Filter** 7-30 Hz (remove noise)
3. **Downsample** 250→62.5 Hz (reduce data)
4. **CSP** spatial filtering (enhance differences)
5. **Extract** band power features (3 frequency bands)
6. **Segment** into trials (3.5s pre + 6s post cue)
7. **Classify** with LDA (predict class)
8. **Evaluate** with kappa score (measure performance)

---

## 📁 Key Files

| File | Purpose | When to Use |
|------|---------|------------|
| `lda_bp_experiment.m` | **Best config** (0.46 κ) | Run first! |
| `final_evaluation.m` | Main pipeline loop | Called by experiments |
| `get_features.m` | Feature extraction | Core processing |
| `multiclass_csp.m` | CSP computation | Spatial filtering |
| `biosig_classify.m` | LDA classifier | Classification |
| `get_kappa.m` | Performance metric | Evaluation |

---

## ⚙️ Best Parameters (lda_bp_experiment)

```matlab
params.trim_low = 3.5;              # Trial start: 3.5s before cue
params.trim_high = 6;               # Trial end: 6s after cue
params.downsampling = 4;            # Downsample factor
params.feat.type = 'bp';            # Band Power features
params.feat.bands = [8,14;19,24;24,30];  # Mu + Beta bands
params.feat.window = 2;             # 2s smoothing window
params.csp = 1;                     # Enable CSP
params.classifier.type = 'LDA';     # Linear classifier
```

---

## 📈 Performance Metrics

| Metric | Value | Meaning |
|--------|-------|---------|
| **Test Kappa** | **0.46** | 46% better than random |
| Train Kappa | 0.79 | Training performance |
| Competition Rank | 3rd place | Out of all submissions |
| Accuracy | ~60% | Correct predictions |
| Random Chance | 25% | 1 in 4 classes |

---

## 🎯 Frequency Bands

| Band | Range (Hz) | Name | ERD During Motor Imagery |
|------|-----------|------|------------------------|
| 1 | 8-14 | Mu (μ) | ⬇️⬇️ Strong decrease |
| 2 | 19-24 | Lower Beta (β) | ⬇️ Moderate decrease |
| 3 | 24-30 | Upper Beta (β) | ⬇️ Slight decrease |

**Why 7-30 Hz filter?** Removes noise, keeps mu and beta rhythms.

---

## 🔍 Common Spatial Patterns (CSP)

**Purpose:** Find best way to combine 22 EEG channels

**Input:** 22 channels × time points  
**Output:** 22 transformed channels (better separated)

**Key Rule:** CSP matrix computed on TRAINING data only, then applied to test data!

---

## 🤖 Classifiers Available

| Classifier | File | Speed | Accuracy | Best For |
|------------|------|-------|----------|----------|
| **LDA** ✅ | `biosig_classify.m` | Fast | 0.46 κ | **Best overall** |
| SVM | `shogun_classify.m` | Slow | 0.44 κ | More complex data |

---

## 📂 Data Structure

```
data/2a/
├── A01T.gdf    # Subject 1 training (288 trials)
├── A01E.gdf    # Subject 1 test (288 trials)
├── A02T.gdf    # Subject 2 training
├── A02E.gdf    # Subject 2 test
├── ...         # Subjects 3-9
└── true_labels/
    ├── A01E.mat    # True labels for test set
    └── ...
```

**Download from:** http://www.bbci.de/competition/iv/#dataset2a

---

## 🛠️ Troubleshooting

| Error | Solution |
|-------|----------|
| "settings.m not found" | `cp settings_example.m settings.m` |
| "sload not found" | Run `biosig_setup.m` |
| "Data file not found" | Download .gdf files to `data/2a/` |
| Low kappa (<0.3) | Check data path, try re-running |

---

## 📝 Code Flow

```
lda_bp_experiment.m
    ↓
final_evaluation(params)
    ↓ [for each subject]
    ├─→ get_features(train_file)
    │      ├─→ load_data()
    │      ├─→ remove_artifacts()
    │      ├─→ multiclass_csp()
    │      └─→ bandpower()
    │
    ├─→ get_features(test_file)
    │
    ├─→ biosig_classify()
    │      ├─→ reshape_features()
    │      ├─→ train_sc() [BioSig LDA]
    │      └─→ test_sc()
    │
    └─→ evaluate_classifier()
           └─→ get_kappa()
```

---

## 🔧 How to Modify

### Change time window:
```matlab
params.trim_low = 4.0;    # Try 2-5s
params.trim_high = 7;     # Try 4-8s
```

### Change frequency bands:
```matlab
params.feat.bands = [8,12; 12,16; 16,24; 24,30];  # 4 bands
```

### Disable CSP:
```matlab
params.csp = 0;  # Compare performance!
```

### Try SVM:
```matlab
params.classifier.type = 'svm';
params.classifier.kernel = 'gaussian';
params.classifier.C = 1.0;
params.classifier.kernel_width = 0.5;
```

### Use TDP features:
```matlab
params.feat.type = 'tdp';
params.feat.subtype = 'log-power';
params.feat.u = 0.015;
params.feat.d = 5;
```

---

## 🧮 Key Formulas

### Kappa Score
```
κ = (Observed_Accuracy - Expected_Accuracy) / (1 - Expected_Accuracy)
```

### Band Power
```
BP(f1,f2) = ∫[f1 to f2] |FFT(signal)|² df
```

### CSP
```
Find W that maximizes: var(W'*C1) / var(W'*C2)
where C1, C2 are class covariances
```

---

## 📊 Expected Output

```
Running final classifier evaluation experiment
### Loading data from file A01T.gdf...
Input signal size: 168000 x 25
Multiclass CSP calculated for signal
Features: Bandpower
Feature set size: 168000 x 66

Subject 1: Train κ=0.82, Test κ=0.47
Subject 2: Train κ=0.76, Test κ=0.41
Subject 3: Train κ=0.79, Test κ=0.49
...
Subject 9: Train κ=0.81, Test κ=0.45

Mean training kappa: 0.7891
Mean test set kappa: 0.4613  ← SUCCESS!
```

---

## 🎓 Key Concepts

| Term | Meaning |
|------|---------|
| **EEG** | Electroencephalography - records brain electrical activity |
| **Motor Imagery** | Imagining a movement without doing it |
| **ERD** | Event-Related Desynchronization - decrease in brain wave power |
| **CSP** | Common Spatial Patterns - optimal channel combination |
| **Band Power** | Signal energy in a frequency band |
| **Kappa** | Agreement measure that accounts for chance |
| **BCI** | Brain-Computer Interface |

---

## 🎯 Important Notes

⚠️ **CSP Matrix:** Must be computed on training data, then applied to test data  
⚠️ **Overfitting:** Training κ > Test κ is normal  
⚠️ **Time Window:** Best classification usually 2-4s after cue  
⚠️ **Individual Differences:** Performance varies across subjects  

✅ **Best Practice:** Always use same preprocessing for train and test  
✅ **Validation:** Test on separate subjects (cross-subject validation)  
✅ **Baseline:** Random = 25% accuracy (4 classes)  

---

## 🔢 Dimensions Cheat Sheet

| Stage | Dimensions | Example |
|-------|-----------|---------|
| Raw signal | time × channels | 168000 × 25 |
| EEG only | time × 22 | 168000 × 22 |
| After downsample | time/4 × 22 | 42000 × 22 |
| After CSP | time/4 × 22 | 42000 × 22 |
| Features | time × (22×3) | 42000 × 66 |
| Per trial | trial_len × feat × trials | 150 × 66 × 288 |
| Classifier input | (trials × trial_len) × feat | 43200 × 66 |

---

## 🚀 Experiment Ideas

1. **Different time windows** → When is ERD strongest?
2. **Different frequency bands** → Which bands matter most?
3. **With/without CSP** → How much does CSP help?
4. **Different classifiers** → LDA vs SVM performance?
5. **Different downsampling** → Speed vs accuracy trade-off?
6. **Fewer channels** → Can we use only central channels?

---

## 📚 Further Study

**Beginner:**
- Watch: "Introduction to EEG" on YouTube
- Read: Wikipedia on Motor Imagery
- Understand: Brain anatomy basics

**Intermediate:**
- Learn: Digital signal processing (filtering, FFT)
- Study: Machine learning classification
- Practice: MATLAB/Octave programming

**Advanced:**
- Read: CSP original paper (Ramoser et al.)
- Study: BCI Competition datasets
- Implement: Own BCI system

---

## 🎬 What Each Experiment Does

| File | Classifier | Features | Expected κ | Runtime |
|------|-----------|----------|-----------|---------|
| `lda_bp_experiment` | LDA | Band Power | **0.46** ⭐ | ~5 min |
| `lda_tdp_experiment` | LDA | TDP | 0.43 | ~10 min |
| `svm_bp_experiment` | SVM | Band Power | 0.44 | ~30 min |
| `svm_tdp_experiment` | SVM | TDP | 0.41 | ~45 min |

⭐ = Best performance

---

## 💡 Tips for Beginners

1. **Start simple:** Run `lda_bp_experiment.m` first
2. **Understand output:** Watch what gets printed
3. **One step at a time:** Don't try to understand everything at once
4. **Read the docs:** `COMPREHENSIVE_GUIDE.md` has details
5. **Experiment:** Change ONE parameter at a time
6. **Compare results:** Keep a log of what works
7. **Be patient:** Brain signals are inherently noisy!

---

## 🎯 Success Criteria

| Metric | Poor | Okay | Good | Excellent |
|--------|------|------|------|-----------|
| Test κ | <0.2 | 0.2-0.3 | 0.3-0.4 | >0.4 |
| Train κ | <0.5 | 0.5-0.7 | 0.7-0.8 | >0.8 |

**Your results:** Test κ ≈ 0.46 = **Excellent!** 🏆

---

## 📞 Help Resources

| Issue | Resource |
|-------|----------|
| Conceptual questions | `COMPREHENSIVE_GUIDE.md` |
| Visual explanations | `VISUAL_GUIDE.md` |
| Quick lookup | `QUICK_REFERENCE.md` (this file) |
| Code details | Comments in .m files |
| Dataset info | http://www.bbci.de/competition/iv/ |

---

## ✅ Checklist

Before running experiments:
- [ ] Octave installed
- [ ] BioSig downloaded and in correct path
- [ ] `settings.m` created and configured
- [ ] EEG data downloaded (.gdf files)
- [ ] True labels downloaded (.mat files)
- [ ] All packages installed (signal, etc.)

To verify setup:
- [ ] `octave biosig_setup.m` runs without errors
- [ ] Can load a .gdf file with `sload()`
- [ ] Data directory exists: `ls ~/PR/event-related-desynchronization/data/2a/`

---

## 🎓 Key Takeaways

1. **What:** BCI system for motor imagery classification
2. **How:** Filter → CSP → Features → Classify → Evaluate
3. **Best:** LDA + Band Power = 0.46 kappa
4. **Why:** Detect ERD patterns in mu/beta bands
5. **Use:** Help paralyzed people control devices

---

**Pro Tip:** Keep this file open while running experiments! 📋

**Ready to dive deeper?** → Read `COMPREHENSIVE_GUIDE.md`  
**Prefer visuals?** → Read `VISUAL_GUIDE.md`  
**Just want to run it?** → `cd experiments && octave lda_bp_experiment.m`

---

*Last Updated: November 2025*

