# APA Style Report

## Introduction

This report investigates the effect of different treatments on plant growth. The dataset used in this analysis contains the weight of plants under three different conditions: control (ctrl), treatment 1 (trt1), and treatment 2 (trt2). The analysis includes checking assumptions for One-Way Between-Groups ANOVA, performing the ANOVA test, and conducting post-hoc analysis using Tukey's HSD test.

## Data Preparation

The dataset was loaded and divided into three groups based on the treatment conditions:

```python
import pandas as pd

df = pd.read_csv('plantgrowth.csv')

cntrl = df[df['group'] == 'ctrl']
trt1 = df[df['group'] == 'trt1']
trt2 = df[df['group'] == 'trt2']
```

## Assumptions of One-Way Between-Groups ANOVA

### Assumption 1: Dependent Variable Measurement Level

The dependent variable, plant weight, is measured at a continuous level. The `weight` column in the dataset contains floating-point numbers, indicating that the measurement is continuous.

### Assumption 2: Independent Variable Groups

The independent variable consists of three categorical, independent groups: control (ctrl), treatment 1 (trt1), and treatment 2 (trt2).

### Assumption 3: Independence of Observations

The observations are independent, meaning there is no relationship between the observations in each group.

### Assumption 4: No Significant Outliers

Outliers can significantly impact the results of ANOVA. A boxplot was used to identify outliers:

```python
df.boxplot(column='weight', by='group', grid=False)
```

A significant outlier was identified in the `weight` column. Winsorization was applied to reduce the impact of the outlier:

```python
from scipy.stats.mstats import winsorize

df['weight_winsorized'] = winsorize(df['weight'], limits=[0.05, 0.05])
df.boxplot(column='weight_winsorized', by='group', grid=False)
```

### Assumption 5: Normality

The Shapiro-Wilk test was performed to check for normality:

```python
from scipy.stats import shapiro

stat_ctrl, p_value_ctrl = shapiro(cntrl['weight'])
stat_trt1, p_value_trt1 = shapiro(trt1['weight'])
stat_trt2, p_value_trt2 = shapiro(trt2['weight'])

print(f'Control Group - Statistic: {stat_ctrl}, p-value: {p_value_ctrl}')
print(f'Treatment 1 Group - Statistic: {stat_trt1}, p-value: {p_value_trt1}')
print(f'Treatment 2 Group - Statistic: {stat_trt2}, p-value: {p_value_trt2}')
```

The p-values obtained from the Shapiro-Wilk test were greater than 0.05, indicating that the weight data does not significantly deviate from a normal distribution.

### Assumption 6: Homogeneity of Variances

Levene's test was performed to check for homogeneity of variances:

```python
from scipy.stats import levene

stat, p_value = levene(cntrl['weight'], trt1['weight'], trt2['weight'])

print(f'Statistic: {stat}, p-value: {p_value}')
```

The p-value obtained from Levene's test was greater than 0.05, indicating that the variances are not significantly different across the groups.

## One-Way Between-Groups ANOVA

A one-way ANOVA was conducted to compare the effect of different treatments on plant growth:

```python
from scipy.stats import f_oneway

stat, p_value = f_oneway(cntrl['weight'], trt1['weight'], trt2['weight'])

print(f'Statistic: {stat}, p-value: {p_value}')
```

The ANOVA test successfully rejected the null hypothesis, indicating a significant difference between the means of the groups.

## Post-Hoc Analysis

Tukey's HSD test was performed to determine which groups differ from each other:

```python
from statsmodels.stats.multicomp import pairwise_tukeyhsd
from scipy.stats import kruskal

tukey = pairwise_tukeyhsd(endog=df['weight'], groups=df['group'], alpha=0.05)

print(tukey)
```

## Conclusion

Both the non-parametric test (Kruskal-Wallis test) and the parametric test (One-Way ANOVA) successfully rejected the null hypothesis, indicating a significant difference between the means of the groups. The post-hoc analysis using Tukey's HSD test further identified the specific groups that differ from each other.
## Non-Parametric Test: Kruskal-Wallis Test

Due to the presence of significant outliers, a Kruskal-Wallis test was performed as a non-parametric alternative to ANOVA:


stat, p_value = kruskal(cntrl['weight'], trt1['weight'], trt2['weight'])

print(f'Statistic: {stat}, p-value: {p_value}')

The Kruskal-Wallis test also rejected the null hypothesis, indicating a significant difference between the groups.
