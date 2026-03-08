# concrete-beam-ml-analysis
predicting concrete beam structural targets using physics-informed machine learning and 2-stage OOF stacking. hit 0.836 R2 on kaggle with just 93 training rows.
Beam Structural Performance Prediction (0.836 R2)


# Structural Performance Prediction for Concrete Beams

## Introduction
The goal of this project is to take a small dataset of 117 beam samples and predict three key structural targets: **depth (d)**, **reinforcement density (r)**, and **ultimate load (uil)**. The data is extremely limited, with only 93 rows available for training, making overfitting a major challenge.

## Initial Baseline Results
I started by running a standard Random Forest model just to see where we stood. It did okay on the ultimate load (uil) because that has a very strong linear relationship with the span, but it completely failed on the reinforcement (r) and the depth (d).

* **Initial Mean R2:** ~0.49
* **The Problem:** The reinforcement density (r) is very non-linear and "jumpy." The baseline model was basically guessing randomly for that column, which dragged down the whole score.

## Feature Engineering & Physics Injection
To improve performance, I injected domain knowledge based on structural mechanics:

* **Bending Moment (M):** I calculated $M = uaudl \times le^2$. In engineering, the thickness of a beam is directly related to the square of the span, so adding this helped the model map the stress correctly.
* **Load Intensity:** Added a feature for load per unit length to help with the shear stress mapping.
* **Standardizing:** Used **RobustScaler** instead of a normal scaler because it ignores outliers. In structural data, if one beam has a massive load, it can ruin the math for all the other "normal" beams.



## The Stacking Architecture
The jump from **0.49 to 0.83** came from a **"Two-Stage Stacking"** approach. Instead of predicting everything at once, I broke it into steps:

1. **Stage 1 (Depth Prediction):** I used an ensemble of **XGBoost, Random Forest, and Ridge Regression** to predict the depth (d) first. I used **5-Fold Out-of-Fold (OOF)** predictions. This is critical because it means the model is predicting on data it hasn't seen, which simulates a real-world test and prevents leakage.
2. **Stage 2 (Advanced Features):** I took those predicted depth values and calculated the **Section Area** ($w \times d$) and **Stiffness** ($w \times d^3 / 12$).
3. **Final Prediction:** I fed these new "advanced" physics features back into the final model to predict the reinforcement (r) and ultimate load (uil).



## Results
* **Final Score:** **0.83666 R2** on the Kaggle Leaderboard.
* **Key Takeaway:** By forcing the AI to follow a logical engineering path (calculating depth before reinforcement), the model became significantly more stable and accurate despite the tiny dataset.

---
**Author:** Vishwa (IIT BHU)o much better on such a tiny dataset.
