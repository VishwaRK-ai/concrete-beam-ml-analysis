# concrete-beam-ml-analysis
predicting concrete beam structural targets using physics-informed machine learning and 2-stage OOF stacking. hit 0.836 R2 on kaggle with just 93 training rows.
Beam Structural Performance Prediction (0.836 R2)
Predicting concrete beam structural properties using physics-informed machine learning and 2-stage OOF stacking.

Introduction The goal of this projectis to take a small dataset of 117 beam samples and predict three key structural targets: depth (d), reinforcement density (r), and ultimate load (uil). the data is so small ,only 93 rows for training.
Initial Baseline Results Started by running a standard Random Forest model just to see where we stood. It did okay on the ultimate load (uil) because that has a very strong linear relationship with the span, but it completely failed on the reinforcement (r) and the depth (d).
Initial Mean R2: ~0.49

The Problem: The reinforcement density (r) is very non-linear and jumpy. The baseline model was basically guessing randomly for that column, which dragged down the whole score.

Feature Engineering & Physics Injection I added based on structural mechanics to help the model "see" the relationships:
Bending Moment (M): I calculated M=uaudl×le 
2
 . In engineering, the thickness of a beam is directly related to the square of the span, so adding this helped the model with the stress.

Load Intensity: Added a feature for load per unit length to help with the shear stress mapping.

Standardizing: Used RobustScaler instead of a normal scaler because it ignores outliers. In structural data, if one beam has a massive load, it can ruin the math for all the other normal beams.

The Stacking Architecture
The jump from 0.49 to 0.83 came from a "Two-Stage Stacking" approach. Instead of predicting everything at once, I broke it into steps:

Stage 1 (Depth Prediction): I used an ensemble of XGBoost, Random Forest, and Ridge Regression to predict the depth (d) first. I used 5 Fold (OOF) predictions. This is critical because it means the model is predicting on data it hasn't seen, which simulates a real-world test.

Stage 2 (Advanced Features): I took those predicted depth values and calculated the Section Area (w×d) and Stiffness (w×d 
3
 /12).

Final Prediction: I fed these new "advanced" physics features back into the final model to predict the reinforcement (r) and ultimate load (uil).

Results
Baseline: 0.49 R2

Final Model: 0.836 R2

By predicting depth first and then using that to calculate stiffness, we basically forced the AI to follow the same logic a structural engineer would use, which is why it performed so much better on such a tiny dataset.
