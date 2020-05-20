# KnowledgeHound Person Dimension JSON Specification

Give an intro here...

## Sample
```json
{
    "attribute": [
        {
            "id": "age_range",
            "name": "Age",
            "description": "Age of respondent",
            "visible":true,
            "values": [
                {
                    "id": 0,
                    "value": "< 18",
                    "display_value": "Under 18"
                },
                {
                    "id": 1,
                    "value": "18-22",
                    "display_value": "18-22 Years Old"
                },
                {
                    "id": 2,
                    "value": "23-29",
                    "display_value": "23-29 Years Old"
                },
                {
                    "id": 3,
                    "value": "30-39",
                    "display_value": "30-39 Years Old"
                },
                {
                    "id": 4,
                    "value": "40-49",
                    "display_value": "40-49 Years Old"
                },
                {
                    "id": 5,
                    "value": "50-59",
                    "display_value": "50-59 Years Old"
                },
                {
                    "id": 6,
                    "value": ">= 60",
                    "display_value": "60+ Years Old"
                }
            ]
        }
    ],
    "person": [
        {
            "id": "1",
            "first_name": "John",
            "last_name": "Stone",
            "gender": "Male",
            "age": 55,
            "income": 84820,
            "household_size": 4,
            "count_of_children": 2,
            "marital_status": "married",
            "home_ownership": "own",
            "email": "john@stone.com",
            "phone": "555-123-7777",
            "postal_code": "12345"
        },
        {
            "id": "2",
            "first_name": "Ponnappa",
            "last_name": "Priya",
            "gender": "Female",
            "income": 85322,
            "household_size": 6,
            "count_of_children": 4,
            "marital_status": "married",
            "home_ownership": "own",
            "email": "priya@ponnappa.com",
            "phone": "123-555-7777",
            "postal_code": "54321",
            "attributes": [
                {
                    "id": "age_range",
                    "values": [4]
                }
            ]
        },
        {
            "id": "3",
            "first_name": "Mia",
            "last_name": "Wong",
            "gender": "Non-Binary",
            "age": 20,
            "income": 50931,
            "household_size": 1,
            "marital_status": "single",
            "home_ownership": "rent",
            "email": "mia@wong.com",
            "phone": "777-555-1234",
            "postal_code": "23451",
            "attributes": [
                {
                    "id": "age_range",
                    "values": [1]
                }
            ]
        },
        {
            "id": "4",
            "first_name": "Peter",
            "last_name": "Stanbridge",
            "age": 48,
            "income": 86422,
            "household_size": 3,
            "count_of_children": 2,
            "marital_status": "widowed",
            "home_ownership": "own",
            "email": "peter@stanbridge.com",
            "phone": "777-555-4321",
            "postal_code": "15432"
        },
        {
            "id": "5",
            "first_name": "Natalie",
            "last_name": "Lee-Walsh",
            "gender": "Female",
            "age": 24,
            "income": 95804,
            "household_size": 3,
            "count_of_children": 1,
            "marital_status": "married",
            "home_ownership": "rent",
            "email": "natalie@walsh.com",
            "phone": "231-555-7777",
            "postal_code": "34512"
        },
        {
            "id": "6",
            "first_name": "Ang",
            "last_name": "Li",
            "age": 28,
            "income": 92764,
            "household_size": 3,
            "count_of_children": 1,
            "marital_status": "married",
            "home_ownership": "own",
            "email": "ang@li.com",
            "phone": "132-555-7777",
            "postal_code": "21543"
        },
        {
            "id": "7",
            "first_name": "Nguta",
            "last_name": "Ithya",
            "gender": "Male",
            "age": 50,
            "income": 84820,
            "household_size": 1,
            "marital_status": "separated",
            "home_ownership": "own",
            "email": "nguta@ithya.com",
            "phone": "555-231-7777",
            "postal_code": "45123"
        },
        {
            "id": "8",
            "first_name": "Tamzyn",
            "last_name": "French",
            "age": 63,
            "income": 71047,
            "household_size": 5,
            "count_of_children": 3,
            "marital_status": "married",
            "home_ownership": "own",
            "email": "tamzyn@french.com",
            "phone": "555-132-7777",
            "postal_code": "51234"
        },
        {
            "id": "9",
            "first_name": "Salome",
            "last_name": "Simoes",
            "gender": "Non-Binary",
            "age": 28,
            "income": 92125,
            "household_size": 1,
            "marital_status": "single",
            "home_ownership": "rent",
            "email": "salome@simeos.com",
            "phone": "555-231-7777",
            "postal_code": "43215"
        },
        {
            "id": "10",
            "first_name": "Trevor",
            "last_name": "Virtue",
            "age": 71,
            "income": 54415,
            "household_size": 2,
            "marital_status": "married",
            "home_ownership": "own",
            "phone": "555-777-2341",
            "postal_code": "43251"
        }
    ]
}
```
