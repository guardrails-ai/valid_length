# Overview

| Developed by | Guardrails AI |
| --- | --- |
| Date of development | Feb 15, 2024 |
| Validator type | Format |
| Blog |  |
| License | Apache 2 |
| Input/Output | Output |

# Description

This validator can perform the following checks:

1. If applying this validator on a string: ensure that a generated string is within an expected length
2. If applying this validator on a generated JSON object: ensure that a generated list is within an expected length

# Installation

```bash
$ guardrails hub install hub://guardrails/valid-length
```

# Usage Examples

## Validating string output via Python

In this example, we verify that an LLM generated response contains anywhere from 100-200 characters.

```python
# Import Guard and Validator
from guardrails.hub import ValidChoices
from guardrails import Guard

# Initialize Validator
val = ValidChoices(
		min=100,
		max=200,
		on_fail="fix"
)

# Setup Guard
guard = Guard.from_string(
    validators=[val, ...],
)

guard.parse(
		"Guardrails are essential for AI dev. "
		"You can initialize guadrails for strings, JSON objects and via python and javascript."
)  # Validator passes
guard.parse("Guardrails are essential for AI dev.")  # Validator fails
```

## Validating JSON output via Python

### Validating the length of a string field within a generated JSON

This example applies the validator to a string field of a JSON object.

```python
# Import Guard and Validator
from pydantic import BaseModel
from guardrails.hub import ValidChoices
from guardrails import Guard

val = ValidChoices(
		min=100,
		max=200,
		on_fail="fix"
)

# Create Pydantic BaseModel
class ProductInfo(BaseModel):
		product_name: str = Field(description="Name of the product")
		product_summary: str = Field(
				description="A summary of the product", validators=[val]
		)

# Create a Guard to check for valid Pydantic output
guard = Guard.from_pydantic(output_class=PetInfo)

# Run LLM output generating JSON through guard
guard.parse("""
{
		"product_name": "Hairspray",
		"product_summary": "This product helps your styled hair stay in place."
}
""")
```

### Validating the length of a list within a generated JSON

This example applies the validator to a list of a JSON object, and ensures that the length of the list is within an expected range.

```python
# Import Guard and Validator
from pydantic import BaseModel
from guardrails.hub import ValidChoices
from guardrails import Guard

val = ValidChoices(
		min=1,
		max=2,
		on_fail="fix"
)

# Create Pydantic BaseModels
class ProductInfo(BaseModel):
		"""Information about a single product."""
		product_name: str = Field(description="Name of the product")
		product_summary: str = Field(description="A summary of the product")

class ProductCategory(BaseModel):
		"""List of products."""
		category_name: str = Field(description="Name of product category")
		products: list[ProductInfo] = Field(description="List of products")

# Create a Guard to check for valid Pydantic output
guard = Guard.from_pydantic(output_class=ProductCategory)

# Run LLM output generating JSON through guard
guard.parse("""
{
		"category_name": "Hair care",
		"products": [
				{
						"product_name": "Hair spray",
						"product_summary": "Helps your styled hair stay in place."
				},
				{
						"product_name": "Shampoo",
						"product_summary": "Helps clean your hair."
				}
		]
""")
```

## Validating string output via RAIL

tbd

## Validating JSON output via RAIL

tbd

# API Reference

`__init__`

- `min`: Min expected length of the object (str, list).
- `max`: Max expected length of the object (str, list).
- `on_fail`: The policy to enact when a validator fails.
