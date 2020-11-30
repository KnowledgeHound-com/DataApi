# KnowledgeHound Survey Dimension JSON Specification

The Survey dimension specification is intended to encapsulate and define details about a survey, including
survey metadata, such as description and dates, and survey question content.

For more details, see the DataSetsAPI document.

## Sample
```json
{
    "id": "DEMO1234",
    "description": "This sample survey was created to provide a practical guide to KnowledgeHound's data API",
    "type": "OneTime",
    "status": "Live",
    "start_date": "2020-04-08",
    "responses_as_of_date": "2020-05-23",
    "language": "eng",
    "base_description": "All respondents were imagined by a GAN",
    "base_size": "10",
    "response_count": "10",
    "source_url": "https://www.knowledgehound.com",
    "source_display_text": "Knowledgehound Home",
    "question_templates": [
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
        },
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
        },
        {
            "id": "T3",
            "type": "Categorical",
            "categorical_response_options": [
                {
                    "id": "R9",
                    "text": "Gone with the Wind",
                    "media_id": "M1"
                },
                {
                    "id": "R10",
                    "text": "The Wizard of Oz",
                    "media_id": "M2"
                },
                {
                    "id": "R11",
                    "text": "One Flew Over the Cuckoo's Nest",
                    "media_id": "M3"
                },
                {
                    "id": "R12",
                    "text": "Forrest Gump",
                    "media_id": "M4"
                },
                {
                    "id": "R13",
                    "text": "The Sound of Music",
                    "media_id": "M5"
                },
                {
                    "id": "R14",
                    "text": "West Side Story",
                    "media_id": "M6"
                }
            ],
            "selection_limit": 3,
            "text": "What are your top three movies?"
        },
        {
            "id": "T4",
            "type": "Int",
            "text": "How old are you?",
            "visible": false,
            "tags": ["age"],
            "default_weighting": true,
            "weighting_label": "Respondent Age"
        },
        {
            "id": "T5",
            "type": "Decimal",
            "text": "Enter a decimal between 1 and 2"
        },
        {
            "id": "T6",
            "type": "Categorical",
            "categorical_response_options": [
                {
                    "id": "R15",
                    "text": "Definitely"
                },
                {
                    "id": "R16",
                    "text": "Maybe"
                },
                {
                    "id": "R17",
                    "text": "Unsure"
                },
                {
                    "id": "R18",
                    "text": "Unlikely"
                },
                {
                    "id": "R19",
                    "text": "Won't"
                }
            ],
            "selection_limit": 1,
            "text": "Indicate your likelihood to buy each brand",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ]
        },
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
            "text": "Where have you purchased each brand",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ]
        },
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
        },
        {
            "id": "T9",
            "type": "Categorical",
            "categorical_response_options": [
                {
                    "id": "R29",
                    "text": "Convenience"
                },
                {
                    "id": "R30",
                    "text": "Super Market"
                },
                {
                    "id": "R31",
                    "text": "Gas Station"
                },
                {
                    "id": "R32",
                    "text": "Online"
                }
            ],
            "text": "Where have you purchased [v1]",
            "display_text": "Where have you purchased [brand]",
            "description": "Optional: Any additional information about the question",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ]
        },
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
        },
        {
            "id": "T11",
            "type": "OpenEnd",
            "text": "Say hello to the world"
        }
    ],
    "question_groupings": [
        {
            "id": "Q1",
            "order": 1,
            "questions": [
                {
                    "question_template_id": "T1",
                    "order": 1
                }
            ]
        },
        {
            "id": "Q2",
            "order": 2,
            "questions": [
                {
                    "question_template_id": "T2",
                    "order": 1
                }
            ]
        },
        {
            "id": "Q3",
            "order": 3,
            "questions": [
                {
                    "question_template_id": "T3",
                    "order": 1
                }
            ]
        },
        {
            "id": "Q4",
            "order": 4,
            "questions": [
                {
                    "question_template_id": "T4",
                    "order": 1
                }
            ]
        },
        {
            "id": "Q5",
            "order": 5,
            "questions": [
                {
                    "question_template_id": "T5",
                    "order": 1
                }
            ]
        },
        {
            "id": "Q6",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ],
            "order": 6,
            "questions": [
                {
                    "question_template_id": "T6",
                    "order": 1
                }
            ]
        },
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
        },
        {
            "id": "Q8",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ],
            "order": 8,
            "questions": [
                {
                    "question_template_id": "T8",
                    "order": 1
                },
                {
                    "question_template_id": "T9",
                    "order": 2
                }
            ]
        },
        {
            "id": "Q9",
            "variables": [
                {
                    "id": "V1",
                    "type": "survey",
                    "order": 1
                }
            ],
            "order": 9,
            "questions": [
                {
                    "question_template_id": "T10",
                    "order": 1
                }
            ]
        },
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
    ],
    "question_flow": "Future support for control logic",
    "variables": [
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
    ],
    "media": [
        {
            "id": "M1",
            "relative_path": "/images/gone_with_the_wind.png",
            "type": "image"
        },
        {
            "id": "M2",
            "relative_path": "/images/the_wizard_of_oz.png",
            "type": "image"
        },
        {
            "id": "M3",
            "relative_path": "/images/one_flew_over_the_cuckoos_nest.png",
            "type": "image"
        },
        {
            "id": "M4",
            "relative_path": "/images/forrest_gump.png",
            "type": "image"
        },
        {
            "id": "M5",
            "relative_path": "/images/the_sound_of_music.png",
            "type": "image"
        },
        {
            "id": "M6",
            "relative_path": "/images/west_side_story.png",
            "type": "image"
        }
    ]
}
```
