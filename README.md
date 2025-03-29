
---

## **🚀 Overview of the ETL Pipeline**
This ETL pipeline is designed to **process and analyze movie ratings** from IMDb using AWS services. It ensures **data quality** before storing it in **Amazon Redshift** for further analysis.

### **🛠️ AWS Services Used**
1. **Amazon S3** – Used as a **data lake** for storing raw and processed data.  
2. **AWS Glue Crawler** – Scans and extracts metadata from S3 datasets.  
3. **AWS Glue Catalog** – Manages metadata and schema information.  
4. **AWS Glue Data Quality** – Performs data validation checks.  
5. **Amazon Redshift** – Stores transformed data for querying and analysis.  
6. **Amazon EventBridge** – Triggers automated workflows.  
7. **Amazon SNS (Simple Notification Service)** – Sends email notifications about data quality results.  

---

## **📌 ETL Pipeline Architecture**
![architecture](https://github.com/user-attachments/assets/e6bb36d0-38fd-4add-919a-15de8d84009e)

 

The pipeline follows these steps:
1. **Data Ingestion**: Movie rating data (`imdb_movies_rating.csv`) is uploaded to **Amazon S3**.
2. **Metadata Extraction**: AWS Glue Crawler creates metadata tables from the S3 dataset.
3. **Data Quality Validation**: Rules are applied to filter quality movies (IMDb rating **≥ 8.5**).
4. **Data Transformation**: Filtered data is structured and **loaded into Amazon Redshift**.
5. **Error Handling**: Failed records are stored in **S3 (bad_records directory)**.
6. **Automation & Notification**: **EventBridge + SNS** sends an email when data processing is completed.

---

## **🔄 Step-by-Step Implementation of the ETL Pipeline**

### **Step 1: Create an S3 Bucket & Upload Data**
- **Create an S3 bucket**: `movie-data-analysis-storage`
- **Upload dataset**: `imdb_movies_rating.csv`

📌 **Result:** Data is now stored and ready for processing.

---

### **Step 2: Create an AWS Glue Crawler**
- **Create a database**: `movie-data`
- **Define a crawler**: `crawl-movie-dataset`
- **Scan S3 storage** to generate metadata tables.

📌 **Result:** A metadata table is created under `movie-data` database.

---

### **Step 3: Perform Data Quality Checks**
- **Navigate to the metadata table** (`imdb_movies_rating_csv`).
- **Define validation rules**:
  - Ensure `imdb-rating` is **present**.
  - Validate that ratings are **between 8.5 and 10.3**.
- **Save as `movie_data_quality_check`**.
- **Run quality checks** and store results in **S3 (`historical-data-rule-outcome` folder)**.

📌 **Result:** Quality issues are identified before transformation.

---

### **Step 4: Load Processed Data into Amazon Redshift**
- **Create a schema**: `movies`
- **Define a table**: `movies-imdb-movies-rating`
- **Run Glue Crawler** to create a **Redshift metadata table**: `redshift_dev_movies_imdb_movies_rating`

📌 **Result:** Redshift is now ready to store transformed data.

---

### **Step 5: Build the AWS Glue ETL Job**
1. **Define source**: `S3-data-source` (AWS Glue Catalog).
2. **Apply Data Quality Checks**:
   - **Publish results to CloudWatch**.
   - **Add a column** indicating whether a record passes or fails validation.
   - Store failed records in **S3 (`bad_records` directory)**.
3. **Filter and Transform Data**:
   - **Failed records** → Stored in S3 (`bad_records`).
   - **Valid records** → Schema adjusted for Redshift.
4. **Load into Redshift**:
   - Store valid data into **Amazon Redshift (`movies-imdb-movies-rating` table)**.
5. **Set up Glue Job Execution**:
   - Allocate **2 workers** for processing.
   - Configure **VPCs** for Glue and CloudWatch.

📌 **Result:** **Out of 1000 records, only 33 high-quality movies are retained in Redshift**.

---

### **Step 6: Automate Notifications Using AWS EventBridge + SNS**
1. **Create an EventBridge Rule**: `MovieDQCheckStatus_Rule`
   - Trigger when **data quality checks complete**.
   - Capture both **success and failure states**.
2. **Integrate with SNS**:
   - Create an **SNS topic**.
   - Add an **email subscriber**.
   - Notify users when the ETL process is complete.

📌 **Result:** Users receive an **email notification** on the **status of data processing**.

---

## **📊 Data Flow Summary**
| **Step** | **Task** | **Service Used** | **Output** |
|----------|---------|------------------|------------|
| 1 | Upload IMDb dataset | Amazon S3 | `movie-data-analysis-storage` |
| 2 | Generate metadata | AWS Glue Crawler | `movie-data` database |
| 3 | Apply validation rules | AWS Glue Data Quality | **Filtered movies** |
| 4 | Store bad records | Amazon S3 | `bad_records` directory |
| 5 | Load valid records | Amazon Redshift | `movies-imdb-movies-rating` |
| 6 | Automate workflow | EventBridge + SNS | **Email notifications** |

---

## **🎯 Key Benefits of This Pipeline**
✅ **Automated Data Processing** – Fully managed AWS Glue jobs streamline data transformation.  
✅ **Data Quality Enforcement** – Ensures only high-rated movies are stored for analysis.  
✅ **Scalability & Flexibility** – Can handle large-scale datasets with AWS Redshift.  
✅ **Real-time Monitoring** – CloudWatch & SNS provide instant alerts.  

---

## **📌 Next Steps & Enhancements**
1. **Optimize Redshift Queries** – Create materialized views for faster analysis.  
2. **Integrate with Amazon Athena** – Query **bad records** stored in S3.  
3. **Use Lambda for Automation** – Improve error handling and data validation.  
4. **Enhance Reporting** – Use **AWS QuickSight** for interactive dashboards.  

---

## **🌟 Visual Representation of ETL Flow**
**1️⃣ Data Ingestion** → **2️⃣ Metadata Extraction** → **3️⃣ Data Quality Validation** → **4️⃣ Data Transformation** → **5️⃣ Load into Redshift** → **6️⃣ Automated Alerts**

🔽 **High-Quality Movies** ✅ → **Stored in Redshift**  
🔽 **Low-Quality Movies** ❌ → **Stored in S3 (bad_records)**  

---

## **📝 Final Thoughts**
This **ETL pipeline ensures data integrity** while leveraging **AWS services** for efficient processing. With **automation, validation, and scalability**, it offers a **robust data workflow for movie analytics**.


