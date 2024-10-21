# google_metadata_examples

Simple examples of how to get and extract a Data Catalog Tag for a BigQuery dataset/table and add it as a Dataplex Catalog Aspect.

Assumes:

- we are only dealing with BigQuery objects (e.g, dataset or table)
- one DataCatalog Tag Template in use
- we have a Dataplex Catalog Aspect Type that has the same required fields as the DataCatalog Tag with an equivalent type (e.g, strings and richText → Text,  Doubles → Integer or Double, etc,). Note that there are more types in Dataplex

Python code using the Google Python clients is given in the metatdata.py file

### Getting the Data Catalog Entry ID
- the key API  endpoint: https://cloud.google.com/data-catalog/docs/reference/rest/v1/catalog/search
- the key Python client method here is the search_catalog  https://cloud.google.com/python/docs/reference/datacatalog/latest/google.cloud.datacatalog_v1.services.data_catalog.DataCatalogClient#google_cloud_datacatalog_v1_services_data_catalog_DataCatalogClient_search_catalog

### Getting the Entry Tags
If you have a Data Catalog Entry ID you can then get the tags attached to this entry.

- the appropriate API endpoint is https://cloud.google.com/data-catalog/docs/reference/rest/v1/projects.locations.entryGroups.tags/list
- this is achieved using the Pythob Client list tags method https://cloud.google.com/python/docs/reference/datacatalog/latest/google.cloud.datacatalog_v1.services.data_catalog.DataCatalogClient#google_cloud_datacatalog_v1_services_data_catalog_DataCatalogClient_list_tags

### Extracting the Tag field values
Extract the Tag fiels values and format them to {key:value} format ready for Dataplex. 
We assume that an Aspect Type (a metadata template) exists that has the same name fields and same types as the Data Catalog tag Template - if not then the previous wrangling step should prepare the metadata dict/JSON so that it matches the Aspect Type format.

### Add an Aspect

- key API endpoint:  https://cloud.google.com/dataplex/docs/reference/rest/v1/projects.locations.entryGroups.entries/patch
- key method UpdateEntryRequest https://cloud.google.com/python/docs/reference/dataplex/latest/google.cloud.dataplex_v1.services.catalog_service.CatalogServiceClient#google_cloud_dataplex_v1_services_catalog_service_CatalogServiceClient_update_entry


## Example use

```
from metadata import data_catalog_search_for_bq_entry, 

bq_object_name = "sandbox-generic-gcp-analysis.example_data.pokemon"
data_catalog_tag_template = "projects/sandbox-pughd/locations/europe-west2/tagTemplates/catd_sandbox_testing_tag_template"
dataplex_aspect_type = "sandbox-generic-gcp-analysis.europe-west2.tag-copy-test"

# Get Data Catalog entry_id
entry_id = data_catalog_search_for_bq_entry(bq_object_name)

# Get the Data Catalog Tags
list_tag_response = get_data_catalog_entry_tag(entry_id)
 
# Extract the tag values in dict format
tag_fields = extract_data_catalog_tag(list_tag_response, data_catalog_tag_template)
 
# use these to add as a Aspect
add_dataplex_catalog_aspect_to_entry(bq_object_name, dataplex_aspect_type, tag_fields)


```
