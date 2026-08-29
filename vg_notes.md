Excel for the Web:

- Created a duplicate sheet
- Changed data type of each column, before it was just General
- extracted release_year column using IF and YEAR functions
- explored summary measures on numeric columns to check max/min and outliers, using Sorting and MAX()/MIN()
- also used IF and ABS functions to check that the sales columns correctly summed to the total sales column, which they all do
- applied Filter on all columns to check for spelling inconsistencies, duplicates, miscategorized events, and null values
- (to check for Null values, you can also use COUNTBLANK or COUNTIF)
- also noted with Filter the number/percentage of Null values in each column (Critic score 90%, Sales columns 70%, Release date 10%, Last Update 70%)

- Removed irrelevant columns like "img" and "last_updated"
- Examined duplicate columns with COUNTIFS then deleted duplicate columns with title, console, and release_date
- Standardized the text columns by using PROPER() and TRIM() and checked the columns using IF statement, only a very few like 100 values in each column werern't standardized already
- Formatted release_date as YYYY-MM-DD and found Null entries (keeping them) or tried looking for invalid dates (didn't find any)
- Decided to keep rows with null values in Sales columns and critic_score column
- The Text columns are all full, no Null entries




1. Exploratory Steps (Understanding Data Structure)

Check Column Data Types & Non-Null Counts: Review the auto-detected data types to ensure dates and numbers aren't stored as general text.

Inspect Summary Statistics: Run quick summary measures on numeric columns (critic_score, total_sales, region sales) to identify minimum/maximum values, extreme outliers, or negative sales figures.

Examine Categorical Values: Apply filters on console, genre, publisher, and developer to check for spelling inconsistencies, duplicates, or miscategorized entries.

Identify Missing Data Distributions: Note which fields have heavy missingness (critic_score is ~90% missing; total_sales is ~70% missing) to decide how to handle empty cells during analysis.

2. Data Cleaning Steps (In Priority Order)

Step 1: Remove Irrelevant Columns

Delete unnecessary columns (e.g., img image URLs or last_update timestamps) to declutter the workspace and improve performance in Excel for the Web.

Step 2: Handle Duplicate Entries

Use Data > Remove Duplicates on key primary columns (such as title and console) to remove the ~225 exact/near-duplicate game listings.

Step 3: Standardize Text & Remove Extra Whitespace

Clean leading or trailing spaces in text fields (title, publisher, developer) using =TRIM() in a helper column, then paste over as values.

Standardize casing across categories (e.g., ensuring uniform genre labels).

Step 4: Format & Fix Date Columns

Ensure release_date is formatted explicitly as a Date (YYYY-MM-DD or Short Date format) so Excel handles sorting and year-based grouping correctly.

Filter release_date to identify invalid date strings or missing entries.

Step 5: Address Missing Numerical Values (NaN / Blanks)

Sales Data (total_sales, regional sales): Replace blank cells with 0 if empty fields represent non-recorded/zero sales, or leave them blank/filtered out when calculating averages so missing entries don't skew mean calculations.

Scores (critic_score): Do not fill missing critic scores with 0 (as 0 implies a terrible score). Keep missing scores as blank or label them clearly if categorizing into bins.

Step 6: Handle Missing Categorical Values

Fill the ~17 missing entries in developer with "Unknown" or "N/A" to prevent blank labels in Pivot Table groupings.

Step 7: Validate Cross-Column Math Consistency

Add a check column to verify if regional sales (na_sales + jp_sales + pal_sales + other_sales) equal total_sales where full regional breakdowns exist.








Pandas:

For Pandas, I did similar things as I did in Excel for the Web.
The extra steps not listed below can be found in the notebook.




1. Data Exploration Steps

Load Dataset & Inspect Shape: Load the CSV using pd.read_csv() and run df.shape to see row and column dimensions.

Inspect Column Types & Missing Values: Call df.info() to view non-null counts and memory usage, or df.isna().sum() to pinpoint missing values per column.

Preview Raw Data: Use df.head() and df.tail() to examine the format of entries across features.

Generate Summary Statistics: Use df.describe() for numeric fields (total_sales, critic_score, regional sales) to review min, max, median, and quartile distributions.

Check Categorical Cardinality: Call df['genre'].value_counts() and df['console'].nunique() to identify unique categories and spot potential typos or formatting differences.

2. Data Cleaning Steps

Step 1: Drop Unnecessary Columns

Remove non-essential fields like image paths or system update timestamps:

Python
df = df.drop(columns=['img', 'last_update'])
Step 2: Handle Duplicate Entries

Check for duplicate records on game title and console, then drop them:

Python
df = df.drop_duplicates(subset=['title', 'console'], keep='first')
Step 3: Clean & Standardize String Columns

Strip trailing whitespace from text fields like title, publisher, and developer:

Python
string_cols = ['title', 'publisher', 'developer', 'genre', 'console']
df[string_cols] = df[string_cols].apply(lambda col: col.str.strip())
Step 4: Convert and Format Dates

Convert release_date to datetime format to enable date filtering and year extractions:

Python
df['release_date'] = pd.to_datetime(df['release_date'], errors='coerce')
Step 5: Address Missing Values (NaN)

Sales Figures: Fill missing sales entries with 0.0 or keep them as NaN depending on whether non-listed means zero sales or missing data:

Python
sales_cols = ['total_sales', 'na_sales', 'jp_sales', 'pal_sales', 'other_sales']
df[sales_cols] = df[sales_cols].fillna(0.0)
Categorical Data: Replace missing string entries in fields like developer with a placeholder:

Python
df['developer'] = df['developer'].fillna('Unknown')
Scores: Leave critic_score missing values as NaN to preserve mathematical integrity (imputing 0 skews average calculations).

Step 6: Validate Numerical Logic

Verify that regional sales correctly sum up to total_sales using vectorized operations:

Python
calculated_total = df[['na_sales', 'jp_sales', 'pal_sales', 'other_sales']].sum(axis=1)
# Highlight rows where total sales deviates from regional sum
sales_discrepancies = df[abs(df['total_sales'] - calculated_total) > 0.01]