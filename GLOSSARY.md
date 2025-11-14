# 📖 Glossary of Terms

A comprehensive glossary of all technical terms used in this Brain-Computer Interface project.

---

## 🧠 Neuroscience Terms

### **Alpha Rhythm**
Brain oscillations in the 8-13 Hz range, typically seen when eyes are closed and relaxed. Similar to mu rhythm but over different brain areas.

### **Beta Rhythm**
Brain oscillations in the 15-30 Hz range, associated with active thinking and motor control. Decreases during motor imagery.

### **Brain-Computer Interface (BCI)**
A system that allows direct communication between the brain and external devices without using muscles. Reads brain signals and translates them into commands.

### **Cortex**
The outer layer of the brain responsible for higher-level functions like thinking, perception, and voluntary movement.

### **Delta Rhythm**
Very slow brain waves (0.5-4 Hz), primarily seen during deep sleep. Not relevant for motor imagery.

### **EEG (Electroencephalography)**
A method to record electrical activity of the brain using sensors (electrodes) placed on the scalp. Non-invasive and safe.

### **EOG (Electrooculography)**
Recording of eye movements. This project filters these out as they're artifacts (noise) for motor imagery classification.

### **ERD (Event-Related Desynchronization)**
A **decrease** in the power (amplitude) of brain oscillations in response to an event (like imagining a movement). This is the KEY signal this project detects!

### **ERS (Event-Related Synchronization)**
The opposite of ERD - an **increase** in oscillation power. Can occur after the movement or in different frequency bands.

### **Gamma Rhythm**
Very fast brain waves (>30 Hz), associated with high-level cognitive processing. This project filters these out as they contain mostly noise and muscle activity.

### **Motor Cortex**
The region of the brain responsible for planning, controlling, and executing voluntary movements. Located in the frontal lobe.

### **Motor Imagery**
The mental rehearsal of a movement without actually performing it. Activates similar brain areas as real movement, causing detectable ERD patterns.

### **Mu Rhythm**
Brain oscillations in the 8-14 Hz range over the motor cortex. Shows strong ERD during motor imagery. Most important signal for this project!

### **Somatotopy**
The mapping of body parts to specific areas of the brain. Different body parts (left hand, right hand, feet, tongue) activate different cortical areas.

### **Theta Rhythm**
Brain oscillations in the 4-8 Hz range, associated with drowsiness and meditation. Not the focus of motor imagery BCIs.

---

## 🔬 Signal Processing Terms

### **Artifact**
Unwanted signals or noise in EEG recordings. Sources include:
- Eye blinks (EOG)
- Muscle tension (EMG)
- Electrical interference (50/60 Hz power line)
- Motion artifacts

### **Band Power**
The total energy (power) of a signal within a specific frequency range (band). Calculated by integrating the power spectrum over that range.

### **Bandpass Filter**
A filter that allows frequencies within a certain range to pass through while blocking frequencies outside that range. This project uses 7-30 Hz.

### **Butterworth Filter**
A type of filter with a smooth frequency response. Used in this project for artifact removal (see `remove_artifacts.m`).

### **Downsampling**
Reducing the sampling rate by keeping only every Nth sample. Used to reduce computational load. This project downsamples from 250 Hz to 62.5 Hz (factor of 4).

### **FFT (Fast Fourier Transform)**
An algorithm to convert a time-domain signal into frequency-domain representation. Essential for analyzing which frequencies are present in the EEG.

### **Frequency Band**
A range of frequencies, e.g., 8-14 Hz. Different brain rhythms occupy different frequency bands.

### **Frequency Domain**
Representation of a signal showing how much of each frequency is present. Obtained by applying FFT to time-domain signal.

### **Highpass Filter**
Allows high frequencies to pass, blocks low frequencies. Removes slow drift and DC offset.

### **Lowpass Filter**
Allows low frequencies to pass, blocks high frequencies. Removes high-frequency noise and artifacts.

### **Nyquist Frequency**
Half the sampling rate. All frequencies must be below this to avoid aliasing. For 250 Hz sampling, Nyquist = 125 Hz.

### **Power Spectrum**
A representation showing the power (energy) at each frequency in a signal. Created by taking the squared magnitude of the FFT.

### **Sampling Rate (fs)**
Number of samples recorded per second, measured in Hertz (Hz). This dataset uses 250 Hz (250 samples per second).

### **Signal-to-Noise Ratio (SNR)**
Ratio of desired signal power to noise power. Higher is better. ERD signals have relatively low SNR, making classification challenging.

### **Spatial Filter**
A weighted combination of multiple sensors (EEG channels) to enhance signals of interest. CSP is a type of spatial filter.

### **Spectrogram**
A visual representation showing how the frequency content of a signal changes over time.

### **Time Domain**
The standard representation of a signal showing amplitude over time.

### **Window (Smoothing)**
A segment of time used for averaging or smoothing. This project uses a 2-second window for band power calculation.

---

## 🤖 Machine Learning Terms

### **Accuracy**
Percentage of correct predictions. For 4 classes, random = 25%. Our algorithm achieves ~60% accuracy.

### **Classifier**
An algorithm that assigns input data to one of several categories (classes). This project uses LDA and SVM classifiers.

### **Class / Label**
A category that data belongs to. This project has 4 classes:
1. Left hand
2. Right hand  
3. Feet
4. Tongue

### **Confusion Matrix**
A table showing predicted vs. true classes. Diagonal elements are correct predictions; off-diagonal are errors.

### **Cross-Validation**
A technique to evaluate model performance by splitting data into multiple train/test sets. This project uses **leave-one-subject-out** cross-validation.

### **Feature**
A measurable property used for classification. This project uses band power in different frequency ranges as features.

### **Feature Extraction**
Converting raw signals into numerical features suitable for machine learning. In this project: raw EEG → band power values.

### **Feature Space**
A multi-dimensional space where each dimension is one feature. Classifiers find decision boundaries in this space.

### **Generalization**
The ability of a trained model to perform well on new, unseen data. Good generalization means low overfitting.

### **Hyperparameter**
A parameter set before training (not learned from data). Examples: frequency bands, time windows, classifier regularization.

### **Kernel**
A function used in SVM to map data into higher-dimensional space. This project supports linear and Gaussian (RBF) kernels.

### **LDA (Linear Discriminant Analysis)**
A linear classifier that finds hyperplanes (decision boundaries) separating classes. Fast and works well with Gaussian-distributed data.

### **Overfitting**
When a model performs well on training data but poorly on test data. Happens when the model learns noise instead of true patterns.

### **Prediction**
The class assigned by a classifier to a new data point.

### **Regularization**
Techniques to prevent overfitting by penalizing model complexity. SVM uses parameter C for regularization.

### **Supervised Learning**
Learning from labeled data (input-output pairs). The classifier learns to map inputs to correct outputs.

### **SVM (Support Vector Machine)**
A powerful classifier that finds the optimal separating hyperplane with maximum margin. Can use kernels for non-linear boundaries.

### **Test Set**
Data used to evaluate the trained model. NEVER used during training. In this project: A0{1-9}E.gdf files.

### **Training Set**
Data used to train (fit) the model. In this project: A0{1-9}T.gdf files.

### **Underfitting**
When a model is too simple to capture data patterns. Results in poor performance on both training and test data.

---

## 📊 Statistical Terms

### **Cohen's Kappa (κ)**
A measure of agreement that accounts for chance. Better than accuracy for multi-class problems.
- κ = 0: No better than random
- κ = 0.46: Our algorithm's performance
- κ = 1: Perfect agreement

### **Covariance**
A measure of how two variables change together. Used in CSP to characterize signal properties for each class.

### **Eigenvalue**
A scalar that indicates the importance (variance) captured by an eigenvector. Larger eigenvalues = more important directions.

### **Eigenvector**
A direction in feature space. In CSP, eigenvectors become spatial filters (channel combinations).

### **Expected Accuracy**
The accuracy expected by chance alone. For 4 equally-likely classes: 25%.

### **Kappa Score**
See Cohen's Kappa.

### **Mean**
Average value. This project reports mean kappa across 9 subjects.

### **Standard Deviation**
A measure of variability/spread. Shows how much performance varies across subjects.

### **Variance**
The square of standard deviation. CSP maximizes variance ratio between classes.

---

## 🔢 Mathematical Terms

### **Convolution**
A mathematical operation used in filtering. Filtering is implemented as convolution of signal with filter coefficients.

### **Dot Product**
Multiplication and sum of two vectors. Used in applying spatial filters: `filtered = signal * csp_matrix`.

### **Hyperplane**
A flat subspace one dimension lower than the full space. In 2D: a line; in 3D: a plane. Classifiers use hyperplanes as decision boundaries.

### **Linear Combination**
A weighted sum of multiple values. CSP creates linear combinations of EEG channels.

### **Matrix**
A 2D array of numbers. Used to represent:
- Signal data (time × channels)
- Covariance matrices
- CSP transformation matrices

### **Matrix Multiplication**
Operation to combine matrices. Used when applying CSP: `s = s * csp_matrix`.

### **Projection**
Mapping data onto a lower-dimensional subspace. CSP projects multi-channel data onto components with maximum class separation.

---

## 💻 Programming Terms

### **Array**
A collection of elements. In MATLAB/Octave, can be multi-dimensional.

### **Function**
A reusable block of code. All `.m` files in this project are functions.

### **Header (h)**
A structure containing metadata about EEG data:
- Sampling rate
- Event markers (cues)
- Class labels
- Channel names

### **Parameter Structure (params)**
A MATLAB struct containing all configuration parameters. Passed between functions to control behavior.

### **Script**
A file containing commands to execute. Experiment files (e.g., `lda_bp_experiment.m`) are scripts.

### **Struct**
A MATLAB data structure with named fields. Used for `params` and `h` (header).

---

## 🎯 Project-Specific Terms

### **BCI Competition IV**
An international competition for brain-computer interface algorithms. This project uses dataset 2a from this competition.

### **BioSig**
An open-source MATLAB/Octave toolbox for processing biosignals (EEG, ECG, etc.). Used in this project for:
- Loading .gdf files (`sload`)
- LDA classification (`train_sc`, `test_sc`)
- CSP computation (`csp`, `covm`)
- Trial extraction (`trigg`)

### **Common Spatial Patterns (CSP)**
An algorithm to find optimal linear combinations of EEG channels that maximize variance for one class while minimizing it for others.

### **Cue**
A visual stimulus (arrow or symbol) shown to the subject indicating which movement to imagine. Marks the start of motor imagery.

### **GDF (General Data Format)**
A file format for storing biosignal data. Used for BCI Competition data.

### **.gdf File**
The actual data files containing EEG recordings. 
- `A0{1-9}T.gdf`: Training data
- `A0{1-9}E.gdf`: Evaluation (test) data

### **Octave**
An open-source alternative to MATLAB. This project is designed to run in Octave.

### **Shogun**
A machine learning library with SVM implementations. Used for SVM experiments (optional).

### **Subject**
A person who participated in the EEG recording. This dataset has 9 subjects (A01-A09).

### **TDP (Time-Domain Parameters)**
An alternative feature extraction method using adaptive autoregressive models. Not as good as band power for this dataset.

### **Trial**
One instance of motor imagery. Each subject performed 288 trials (72 per class) in training and test sets.

### **TRIG**
Trigger positions - time points when events (cues) occurred. Stored in header `h.TRIG`.

---

## 📐 Notation Guide

| Symbol | Meaning |
|--------|---------|
| `s` | Signal data matrix (time × channels) |
| `h` | Header structure with metadata |
| `f` | Feature matrix |
| `fs` | Sampling frequency (Hz) |
| `κ` | Kappa score |
| `v` | CSP transformation matrix |
| `DIV` | Downsampling factor |
| `pre` | Samples before cue |
| `post` | Samples after cue |
| `bp` | Band power |
| `tdp` | Time-domain parameters |

---

## 🎯 Common Abbreviations

| Abbr. | Full Term |
|-------|-----------|
| **BCI** | Brain-Computer Interface |
| **BP** | Band Power |
| **CSP** | Common Spatial Patterns |
| **EEG** | Electroencephalography |
| **EOG** | Electrooculography |
| **ERD** | Event-Related Desynchronization |
| **ERS** | Event-Related Synchronization |
| **FFT** | Fast Fourier Transform |
| **GDF** | General Data Format |
| **Hz** | Hertz (cycles per second) |
| **LDA** | Linear Discriminant Analysis |
| **MI** | Motor Imagery |
| **SNR** | Signal-to-Noise Ratio |
| **SVM** | Support Vector Machine |
| **TDP** | Time-Domain Parameters |

---

## 📚 Units

| Unit | Meaning | Typical Values in Project |
|------|---------|--------------------------|
| **Hz** | Hertz (frequency) | 8-30 Hz (frequency bands) |
| **s** | Seconds | 3.5-6 s (time windows) |
| **μV** | Microvolts | EEG amplitude (~10-100 μV) |
| **ms** | Milliseconds | Timing precision |
| **samples** | Data points | 250 per second |

---

## 🎓 Concept Relationships

### Signal Processing Chain
```
Raw EEG
  ↓ (Filtering)
Filtered EEG (7-30 Hz)
  ↓ (Downsampling)
Reduced-rate EEG (62.5 Hz)
  ↓ (CSP)
Spatially-filtered EEG
  ↓ (Band Power)
Features (66 per time point)
```

### Classification Chain
```
Features
  ↓ (Training)
Trained Classifier
  ↓ (Testing)
Predictions
  ↓ (Comparison to true labels)
Performance Metric (Kappa)
```

### Frequency Hierarchy
```
All frequencies (0-125 Hz)
  ├─ Low frequencies (0-7 Hz) ← filtered out
  ├─ Mu band (8-14 Hz) ← USED (Band 1)
  ├─ Beta band (15-30 Hz) ← USED (Bands 2-3)
  └─ High frequencies (>30 Hz) ← filtered out
```

---

## 🔍 Understanding Variable Names

### Common MATLAB Variable Patterns

```matlab
% Data variables
s           % Signal (time × channels)
h           % Header (metadata)
f           % Features
labels      % Class labels

% Parameters
params      % Parameter structure
fs          % Sampling frequency
DIV         % Division factor
pre, post   % Before/after cue samples

% Classifier variables
cc          % Trained classifier
r           % Classification results
MODE        % Classifier configuration

% Evaluation variables
kappa_value % Kappa score
CMX         % Confusion matrix
ACC         % Accuracy

% Loop variables
i, j, k     % Indices
ix          % Index array
```

---

## 🎯 Key Relationships

### Brain ↔ Signal
```
Brain Activity
  ↓ (Neurons firing)
Electrical Field
  ↓ (Detected by sensors)
EEG Signal
  ↓ (During motor imagery)
ERD Pattern
```

### Movement ↔ Brain Area
```
LEFT Hand Imagery → RIGHT Motor Cortex ERD
RIGHT Hand Imagery → LEFT Motor Cortex ERD
FEET Imagery → CENTRAL Motor Cortex ERD
TONGUE Imagery → MOUTH Motor Cortex ERD
```

### Time ↔ Processing
```
0s: Cue appears
↓
0-2s: ERD begins
↓
2-4s: ERD strongest ← Best classification time
↓
4-6s: ERD continues
↓
6s+: Return to baseline
```

---

## 💡 Analogies to Help Understanding

| Concept | Analogy |
|---------|---------|
| **EEG** | Like a microphone array listening to orchestra (brain) |
| **Filtering** | Like tuning a radio to remove static |
| **CSP** | Like finding the best camera angle to see differences |
| **Band Power** | Like measuring volume in different pitch ranges |
| **Classifier** | Like a sorting machine for parcels |
| **ERD** | Like dimming lights in a specific room |
| **Kappa** | Like grading on a curve (accounts for guessing) |

---

## 🎬 Timeline Terms

### Trial Structure
```
Pre-trial
  ↓
Fixation cross (準備)
  ↓
Cue appears (指示)
  ↓
Motor imagery period (想像)
  ↓
Relaxation (休息)
```

### Processing Timeline
```
Real-time (online)
  - User imagines movement
  - EEG recorded
  - Immediate classification
  - Device control

Offline (this project)
  - Data already recorded
  - Batch processing
  - Algorithm development
  - Performance evaluation
```

---

## 📖 Reading the Literature

When reading BCI papers, you'll encounter:

- **CNS**: Central Nervous System
- **fMRI**: Functional MRI (brain imaging)
- **MEG**: Magnetoencephalography (detects magnetic fields)
- **P300**: Event-related potential (different BCI paradigm)
- **SSVEP**: Steady-State Visual Evoked Potential (another BCI type)
- **MRCP**: Movement-Related Cortical Potential
- **SMR**: Sensorimotor Rhythm (another name for mu/beta)

---

## 🎯 Quick Definition Lookup

**Can't remember a term?** Use this quick guide:

| If you see... | It means... |
|---------------|-------------|
| `h.SampleRate` | How many samples per second (250 Hz) |
| `h.Classlabel` | Which movement (1-4) for each trial |
| `h.TRIG` | When cues appeared |
| `params.csp` | Whether to use CSP (1=yes, 0=no) |
| `kappa` | Performance score (0-1) |
| `covm` | Covariance matrix calculation |
| `trigg` | Extract trials from continuous signal |
| `sload` | Load .gdf file |

---

## 🎓 Learn More

For deeper understanding of these terms:

**Neuroscience:**
- "EEG for Dummies" tutorials online
- Brain anatomy atlases
- Motor control textbooks

**Signal Processing:**
- Digital Signal Processing courses
- FFT tutorials
- MATLAB/Octave documentation

**Machine Learning:**
- Pattern Recognition textbooks
- Classification tutorials
- Cross-validation guides

**BCI Specific:**
- BCI Competition website
- Motor imagery BCI reviews
- CSP tutorial papers

---

## 📝 Notes

- **Case Sensitivity:** MATLAB/Octave is case-sensitive: `classlabel ≠ Classlabel`
- **Indexing:** MATLAB uses 1-based indexing (first element is index 1, not 0)
- **Matrix Orientation:** In this project, typically rows=time, columns=channels
- **Frequency Units:** Always in Hz (cycles per second)
- **Time Units:** Usually in seconds, sometimes samples

---

## ✅ Glossary Usage Tips

1. **Ctrl+F (Find)** to quickly search for terms
2. Start with high-level terms (BCI, ERD) then dive deeper
3. Cross-reference with the comprehensive guide
4. Practice using terms in sentences
5. Create your own examples

---

**Still confused about a term?** Check:
1. This glossary
2. `COMPREHENSIVE_GUIDE.md` - Section explaining the concept
3. `VISUAL_GUIDE.md` - Visual representation
4. Comments in the .m files
5. Online resources (Wikipedia, papers)

---

*This glossary is your companion for understanding BCI terminology!* 📖✨

