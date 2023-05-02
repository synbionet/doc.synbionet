# Metadata and Specifications

**WORK IN PROGRESS**

Many facets of the system need to track URI links to external data for information such as name, description, other links, etc.... This document outlines some of the JSON schema needed for that information.

## Service metadata
A service should contain a URI to a json document describing the service.  This information can be used to populate the UI.

### Minmal Schema
```json
{
    "type": "object",
    "properties": {
        "name": {
            "type": "string",
            "description": "Name of the service"
        },
        "description": {
            "type": "string",
            "description": "Description of the service"
        },
        "inputSpecification": {
            "type": "string",
            "format": "uri",
            "description": "URI of JSON schema for service input"
        },
        "termsAndConditions": {
            "type": "string",
            "format": "uri",
            "description": "URI of service terms and conditions"
        }
    },
    "required": [
        "name",
        "description",
        "inputSpecification"
    ]
}
```

## Input Specification
A JSON schema document used to capture the required input of a service. This can be used to generate a input form in the UI or used to integrate with other APIs.

### Example Schema

For example, here's how you might define the input specification for a service that performs protein expression and purification
```json
{
    "name": "Service Specification",
    "description": "This service provides protein expression...",
    "inputSpecification": {
        "type": "object",
        "properties": {
            "geneName": {
                "type": "string",
                "description": "Please enter the name of the gene"
            },
            "typeOfSequence": {
                "type": "string",
                "enum": [
                    "DNA",
                    "Protein",
                ],
                 "description": "Select the type of sequence"
            },
            "sequence": {
                "type": "string",
                "description": "Please enter the sequence string"
            },
            "bacterialExpression": {
                 "type": "boolean",
                 "description": "Bacterial Expression System"
            },
            
            "yeastExpression": {
                "type": "boolean",
                "description": "Yeast Expression System"
            },
            "amount": {
                "type": "number",
            },
            "purity": {
                "type": "number",
            },
            "otherRequirments": {
                "type": "string",
            }
        },
        "required": [
            "geneName",
            "typeOfSequence",
            "sequence"
        ],
    }
}
```

The output format from completing this form in the UI will be json.

