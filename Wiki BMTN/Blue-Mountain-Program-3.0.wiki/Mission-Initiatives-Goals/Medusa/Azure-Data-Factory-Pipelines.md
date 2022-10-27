# Overview
Azure Data Factory (ADF) is the service used to process and ingest data into the database solution. Some notes on the terminology used here:
- Pipelines are the central containers for processing ADF data flows. 
- Pipelines contain activities which reference data sets and are executed using triggers. 
- The copy activity is used in the two pipelines in the solution, and simply copies data from the source dataset to the target, or sink, dataset based on the identified mappings. 

There are two pipelines which were deployed for this effort
1. plBikeXML - Processing of the S1000DBIKE xml data
2. plCopyPDF_TaskOrder - Processing of ETIMS PDF metadata


## Pipelines
Pipeline: **plBikeXML**
The pipeline for the S1000DBIKE data consists of a single copy activity to copy data from the source XML file into a structured SQL table. 
Activity: **Copy data** 
- Name: XML to SQL DB
Source: dsInputXML
Sourcefilename: @pipeline().parameters.dssourcefile
Sink: dsSqlXMLBikeTable
Mappings:  one to one column mapping
			
Parameters
- dssourcefile - Type: string Default value: DMC-S1000DBIKE-AAA-D00-00-00-00AA-00PA-D_005-00_EN-US.XML

 

Pipeline: **plCopyPDF_TaskOrder**
The pipeline for the ETIMS Task Order PDF data consists of a single copy activity to copy data from the json created from the call to the Azure function into a structured SQL table.

Activity: **Copy data** 
- Name: Copy data1
Source: dsRestPDFtoJSON
Urlstring: @concat(pipeline().parameters.sourcefolder, pipeline().parameters.sourcefile)
Request Method: Post
 Sink: dsPDFJSONTable
Mappings:  one to one column mapping

Parameters
- sourcefile
 type: string
Default value: ETIM%20Sample%20-%201%20page.pdf
Sourcefolder
type: string
Default value: https://stpublictest.blob.core.usgovcloudapi.net/input/PDF/ 

## Triggers
**trBlobXML** - this trigger is based on the event of an XML file being created in the specified blob container. 

- Triggers on 
Event: Blob created 
Container name: input 
Blob path begins with: XML/ 
Blob path ends with: .XML 


**trgInputPDFCreated** - this trigger is based on the event of a PDF file being created in the specified blob container. 

- Triggers on 
Event: Blob created 
Container name: input 
Blob path begins with: PDF/ 
Blob path ends with: .pdf 


## Datasets
- dsInputXML -  source dataset for S1000DBIKE xml data
- dsPDFJSONTable - sink for data extracted from ETIMS PDFs
- dsRestPDFtoJSON - source dataset for ETIMS PDFs
- dsSqlXMLBikeTable - sink for S1000DBIKE xml data

## Linked Service
- lsBlob - linked to blob storage containers in storage account stdatafusiondev001 
- lsSqldb - linked to SaaS SQL Server instance sqldb-datafusion-dev
- lsRestService1 - linked to Azure function  https://func-datafusion-dev-001.azurewebsites.us




 

