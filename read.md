Value-at-Risk (VaR) Calculator with Lineage Tracking

This Python script calculates Value-at-Risk (VaR) for a portfolio of trades and provides data lineage tracking. It follows a medallion architecture (Bronze, Silver, Gold layers) for data processing and transformation.

Features

*   Data Ingestion (Bronze Layer): Reads trade data from a CSV file.
*   Data Transformation (Silver Layer): Cleans and transforms the data, including flexible date parsing, data type conversion, and handling invalid dates.
*   VaR Calculation (Gold Layer): Calculates VaR at different confidence levels (95%, 97.5% for trades, 95% for portfolio, 99% per book) using the historical method.
*   Data Lineage Tracking: Records the source of the data and all transformations applied at each layer, storing the lineage information in JSON format.
*   Invalid Data Handling: Captures rows with invalid dates and saves them to a separate CSV file for investigation. Includes an error message.
*   Logging: Uses Python's `logging` module for robust logging of events and errors.
*   Command-line Interface: Accepts input and output file paths as command-line arguments.

Requirements

*   Python 3.x
*   Libraries:
    *   pandas
    *   numpy

Setting up a Virtual Environment and Installing Requirements

1.  Create a virtual environment:

    python3 -m venv .venv  # or python -m venv .venv on some systems

2.  Activate the virtual environment:

    *   Linux/macOS:

        source .venv/bin/activate

    *   Windows:

        .venv\Scripts\activate

3.  Install the required packages:

    pip install pandas numpy

    Alternatively, you can create a `requirements.txt` file with the following content:

    pandas
    numpy

    And then install the packages using:

    pip install -r requirements.txt

How to Use

1.  Save the code: Save the provided Python code as a `.py` file (e.g., `var_calculator.py`).

2.  Prepare your input CSV file: Create a CSV file containing your trade data. The file *must* have the following columns (case-sensitive):

    *   `TradeID` (unique identifier for each trade, alphanumeric)
    *   `BookId` (unique identifier for each book, numeric)
    *   `PnLDate` (PnL date, string, various formats are accepted - see below)
    *   `PnL` (Profit and Loss values, float)

    Accepted Date Formats: The `PnLDate` column can accept various date formats, including:

    *   `YYYY-MM-DD` (e.g., 2024-01-15)
    *   `DD/MM/YYYY` (e.g., 15/01/2024)
    *   `MM/DD/YYYY` (e.g., 01/15/2024)
    *   `YYYY-MM-DD HH:MM:SS` (e.g., 2024-01-15 10:30:00)
    *   `DD/MM/YYYY HH:MM:SS` (e.g., 15/01/2024 10:30:00)
    *   `MM/DD/YYYY HH:MM:SS` (e.g., 01/15/2024 10:30:00)

    Example `input.csv`:

    TradeID,BookId,PnLDate,PnL
    T1,1,2023-10-26,100.50
    T2,1,27/10/2023,50
    T3,2,2023-10-26,-20.25
    T4,1,2023-10-27 10:00:00,75
    T5,2,2023-10-28,120
    T6,1,invalid date,120 # Example of an invalid date

3.  Run the script: Open a terminal or command prompt, navigate to the directory where you saved the script and the CSV file, and run the following command:

    python var_calculator.py /path/to/your/input.csv /path/to/your/output/directory

    *   Replace `/path/to/your/input.csv` with the actual path to your input CSV file.
    *   Replace `/path/to/your/output/directory` with the path to the directory where you want the output files to be saved. The script will create this directory if it doesn't exist.

Parameters

The script accepts two command-line arguments:

*   `file_path`: Path to the input CSV file.
*   `output_dir`: Path to the output directory.

Example

python var_calculator.py input.csv output_data

This will read data from `input.csv`, process it, and save the results to the `output_data` directory.

Output Files

The script will generate the following output files in the specified output directory:

*   `bronze_data.csv`: The raw data read from the input CSV.
*   `silver_data.csv`: The transformed data after cleaning and processing (dates converted, data types corrected, invalid dates removed).
*   `gold_PnLDate_sorted.csv`: Portfolio PnL aggregated by date and sorted in ascending order.
*   `gold_trade_var_95%.csv`, `gold_trade_var_97.5%.csv`: Trade VaR at 95% and 97.5% confidence levels.
*   `gold_book_var_95%.csv`, `gold_book_var_99%.csv`: Book VaR at 95% and 99% confidence levels.
*   `lineage.json`: JSON file containing the data lineage information.
*   `invalid_dates.csv`: CSV file containing rows with invalid dates from the input, including an `error_message` column.
*   `var_calculator.log`: Log file containing information about the script's execution, including any errors or warnings.

Logging

The script uses Python's `logging` module to record events and errors. The log file `var_calculator.log` will be created in the same directory where you run the script. This file is crucial for debugging and monitoring the script's behavior in a production environment.  Log levels are set to INFO, so DEBUG messages will not be recorded.

Error Handling

The script includes error handling to catch common issues such as file not found, CSV parsing errors, and other unexpected exceptions. Errors are logged to the `var_calculator.log` file, and a message is printed to the console *only for general errors* to provide immediate feedback. More detailed error information, including tracebacks, is available in the log file.

Data Lineage

The `lineage.json` file provides a record of the data transformations applied at each stage of the process. This is essential for auditability and understanding how the VaR calculations were derived.

Invalid Dates

The `invalid_dates.csv` file contains the rows from the input CSV that had invalid date formats.  This allows you to investigate and correct the invalid dates in your input data. The file will include the original values of `TradeID`, `BookId`, `PnLDate`, and `PnL`, along with a descriptive `error_message`.

Future Add-ons

*   Airflow Integration: The script can be integrated with Apache Airflow to create a robust and scalable data pipeline for VaR calculation. Airflow can handle scheduling, task dependencies, and retries, making the process more automated and reliable.
*   Database Integration: Instead of reading from and writing to CSV files, the script can be modified to interact with a database (e.g., PostgreSQL, MySQL). This would improve performance and data management.
*   Enhanced VaR Models: More sophisticated VaR models can be implemented, such as Monte Carlo simulation or Historical Simulation, to capture more complex risk factors.
*   Reporting and Visualization: The results can be used to generate reports and visualizations to provide insights into the portfolio's risk profile.
*   Parameterization: Add more command-line parameters to control confidence levels, date formats, and other aspects of the calculation.

