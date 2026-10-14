# Corporate Sales Record Cleaner (Automated Data Sanitization Pipeline)

- **Author:** Teki Mohit Sai Tulasi
- **Program:** GetIntern.in 8-Week Data Science & Machine Learning Track
- **Phase 1 / Week 1 Milestone Project:** Data Cleaning & EDA

An automated pipeline built with **Pandas** and **NumPy** that ingests raw e-commerce sales data (transactions and customers), removes duplicate records, handles missing values, standardizes text, and exports clean, analysis-ready datasets.

## Repository Contents
| File | Description |
|------|-------------|
| `sales_cleaner.ipynb` | Jupyter Notebook with the full cleaning pipeline |
| `transactions.csv` | Raw transactions data (8,199 rows, 10 columns) |
| `customers.csv` | Raw customers data (1,010 rows, 9 columns) |
| `cleaned_transactions.csv` | Cleaned transactions (8,199 rows) |
| `cleaned_customers.csv` | Cleaned customers (1,000 rows) |
| `cleaned_sales.csv` | Final sanitized sales dataset (copy of cleaned transactions) |
| `README.md` | Project documentation |

## Dataset
Source: *Messy E-commerce Dataset* (Kaggle) - https://www.kaggle.com/datasets/saidnizam/messy-e-commerce-dataset

- **transactions.csv:** `transaction_id, customer_id, transaction_date, product_id, quantity, unit_price, payment_method, discount_applied, transaction_status, review_text`
- **customers.csv:** `customer_id, name, age, gender, state, signup_date, email, phone_number, subscribe`

## Cleaning Steps Taken
1. **Ingestion** - loaded both raw CSV files with `pd.read_csv()` (`phone_number` read as text to preserve leading zeros).
2. **Column tidy** - stripped, lowercased and underscored column names.
3. **Duplicate removal** - audited with `.duplicated().sum()` and removed with `.drop_duplicates()`. Customers had 10 duplicate rows; transactions had none. Also confirmed there are no repeated `transaction_id` or `customer_id` values.
4. **Null audit** - inspected missing values using `df.isnull().sum()`.
5. **Date conversion** - converted `transaction_date` and `signup_date` to datetime.
6. **Missing value imputation** - median for numerical columns, mode or a placeholder for categorical columns (see rationale below).
7. **Type fix** - converted `age` to whole numbers.
8. **Text standardization** - applied `.str.strip()` and `.str.lower()` and collapsed repeated spaces in text columns.
9. **Duplicate re-check** after standardization (0 extra found).
10. **Export** - saved the cleaned CSV files and baseline summary statistics using `.describe()`.

## Null Value Handling Rationale
| File | Column | Missing | Strategy | Reason |
|------|--------|---------|----------|--------|
| transactions | `discount_applied` | 426 | Median (25) | Median is robust to outliers and skewed values |
| transactions | `review_text` | 4,916 (about 60%) | Placeholder `"no review"` | Most customers leave no review; filling with the most common review would invent data |
| customers | `age` | 56 | Median (42) | Median is not distorted by extreme ages |
| customers | `state` | 53 | Mode | Most frequent value keeps the category distribution |

## Before / After Shape Comparison
| File | Raw rows | Cleaned rows | Columns | Raw nulls | Cleaned nulls | Duplicates removed |
|------|----------|--------------|---------|-----------|---------------|--------------------|
| transactions | 8,199 | 8,199 | 10 | 5,342 | 0 | 0 |
| customers | 1,010 | 1,000 | 9 | 110 | 0 | 10 |

## Sample: Raw vs Cleaned
| Column | Raw value | Cleaned value |
|--------|-----------|---------------|
| payment_method | `Credit Card` | `credit card` |
| gender | `Male` | `male` |
| state | `Sulawesi Utara` | `sulawesi utara` |
| review_text | `NaN` | `no review` |
| age | `26.0` or `NaN` | `26` or `42` (median) |

Full raw and cleaned data are in `transactions.csv` / `customers.csv` and the `cleaned_*.csv` files.

## Notes
- `unit_price` contains some very low values (minimum 100 versus a median of about 283,000). They were kept because outlier removal is outside this week's scope.

## How to Run
```bash
pip install pandas numpy jupyter
jupyter notebook sales_cleaner.ipynb
```
Place `transactions.csv` and `customers.csv` in the same folder as the notebook, then run all cells from top to bottom.

## Tech Stack
Python 3, Pandas, NumPy, Jupyter Notebook
