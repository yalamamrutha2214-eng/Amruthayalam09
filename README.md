# Import Libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load Dataset
# Replace 'data.csv' with your dataset file name
df = pd.read_csv('data.csv')

# Display First 5 Rows
print("First 5 Rows:")
print(df.head())

# Dataset Information
print("\nDataset Info:")
print(df.info())

# Check Missing Values
print("\nMissing Values:")
print(df.isnull().sum())

# Fill Missing Numeric Values with Mean
numeric_cols = df.select_dtypes(include=np.number).columns
df[numeric_cols] = df[numeric_cols].fillna(df[numeric_cols].mean())

# Remove Duplicate Rows
df.drop_duplicates(inplace=True)

# Detect Outliers using IQR Method
for col in numeric_cols:
    Q1 = df[col].quantile(0.25)
    Q3 = df[col].quantile(0.75)
    IQR = Q3 - Q1

    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR

    df = df[(df[col] >= lower) & (df[col] <= upper)]

print("\nCleaned Dataset Shape:", df.shape)

# Save Cleaned Dataset
df.to_csv("cleaned_data.csv", index=False)

# ---------------- VISUALIZATION ---------------- #

# Histogram
plt.figure(figsize=(8,5))
df[numeric_cols[0]].hist(bins=20)
plt.title(f"Distribution of {numeric_cols[0]}")
plt.xlabel(numeric_cols[0])
plt.ylabel("Frequency")
plt.show()

# Correlation Heatmap
plt.figure(figsize=(10,6))
sns.heatmap(df[numeric_cols].corr(),
            annot=True,
            cmap="coolwarm")
plt.title("Correlation Heatmap")
plt.show()

# Boxplot for Outlier Check
plt.figure(figsize=(8,5))
sns.boxplot(data=df[numeric_cols])
plt.title("Boxplot of Numeric Columns")
plt.show()

# Pairplot
sns.pairplot(df[numeric_cols])
plt.show()

# Summary Statistics
print("\nSummary Statistics:")
print(df.describe())
