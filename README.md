# **etl-talend-studio-example**
This is a example of ETL task using Talend Studio, Java, MySQL

---
### **Context Description**
- 2 database **Olist** and **NewMart**. The goal is to detect any update in table **olist.olist_customers_dataset** of customer_number list in NewMart db, extract all information related to the customer_number list from 4 Olist tables, convert information to JSON file output. 
 
---
### **Result Example** 

- table newmart.cif_client

| client_id |
| --- |
| 060e732b5b29e8181a18229c7b0b2b5e |

- table olist.olist_customers_dataset

| customer_unique_id |
| --- |
| 060e732b5b29e8181a18229c7b0b2b5e |
| 290c77bc529b7ac935b93aa66c333dc3 |

- Today **olist.olist_customers_dataset** has 2 update **060e732b5b29e8181a18229c7b0b2b5e, 290c77bc529b7ac935b93aa66c333dc3**

- Talend job will detect these updates and convert information of id **060e732b5b29e8181a18229c7b0b2b5e** to JSON file with new mapping:
```
[ {
  "customer_number" : "060e732b5b29e8181a18229c7b0b2b5e",
  "oder_info" : [ {
    "order_id" : "5741ea1f91b5fbab2bd2dc653a5b5099",
    "product_id" : "0be701e03657109a8a4d5168122777fb",
    "price" : "259.90",
    "review_id" : "9a6614162d285301aa3ef6de4be75265",
    "review_score" : "5",
    "review_content" : ":Loja responsÃ¡vel"
  }, {
    "order_id" : "98b737f8bd00d73d9f61f7344aadf717",
    "product_id" : "223d34a3d9334039f5ff9511dc044bbb",
    "price" : "246.62",
    "review_id" : "fd0e493eac47b2e64aec60efcb2b3dc2",
    "review_score" : "5",
    "review_content" : ":Produtos de primeira linha."
  } ]
} ]
```

---
### **Job Flow** 
- Talend Repository layout

![Talend Repository Layout](https://github.com/IcedTea0000/etl-talend-studio-example/blob/master/document_img/repository_layout.JPG)

- Setup environment: import tJSONDoc custom component into Talend Studio. You can download this component from Talend Exchange. The Database ddl file and data is stored in ..\setup_environment. Run job import_data to import data from csv files to database.
![Talend Import Data](https://github.com/IcedTea0000/etl-talend-studio-example/blob/master/document_img/job_import_data.JPG)

- Run extract_cif job to extract the client_id list from newmart.cif_client. Output is csv file contains list of client_id to track update in ..output\bk
![Talend Extract Cif](https://github.com/IcedTea0000/etl-talend-studio-example/blob/master/document_img/job_extract_cif.JPG)

- Run extract_customer job to extract a list of customer_unique_id that has update record today and in client_id list. This job uses MD5 to hash the record to compare with yesterday data to detect changes.
![Talend Extract Customers](https://github.com/IcedTea0000/etl-talend-studio-example/blob/master/document_img/job_extract_customers.JPG)

- Run extract_json job to pull all information of customer_unique_id list that has update from Olist db. Convert the returned records into JSON objects and write to file.
![Talend Extract JSON](https://github.com/IcedTea0000/etl-talend-studio-example/blob/master/document_img/job_extract_json.JPG)
