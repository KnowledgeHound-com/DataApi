# KnowledgeHound Data Ingestion API Draft

This document defines the draft specification for the KnowledgeHound data ingestion API for survey data.
This document builds on our published authentication and Data API, available at https://knowledgehound.docs.apiary.io/.

## Guiding Principals and Assumptions
- Data sizes can vary significantly by study. Some studies contain a few KB of data, others contain several gigabytes
of content. The first release of this API is optimized for our largest datasets. Future versionsof the API may reduce
complexity for smaller data volumes
- Metadata is exchanged through RFC 7159 compliant JSON. http://www.rfc-editor.org/rfc/rfc7159.txt
- To enable the exchange of large datasets, files are exchanged through an S3 bucket.
- Access to S3 is setup during the onboarding process.

## Datasets
Provides the ability to list, access, create, or modify the datasets this account has access to.

### List Datasets
- URL: `/v2/dataset/`
- Method: `GET`
- Optional Parameters
    - `filter=[string]`
    - `limit=[int:100]`
    - `page=[int:0]`
- Success Response:
```json
{
    "count":  300,
    "pages": 3,
    "next": "url",
    "previous": "url",
    "results": [
        {
            "id": "dataset_id",
            "url": "url",
            "name": "The dataset name",
            "description": "A description of the dataset"
        }
    ]
}
```

### Create a dataset
- URL: `/v2/dataset/{dataset_id}`
- Methods: `POST`
- Post Body:
```json
{
    "source_dataset_id": "source_dataset_id",
    "name": "The dataset name",
    "description": "A description of the dataset (optional)",
    "dimensions":
    {
        "survey": "The relative path to the file in S3 - valid URL",
        "person": "The relative path to the file in S3 - valid URL",
        "survey_response": ["The relative path to the file(s) in S3 - valid URL"]
    },
    "callback_url": "The URL for the success/failure callback- valid URL"
}
```
- Response:
```json
{
  "status": "processing|validation_failure",
  "errors": ["strings"]
}
```
### Modify a dataset
- URL: `/v2/dataset/{dataset_id}`
- Methods: `PUT`
- Post Body:
```json
{
    "source_dataset_id": "source_dataset_id",
    "name": "The dataset name",
    "description": "A description of the dataset (optional)",
    "dimensions":
    {
        "survey": "The relative path to the file in S3 - valid URL",
        "person": "The relative path to the file in S3 - valid URL",
        "survey_response": ["The relative path to the file(s) in S3 - valid URL"]
    },
    "callback_url": "The URL for the success/failure callback- valid URL"
}
```
- Response:
```json
{
  "status": "processing|validation_failure",
  "errors": ["strings"]
}
```

### Get dataset details
- URL: `/v2/dataset/{dataset_id}`
- Methods: `GET`
- Response Body:
```json
{
    "client": "",
    "created": "",
    "created_by": "",
    "modified": "",
    "modified_by": "",
    "source_dataset_id": "source_dataset_id",
    "name": "The dataset name",
    "description": "A description of the dataset",
    "dimensions":
    {
        "survey": "The relative path to the file in S3",
        "person": "The relative path to the file in S3",
        "survey_response": ["The relative path to the file(s) in S3"]
    }
}
```

## Dimension Files
This section of the document provides the file specification for transferring data to KnowledgeHound.
- Files are UTF-8 encoded
- Dates use the W3.org recommended format of YYYY-MM-DDThh:mm:ss.sTZD
    - See: http://www.w3.org/TR/1998/NOTE-datetime-19980827
- Metadata is exchanged through RFC 7159 compliant JSON. http://www.rfc-editor.org/rfc/rfc7159.txt
- Data is exchange through RFC 4180 compliant csv. https://tools.ietf.org/html/rfc4180
    - Remember to handle open ends and other text that may have commas or quotes!
- All files are individually compressed using zip compression
- If the Survey Response dimension file exceeds 2GB decompressed, it should be chunked or partitioned. Each chunk should
ideally be between 200MB and 2GB. Additionally each chunk must be individually compressed and sent as a discrete file. 

### Person Dim
A JSON file containing the list of unique people who have participated in the survey.

If the same person exists in multiple datasets, their ID should remain consistent. Some countries and research may not 
allow for the identification of a person across multiple datasets. In these cases, the ID should be an incrementing 
number that is consistent between the person dimension and the survey response dimension


KnowledgeHound has defined several core attributes about a person. These attributes are all optional and sit at the
root of the person object. Additional attributes may be defined in the attributes object.

```json
{
    "attribute": [
        {
            "id": "The unique ID for the attribute.",
            "name": "The human readable name for the variable",
            "description": "description of the attribute (optional)",
            "visible": "boolean : Defaults to true : Do all users or only admins see the fact",
            "values": [
                {
                    "id": "string: The unique ID for this value",
                    "value": "string",
                    "display_value": "string (optional): If no display text is included, the value field is displayed.",
                    "sort_order": "int (optional): Controls the default ordering in lists and controls. When not sent we apply an alphanumeric sort."
                }
            ]
        }
    ],
    "person": [
        {
            "id": "The unique ID from the source system for this person",
            "first_name": "string (optional)",
            "last_name": "string (optional)",
            "gender": "Male|Female|Non-Binary (optional)",
            "age": "int (optional)",
            "income": "decimal: household income in USD (optional)",
            "household_size": "int (optional)",
            "count_of_children": "int (optional)",
            "marital_status": "divorced|married|separated|single|widowed (optional)",
            "home_ownership": "own|rent|relative (optional)",
            "email": "string (optional)",
            "phone": "string (optional)",
            "postal_code": "string (optional)",
            "country": "string (optional)",
            "attributes": [
                {
                    "id": "The variable used in the question template",
                    "values": ["The values to apply to the person"]
                }
            ]
        }
    ]
}
```

### Survey Dim
A JSON file defining the programing of the survey.

Note: The default_crosstabs, suggested_crosstabs, suggested_filters, and default_filters arrays are all optional. They
may be excluded when not used or sent as empty arrays.
```json
{
    "id": "The unique identifier of the survey",
    "description": "(optional) A description of the survey",
    "type": "Tracker|OneTime|... full list TBD",
    "status": "Created|Live|Completed",
    "start_date": "The datetime the survey began",
    "responses_as_of_date": "The datetime of the most recent survey response.",
    "completion_date": "(optional) The datetime the survey was completed. Not included if the survey is still open.",
    "language": "The ISO 639-2 language code for the default survey language : http://www.loc.gov/standards/iso639-2/php/code_list.php",
    "base_description": "(optional) Textual description of the survey base",
    "base_size": "int (optional): Count to use as base size, this may be different then response_count when applying weighting",
    "response_count": "int: Number of responses in the response file(s)",
    "source_url": "(optional) A url to the original research study in the source system",
    "question_templates": [
        {
            "id": "The unique identifier of the question template",
            "type": "OpenEnd|Categorical|Date|Int|Decimal|Range",
            "categorical_response_options": [
                {
                    "id": "The unique value of the response.",
                    "text": "The displayed response text.",
                    "media_id": "(optional) The id of media content shown."
                }
            ],
            "selection_limit": "(optional) int: The number of permissible values for a categorical question Ex: Pick 1 or Pick 3",
            "text": "The question presented to the user. (Includes variables when used)",
            "display_text": "(optional) If the question contains a variable, this should be the human readable version of the template. If no display text is included, the text field is displayed.",
            "description": "(optional) Any additional information about the question",
            "visible": "boolean (optional): Defaults to true : Do all users or only admins see the question in KnowledgeHound",
            "tags": ["string (optional)"],
            "variables": [
                {
                    "id": "The variable used in the question template",
                    "type": "survey|survey_response|person",
                    "order": "The sequence of the variable"
                }
            ]
        }
    ],
    "question_groupings": [
        {
            "id": "The unique identifier of the question group",
            "description": "(optional)",
            "order": "int",
            "source_url": "(optional) A url to the original question in the source system",
            "variables": [
                {
                    "id": "VariableId",
                    "type": "survey|survey_response|person",
                    "order": "The sequence of the variable"
                }
            ],
            "questions": [
                {
                    "question_template_id": "string: The id of the question template in the question_templates array",
                    "order":  1
                }
            ]
        }
    ],
    "variables": [
        {
            "id": "The variable used in the question template",
            "name": "The human readable name for the variable",
            "description": "(optional)",
            "values": [{
                "value": "string",
                "display_value": "(optional): If no display text is included, the value field is displayed.",
                "sort_order": "int (optional): Controls the default ordering in lists and controls. When not sent we apply an alphanumeric sort."
            }],
            "visible": "boolean (optional): Defaults to true : Do all users or only admins see the variable in KnowledgeHound"
        }
    ],
    "media": [
        {
            "id": "The unique identifier of the media.",
            "relative_path": "The full path to the file in S3",
            "type": "image|video",
            "display_text": "(optional) text to display for accessibility or when the content cannot render"
        }
    ],
    "default_crosstabs": ["(optional) Array of question or variable Ids. (Only the first two are applied)"],
    "suggested_crosstabs": ["(optional) Array of question or variable Ids."],
    "suggested_filters": ["(optional) Array of question or variable Ids."],
    "default_filters": [
        {
            "id": "The unique id of the filter",
            "dim_type": "survey|survey_response|person",
            "filter_id": "The ID of the fact or question response.",
            "type": "equals|not_equals|contains|not_contains",
            "values": ["values"]
        }
    ]
}
```

### Survey Response Dim
A CSV file containing responses to survey questions
- One respondent per line
- If a user wasn't shown a question no empty space should exist between the comas
- The header row must match the following format. `QuestionID_QuestionTemplateId_VariationNumber_ResponseNumber` in the survey dimension
- RFC
