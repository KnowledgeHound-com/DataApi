# KnowledgeHound Data Ingestion API

This document defines the specification for the KnowledgeHound data ingestion API for survey data.
This document builds on our published authentication and Data API, available at https://knowledgehound.docs.apiary.io/.

## Guiding Principles and Assumptions
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
- URL: `/v2/dataset/`
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
  "dataset_id": "<knowledgehound id>"
}
```
- Callback:
```json
{
  "dataset": "<knowledgehound id>",
  "success": true,
  "exceptions": ["error/exception messages", "..."]
} 
```
### Modify a dataset
- This API accepts the full JSON dataset specification and supports updating dimensions only, 
other field updates will be supported soon.
- URL: `/v2/dataset/{source_dataset_id}` (or deprecated `/v2/dataset/{dataset_id}`)
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
  "dataset_id": "<knowledgehound id>"
}
```

### Get dataset details
- URL: `/v2/dataset/{source_dataset_id}` (or deprecated `/v2/dataset/{dataset_id}`)
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
- Data is exchanged through RFC 4180 compliant csv. https://tools.ietf.org/html/rfc4180
    - Remember to handle open ends and other text that may have commas or quotes!
- All files are individually compressed using zip compression
- If the Survey Response dimension file exceeds 2GB decompressed, it should be chunked or partitioned. Each chunk should
ideally be between 200MB and 2GB. Additionally each chunk must be individually compressed and sent as a discrete file. 

### Person Dimension
A JSON file containing the list of unique people who have participated in the survey.

If the same person exists in multiple datasets, their ID should remain consistent. Some countries and research may not 
allow for the identification of a person across multiple datasets. In these cases, the ID should be an incrementing 
value that is consistent between the person dimension and the survey response dimension.


KnowledgeHound has defined several core attributes about a person. These attributes are all optional and sit at the
root of the person object. In the future, custom attributes will be supported in the 'attribute' object.


Where a person attribute is listed as having multiple types such as "decimal or string", they will first be parsed as
a numeric type and default to a string to allow support for individual values and ranges. All person entries must be
type consistent. Mixing numerics or strings in response data for the same field will cause errors.

```json
{
    "attribute": [
      {"to be defined in the future": null}
    ],
    "person": [
        {
            "id": "The unique ID from the source system for this person",
            "first_name": "string (optional) - not currently imported for PII protection",
            "last_name": "string (optional) - not currently imported for PII protection",
            "gender": "Male|Female|Non-Binary (optional)",
            "age": "int (optional)",
            "income": "int (optional) personal income (optional)",
            "household_income": "int (optional) household income  (optional)",
            "household_size": "int (optional)",
            "count_of_children": "int (optional)",
            "marital_status": "divorced|married|separated|single|widowed (optional)",
            "home_ownership": "own|rent|relative (optional)",
            "email": "string (optional) - not currently imported for PII protection",
            "phone": "string (optional) - not currently imported for PII protection",
            "city": "string (optional)",
            "region": "string (optional)",
            "county": "string (optional)",
            "state": "string (optional)",
            "postal_code": "string (optional)",
            "country": "string (optional)",
            "career": "string (optional)",
            "education_level": "string (optional)",
            "ethnicity": "string (optional)",
            "political_affiliation": "string (optional)",
            "employment_status": "string (optional)",
            "religion": "string (optional)",
            "attributes": [
              {"to be defined in the future": null}
            ]
        }
    ]
}
```

### Survey Dimension
A JSON file defining the programing of the survey.

The Survey dimension supports specifying a default weighting variable, using the `default_weighting` and `weighting_label`
fields on a question template. Both fields are optional, but if used, only one template should be marked as such.
Only a numeric question can be used for this purpose - either types `Int` or `Decimal`. Use `weighting_label` to
provide a more concise identifier for the question - for example, if the question is "How old are you?", consider using
"Respondent Age" as the `weighting_label` if selecting the question for default weighting.

The Survey dimension also supports a form of hierarchical generic metadata entry that can be used to feed KnowledgeHound's
flexible survey metadata. This field can be omitted or left as an empty object `"metadata": {}` if not desired.

NOTE: At this time, metadata using this method can only be set at first-time Data API survey creation, not during updates.

To set metadata fields, include `metadata` as an object containing the desired metadata field name as a key and the field
value as a value. You can specify multiple fields:

```
"metadata": {
    "field_1": "value_1",
    "field_2": "value_2"
}
```

KnowledgeHound supports hierarchical metadata fields. You can specify fields of this kind by nesting objects and using the key
of the nested object as the parent metadata field value, and the value of the object as the child metadata field value.
For example, you can specify a field named `FruitType` with a parent value of `Berry` and a child value of `Strawberry` as follows:

```
"metadata": {
    "FruitType": {
        "Berry": "Strawberry"
    }
}
```

This would allow the study in KnowledgeHound to be tagged with both Berry and Strawberry categories that can be used as filters. You can also continue nesting, as well as specify other fields:

```
"metadata": {
    "Sweetness": "High",
    "FoodType": {
        "Fruit": {
            "Citrus": "Orange"
        }
    }
}
```
### Schema

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
    "source_display_text": "(optional) A text snippet to use as a display replacement for the source URL in KnowledgeHound",
    "metadata": {
        "metadata_field_1": "(optional) A set of field-value entries that can be set as flexible metadata in KnowledgeHound"
    },
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
            "default_weighting": "boolean (optional): Defaults to false - use this question as the weighting variable by default",
            "weighting_label": "(optional): Display text to more concisely describe the weighting variable",
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
            "url": "The publicly accessible URL of the file. This can be used in place of relative_path.",
            "type": "image|video",
            "display_text": "(optional) text to display for accessibility or when the content cannot render"
        }
    ],
}
```

### Survey Response Dimension
A CSV file containing responses to survey questions, here are the highlights with greater detail following:
- One respondent per line, respondent ID in column PERSON_ID
- If a user wasn't shown a question no empty space should exist between the commas. 
- Categorical values are represented by a 1 meaning selected, 0 meaning not selected or blank as not presented
- The header row must match the following format. `QuestionGroupingId_QuestionTemplateId_VariableId_VariableValue_ResponseOptionId`
- RFC 4180, UTF-8, no BOM

File formatting
---------------

We require that respondent data CSV files be encoded as UTF-8 with no BOM at the start of the file.
Files should conform to RFC 4180. We do require a header at the start of each respondent CSV, explained below.
In a nutshell, RFC 4180 requires that fields be delimited by commas, and that no spaces should exist between
the delimiting comma and the field: any spaces will be treated as part of the field. Fields may or may not
be enclosed in double quotes ("), but any fields that contain double quotes as part of the data must be
enclosed in double quotes, and the double quotes in the data must be duplicated. For example, the data:

This is a "sample" field

Would need to be represented in the CSV as:

"This is a ""sample"" field"

Fields that contain null values should be completely empty. That is, no character or other data whatsoever
should be included between the delimiting commas or after the final comma in a row of data.

We recommend that fields meant to represent an empty text string be enclosed in double quotes to
differentiate them from null fields.

Question responses
------------------

Fields containing categorical question responses should only contain either the character '1', the character '0',
or a null (empty) field. A 1 represents a positive response to the question. A 0 represents a negative response.
Note that these are both active responses. If a respondent was never shown the question at all, leave the field
null.

Fields containing open-ended or text responses can contain any data.

Fields for integer responses should contain only numeric values with no thousands separators or decimal points.

Decimal fields should always contain decimal points, even if the decimal component of the response is 0, like
in "10.0" for example.

Columns and headers
-------------------

Columns are not required to be in any particular order, except for the first column, which should be PERSON_ID.

PERSON_ID is meant to allow you to correlate responses in the data you are supplying with respondent information located in the
Person dimension.
For this reason, we require that PERSON_ID contain a unique value for each respondent in the CSV. PERSON_ID values
should be alphanumeric.

All successive columns after PERSON_ID are data columns, one per distinct question response.

A distinct question response is the unique combination of question grouping, question template, variable, variable value, and
response option, as specified in the Survey dimension:
A question grouping links to one or more question templates, a question template may or may not link to a variable, 
and a variable contains values. A categorical question will contain response options.

The combination of identifiers for all of these components uniquely identifies a single response field.

We require that each data column in the CSV correspond to one of these distinct fields and that the header field for that
column contain the combined identifier in the following format:

QuestionGroupingId_QuestionTemplateId_VariableId_VariableValue_ResponseOptionId

If any of these 5 components do not apply for a question, they should be left as '0'. Naturally, this means that the ID '0'
is not permitted as an identifier in the Survey dimension specification.

For example, as in the sample specification, say we have a question grouping Q1:

```
{
    "id": "Q1",
    "order": 1,
    "questions": [
        {
            "question_template_id": "T1",
            "order": 1
        }
    ]
}
```

and a corresponding question template T1:

```
{
    "id": "T1",
    "type": "Categorical",
    "categorical_response_options": [
        {
            "id": "R1",
            "text": "Red"
        },
        {
            "id": "R2",
            "text": "Green"
        },
        {
            "id": "R3",
            "text": "Blue"
        }
    ],
    "selection_limit": 1,
    "text": "What is your favorite color?",
    "visible": true
}
```

This specifies a single categorical question with no variables. The unique header fields we would expect for this question would be:

Q1_T1_0_0_R1, Q1_T1_0_0_R2, Q1_T1_0_0_R3

Note that there are no variables for this question, but there are response options. Therefore, we set VariableID and VariableValue
to '0' because they did not apply. We require that non-applicable IDs be set to '0' and not omitted to differentiate ambiguous headers.

As another example, let's say Q1 instead specifies a template ID of T11:

{
    "id": "T11",
    "type": "OpenEnd",
    "text": "Say hello to the world"
}

In this case, there would be no response options: the question is open-ended. There are also no variables. Thus, we expect only one
header field: Q1_T11_0_0_0

If a variable were specified in the question template:

```
{
    "id": "T10",
    "type": "Int",
    "text": "Please rank the following from best tasting (1) to least appetizing (5)",
    "variables": [
        {
            "id": "V1",
            "type": "survey",
            "order": 1
        }
    ]
}
```

With variable V1 being specified as:

```
{
    "id": "V1",
    "name": "Candy Brands",
    "description": "A few popular brands of candy",
    "values": [
        {
            "id": "BRND1",
            "value": "Snickers"
        },
        {
            "id": "BRND2",
            "value": "Reese's Peanut Butter Cups"
        },
        {
            "id": "BRND3",
            "value": "Milky Way"
        },
        {
            "id": "BRND4",
            "value": "Hershey's Bar"
        },
        {
            "id": "BRND5",
            "value": "Milka"
        }
    ]
}
```

Then we would expect, still using question grouping Q1 and template T10, 5 columns and header fields, one for each variable value:

Q1_T10_V1_BRND1_0, Q1_T10_V1_BRND2_0, Q1_T10_V1_BRND3_0, Q1_T10_V1_BRND4_0, Q1_T10_V1_BRND5_0

Note again that the field for ResponseOptionId was left as '0' because this was not a categorical question.
If it was a categorical question, for example with 5 possible responses, we would expect 25 fields, one for each combination
of variable value and response option ID.
For instance, this template specifies both variables and response options and would have 25 fields:

```
{
    "id": "T8",
    "type": "Categorical",
    "categorical_response_options": [
        {
            "id": "R24",
            "text": "Definitely"
        },
        {
            "id": "R25",
            "text": "Maybe"
        },
        {
            "id": "R26",
            "text": "Unsure"
        },
        {
            "id": "R27",
            "text": "Unlikely"
        },
        {
            "id": "R28",
            "text": "Won't"
        }
    ],
    "selection_limit": 1,
    "text": "Indicate your likelihood to buy [V1]",
    "display_text": "Indicate your likelihood to buy [brand]",
    "variables": [
        {
            "id": "V1",
            "type": "survey",
            "order": 1
        }
    ]
}
```

If there were more than one variable, for example, 3 variables each with 5 values, and also 5 categorical response options,
we would expect 75 headers/columns for that question.

