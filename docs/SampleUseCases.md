# Sample Use Cases Guide

The Survey Dimension is a template based schema designed to allow flexible reuse of common survey components such as
list of products or brands. This guide demonstrates how the simplest common survey data use cases map to the
[Sample](Survey.md) data and how to map to corresponding CSV Headers and values.

After understanding these basic use cases, you should be able to extrapolate reuse of templates across multiple
questions in a survey to reduce duplication in the survey specification.

Each use case is broken down in to the respective sections in the Survey Dimension using keys 'question_templates',
'question_groupings' amd 'variables'

The CSV section demonstrates how the relevant columns would be represented in the CSV file associated with the 
sample survey dimension.

## Single Select Question : What is your favorite color?

### Question Template T1:

```json
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

Note: The selection_limit of 1 indicates that this is a single select question and that respondents could only select
one response option.

### Question Grouping Q1:

```json
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

### CSV:

(1 = Respondent Selected, 0 = Respondent saw response option and did not select, null = Respondent did not see option)

```csv
PERSON_ID,Q1_T1_0_0_R1,Q1_T1_0_0_R2,Q1_T1_0_0_R3
alpha,1,0,0
2,0,1,0
3,0,1,0
4,1,0,0
5,0,0,1
6,,,
7,1,0,0
8,0,1,0
9,1,0,0
```


## Multi Select Question : Which countries have you visited?

### Question Template T2:

```json
{
    "id": "T2",
    "type": "Categorical",
    "categorical_response_options": [
        {
            "id": "R4",
            "text": "France"
        },
        {
            "id": "R5",
            "text": "Spain"
        },
        {
            "id": "R6",
            "text": "United States"
        },
        {
            "id": "R7",
            "text": "China"
        },
        {
            "id": "R8",
            "text": "Italy"
        }
    ],
    "text": "Which countries have you visited?"
}
```

Note: The absence of a selection_limit indicates the respondent may select as few or as many response options as they 
wish. You may also indicate an upper limit, E.G., 'selection_limit': 3

### Question Grouping Q2:

```json
{
    "id": "Q2",
    "order": 2,
    "questions": [
        {
            "question_template_id": "T2",
            "order": 1
        }
    ]
}
```

### CSV:

(1 = Respondent Selected, 0 = Respondent saw response option and did not select, null = Respondent did not see option)

```csv
PERSON_ID,Q2_T2_0_0_R4,Q2_T2_0_0_R5,Q2_T2_0_0_R6,Q2_T2_0_0_R7,Q2_T2_0_0_R8
alpha,1,1,0,0,0
2,1,0,1,0,1
3,0,1,0,1,1
4,1,1,0,0,0
5,1,1,1,0,0
6,,,,,
7,1,0,1,1,0
8,0,1,1,0,0
9,1,1,1,1,1
```


## Open Ended/Text: Say hello to the world

### Question Template T11:

```json
{
    "id": "T11",
    "type": "OpenEnd",
    "text": "Say hello to the world"
}
```

### Question Grouping Q10:

```json
{
    "id": "Q10",
    "order": 10,
    "questions": [
        {
            "question_template_id": "T11",
            "order": 1
        }
    ]
}
```

### CSV

(1 = Respondent Selected, 0 = Respondent saw response option and did not select, null = Respondent did not see option)

```csv
PERSON_ID,Q10_T11_0_0_0
alpha,Open ended response text here
2,Open ended response text here
3,Open ended response text here
4,Open ended response text here
5,Open ended response text here
6,Open ended response text here
7,Open ended response text here
8,Open ended response text here
9,Open ended response text here
```

## Numeric Question: How old are you?

### Question Template T4:

```json
{
    "id": "T4",
    "type": "Int",
    "text": "How old are you?",
    "visible": false,
    "tags": ["age"],
    "default_weighting": true,
    "weighting_label": "Respondent Age"
}
```

Note: The type may be "Int" or "Decimal", and this example defines this numeric question as the default weighting 
variable for the dataset, and the label which describes the weighting.

### Question Grouping Q4:

```json
{
    "id": "Q4",
    "order": 4,
    "questions": [
        {
            "question_template_id": "T4",
            "order": 1
        }
    ]
}
```

### CSV

```csv
PERSON_ID,Q4_T4_0_0_0
alpha,22
2,45
3,64
4,26
5,36
6,19
7,54
8,42
9,33
```

## Grid/Matrix Question: Where have you purchased each brand?

Grid/Matrix questions use an additional element called 'variables' which represent the rows in a table-like
survey question which is being asked of a list of related things - commonly products or brands etc.

### Question Template T7:

```json
{
    "id": "T7",
    "type": "Categorical",
    "categorical_response_options": [
        {
            "id": "R20",
            "text": "Convenience"
        },
        {
            "id": "R21",
            "text": "Super Market"
        },
        {
            "id": "R22",
            "text": "Gas Station"
        },
        {
            "id": "R23",
            "text": "Online"
        }
    ],
    "text": "Where have you purchased each brand?",
    "variables": [
        {
            "id": "V1",
            "type": "survey",
            "order": 1
        }
    ]
}
```


### Question Grouping Q7:

```json
{
    "id": "Q7",
    "variables": [
        {
            "id": "V1",
            "type": "survey",
            "order": 1
        }
    ],
    "order": 7,
    "questions": [
        {
            "question_template_id": "T7",
            "order": 1
        }
    ]
}
```

### Grid/Matrix Variables V1:

```json
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

### CSV

(1 = Respondent Selected, 0 = Respondent saw response option and did not select, null = Respondent did not see option)

```csv
PERSON_ID,Q7_T7_V1_BRND1_R20,Q7_T7_V1_BRND1_R21,Q7_T7_V1_BRND1_R22,Q7_T7_V1_BRND1_R23,Q7_T7_V1_BRND2_R20,Q7_T7_V1_BRND2_R21,...
alpha,1,1,0,1,1,1,...
2,1,0,0,0,1,0,...
3,0,1,0,0,0,1,...
4,1,0,1,0,1,0,...
5,0,1,0,0,0,1,...
6,1,1,1,1,1,1,...
7,1,0,0,0,1,0,...
8,1,0,0,0,1,0,...
9,0,0,1,1,0,0,...
```



