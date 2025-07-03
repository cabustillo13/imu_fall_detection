# Inertial Measurement Unit Fall Detection

## 0) Research

Before starting any project, I always research information to better understand the problem and avoid reinventing the wheel. I enjoy bridging the gap between scientific publications and the project at hand, as this saves implementation time, reveals what has and hasn't worked in research, allows for analysis of strategies used in papers, and enables their adaptation to our project.
<br>All of this translates into better and more agile delivery times.

- [Inertial Measurement Unit Fall Detection Dataset](https://www.frdr-dfdr.ca/repo/dataset/6998d4cd-bd13-4776-ae60-6d80221e0365)
- [Validation of accuracy of SVM-based fall detection system using real-world fall and non-fall datasets](https://pdfs.semanticscholar.org/8220/032093e94c51080dd28b2c9eb7f4b7e5c191.pdf)

## 1) Considerations

### 1) Classification Approach: __Binary__ vs. Multi class

Based on the objectives of the "Fall Detection Challenge" and insights from the provided research paper, I have opted to frame this problem as a **binary classification task**.

**Justification:**

- Challenge Objective Alignment:
<br>The core requirement of the challenge is to "develop a model that can detect falls." This explicitly points towards a binary outcome: Is a fall occurring (positive class), or is it not (negative class)? The primary goal is the detection of the critical 'Fall' event.

- Practical Application:
<br>In real-world fall detection systems, the most crucial distinction is between a genuine fall incident (requiring immediate attention or alert) and any non-fall activity. While "Activities of Daily Living (ADLs)" and "Near Falls" represent different types of non-fall events, for the purpose of immediate detection and alerting, they both fall under the umbrella of "non-fall" scenarios.

- Reference to Scientific Literature (Aziz et al., 2017):
<br>The supplementary paper, "Validation of accuracy of SVM-based fall detection system using real-world fall and non-fall datasets" by Aziz et al. (2017), reinforces this approach.

**Conclusion on Class Definition:**

Therefore, my classification system defines the classes as follows:
- Positive Class (1): Falls
- Negative Class (0): ADLs (Activities of Daily Living) + Near_Falls