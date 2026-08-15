# Predicting Gene Expression Using Perturb-seq Data

This project was developed for a hackathon on predicting gene expression from Perturb-seq data.
The goal was to predict the gene expression profiles of double-gene perturbationsusing expression profiles from the corresponding single-gene perturbations. The training data contained both single perturbations (`gene + ctrl`) and known double perturbations (`gene A + gene B`), with multiple experimental replicates for some perturbations.
We first averaged replicate measurements to obtain one expression profile for each perturbation. We then used the corresponding single-perturbation profiles to predict the expression profile of a double perturbation.
We explored a scaled additive baseline and an XGBoost model with an additional interaction feature (`x1 * x2`) to capture potential non-additive effects between two perturbations. Because the number of training double perturbations was limited relative to the number of output genes, XGBoost was applied to the most variable genes, while the scaled additive model was used for the remaining genes.

## Pipeline

```text
Raw train_set.csv
        ↓
Identify duplicate perturbation columns
        ↓
Average experimental replicates
        ↓
Separate single and double perturbations
        ↓
Construct features: [x1, x2, x1 × x2]
        ↓
XGBoost + scaled additive baseline
        ↓
Predict unseen double perturbations
```

## Prediction Model

The final prediction combines XGBoost with the scaled additive baseline:

$$
\hat{y} =
\begin{cases}
\mathrm{XGBoost}(x_1, x_2, x_1 \odot x_2), & \text{top-200 variable genes} \\
s_{\mathrm{global}}(x_1+x_2), & \text{remaining 800 genes}
\end{cases}
$$

where $x_1$ and $x_2$ are the expression profiles of the two corresponding single-gene perturbations, and $x_1 \odot x_2$ is their element-wise interaction.
