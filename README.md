## Overview

| Developed by | Guardrails AI |
| --- | --- |
| Date of development | Feb 15th |
| Validator type | text2sql |
| Blog |  |
| License | Apache 2 |
| Input/Output | Output |

## Description

This validator confirms that all SQL predicates generated by the LLM are valid and allowed.

## Installation

```bash
$ guardrails hub install hub://guardrails/exclude_sql_predicates
```

## Usage Examples

### Validating string output via Python

In this example, we apply the validator to a string output generated by an LLM.

```python
# Import Guard and Validator
from guardrails.hub import ExcludeSqlPredicates
from guardrails import Guard

# Initialize Validator
val = ExcludeSqlPredicates(
	predicates=["drop"],
	on_fail="noop"
)

# Setup Guard
guard = Guard.from_string(validators=[val, ...])

guard.parse("select id from employees;")  # Validator passes
guard.parse("drop employees;")  # Validator fails
```

### Validating JSON output via Python

In this example, we apply the validator to a string field of a JSON output generated by an LLM.

```python
# Import Guard and Validator
from pydantic import BaseModel
from guardrails.hub import ExcludeSqlPredicates
from guardrails import Guard

# Initialize Validator
val = ExcludeSqlPredicates(
	predicates=["drop"],
	on_fail="noop"
)

# Create Pydantic BaseModel
class SQLQuery(BaseModel):
    query: str = Field(
        description="SQL Query", validators=[val]
    )

# Create a Guard to check for valid Pydantic output
guard = Guard.from_pydantic(output_class=PetInfo)

# Run LLM output generating JSON through guard
guard.parse("""
{
    "pet_name": "select * from employees;",
}
""")
```

## API Reference

**`__init__(self, on_fail="noop")`**
<ul>

Initializes a new instance of the Validator class.

**Parameters:**

- **`predicates`** _(list[str])_: The list of SQL predicates to avoid.
- **`on_fail`** *(str, Callable)*: The policy to enact when a validator fails. If `str`, must be one of `reask`, `fix`, `filter`, `refrain`, `noop`, `exception` or `fix_reask`. Otherwise, must be a function that is called when the validator fails.

</ul>

<br>

**`__call__(self, value, metadata={}) → ValidationOutcome`**

<ul>

Validates the given `value` using the rules defined in this validator, relying on the `metadata` provided to customize the validation process. This method is automatically invoked by `guard.parse(...)`, ensuring the validation logic is applied to the input data.

Note:

1. This method should not be called directly by the user. Instead, invoke `guard.parse(...)` where this method will be called internally for each associated Validator.
2. When invoking `guard.parse(...)`, ensure to pass the appropriate `metadata` dictionary that includes keys and values required by this validator. If `guard` is associated with multiple validators, combine all necessary metadata into a single dictionary.

**Parameters:**

- **`value`** *(Any):* The input value to validate.
- **`metadata`** *(dict):* A dictionary containing metadata required for validation. No additional metadata keys are needed for this validator.

</ul>
