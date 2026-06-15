import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load Dataset
df = pd.read_csv("dataset.csv")

# -------------------------------
# Basic Information
# -------------------------------
print("First 5 Rows:")
print(df.head())

print("\nDataset Shape:")
print(df.shape)

print("\nColumn Names:")
print(df.columns)

print("\nData Types:")
print(df.dtypes)

print("\nMissing Values:")
print(df.isnull().sum())

print("\nStatistical Summary:")
print(df.describe())

# -------------------------------
# Data Cleaning
# -------------------------------
# Remove duplicate rows
df = df.drop_duplicates()

# Fill missing numerical values with median
num_cols = df.select_dtypes(include=np.number).columns
for col in num_cols:
    df[col].fillna(df[col].median(), inplace=True)

# Fill missing categorical values with mode
cat_cols = df.select_dtypes(include='object').columns
for col in cat_cols:
    df[col].fillna(df[col].mode()[0], inplace=True)

# -------------------------------
# Univariate Analysis
# -------------------------------

for col in num_cols:
    plt.figure(figsize=(6,4))
    sns.histplot(df[col], kde=True)
    plt.title(f"Distribution of {col}")
    plt.show()

# -------------------------------
# Correlation Analysis
# -------------------------------

plt.figure(figsize=(10,8))
sns.heatmap(df[num_cols].corr(),
            annot=True,
            cmap="coolwarm",
            fmt=".2f")
plt.title("Correlation Matrix")
plt.show()

# -------------------------------
# Boxplots for Outliers
# -------------------------------

for col in num_cols:
    plt.figure(figsize=(6,4))
    sns.boxplot(x=df[col])
    plt.title(f"Boxplot of {col}")
    plt.show()

# -------------------------------
# Pairplot
# -------------------------------

sns.pairplot(df[num_cols])
plt.show()

# -------------------------------
# Insights
# -------------------------------

print("\nEDA Completed Successfully!")
print("Check visualizations for patterns, trends, correlations, and outliers.")
