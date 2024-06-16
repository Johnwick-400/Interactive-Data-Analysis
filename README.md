# Interactive Data Analysis

An analysis tool that automates the process of data extraction, cleaning, analysis, and visualization. This tool is built using Python and Streamlit, providing an intuitive web interface for users to upload datasets and receive comprehensive analysis and visualizations.

## Features

- **Data Upload**: Supports CSV, Excel, and JSON file formats.
- **Data Cleaning**: Handles duplicate entries and missing values.
- **Data Analysis**: Provides descriptive statistics and correlation matrix.
- **Data Visualization**: Includes correlation heatmap, histograms, box plots, and pairwise scatter plots.

## Installation

1. **Clone the repository**:
    ```sh
    git clone https://github.com/Johnwick-400/Interactive-Data-Analysis
    cd interactive-data-analysis
    ```

2. **Install the required libraries**:
    ```sh
    pip install -r requirements.txt
    ```

    **`requirements.txt`**:
    ```text
    pandas
    numpy
    matplotlib
    seaborn
    streamlit
    ```

## Usage

1. **Run the Streamlit application**:
    ```sh
    streamlit run app.py
    ```

2. **Open your web browser** and go to `http://localhost:8501`.

3. **Upload your dataset**: Click the "Upload your dataset" button and select your file (CSV, Excel, or JSON).

4. **View the results**: The application will display the raw data, cleaned data, analysis results, and visualizations.

## Code Overview

### Main Application (`app.py`)

```python
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns


sns.set(style="whitegrid")
sns.set_palette("Set2")

def load_data(uploaded_file):
    if uploaded_file.name.endswith('.csv'):
        return pd.read_csv(uploaded_file)
    elif uploaded_file.name.endswith('.xlsx'):
        return pd.read_excel(uploaded_file)
    elif uploaded_file.name.endswith('.json'):
        return pd.read_json(uploaded_file)
    else:
        raise ValueError("Unsupported file format")

def clean_data(df):
    df = df.drop_duplicates()
    df = df.dropna() 
    return df

def analyze_data(df):
    numeric_df = df.select_dtypes(include=['float64', 'int64'])  
    description = numeric_df.describe()
    correlation = numeric_df.corr()  
    
    return {"description": description, "correlation": correlation}

def visualize_data(df):
    numeric_df = df.select_dtypes(include=['float64', 'int64'])  # Select only numeric columns
    
    plt.figure(figsize=(10, 6))
    sns.heatmap(numeric_df.corr(), annot=True, cmap='coolwarm', linewidths=0.5, linecolor='black')
    plt.title('Correlation Matrix', fontsize=16)
    st.pyplot(plt)
    
   
    st.write("### Histograms")
    for column in numeric_df.columns:
        plt.figure(figsize=(10, 6))
        sns.histplot(numeric_df[column], kde=True, color='skyblue', edgecolor='black')
        plt.title(f'Histogram of {column}', fontsize=14)
        plt.xlabel(column, fontsize=12)
        plt.ylabel('Frequency', fontsize=12)
        st.pyplot(plt)
    
    
    st.write("### Box Plots")
    for column in numeric_df.columns:
        plt.figure(figsize=(10, 6))
        sns.boxplot(x=numeric_df[column], color='lightgreen')
        plt.title(f'Box Plot of {column}', fontsize=14)
        plt.xlabel(column, fontsize=12)
        st.pyplot(plt)
    
    st.write("### Pairwise Scatter Plots")
    if len(numeric_df.columns) > 1:
        sns.pairplot(numeric_df, diag_kind='kde', plot_kws={'alpha': 0.7, 's': 80, 'edgecolor': 'k'}, palette='Set2')
        plt.suptitle('Pairwise Scatter Plots', y=1.02, fontsize=16)
        st.pyplot(plt)

def main():
    st.title("Interactive  Data Analysis")
    
    uploaded_file = st.file_uploader("Upload your dataset", type=["csv", "xlsx", "json"])
    if uploaded_file is not None:
        df = load_data(uploaded_file)
        st.write("### Raw Data", df)
        
        df_cleaned = clean_data(df)
        st.write("### Cleaned Data", df_cleaned)
        
        analysis_results = analyze_data(df_cleaned)
        st.write("### Analysis Results")
        st.write("#### Descriptive Statistics", analysis_results["description"])
        st.write("#### Correlation Matrix", analysis_results["correlation"])
        
        st.write("### Visualizations")
        visualize_data(df_cleaned)

if __name__ == "__main__":
    main()
