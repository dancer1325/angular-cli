# Schematic Prompts

* Schematic prompts
  * allows
    * introduce user interaction | schematic execution
  * displayed | BEFORE execution of the schematic
  * -- defined via -- schematic's options extensions

* schematic runtime
  * allow configuring schematic options /
    * display a customizable question | user
    * user's response -- can be used as the -- option's value

## Basic Usage

* TODO:
* To illustrate the addition of a prompt to an existing schematic the following example JSON schema for a hypothetical _hello world_ schematic will be used.

```json
{
  "properties": {
    "name": {
      "type": "string",
      "minLength": 1,
      "default": "world"
    },
    "useColor": {
      "type": "boolean"
    }
  }
}
```

Suppose it would be preferred if the user was asked for their name.
This can be accomplished by augmenting the `name` property definition with an `x-prompt` field.

```json
"x-prompt": "What is your name?"
```

* _shorthand_ form
  * requirements
    * text of the prompt
* _longhand_ form
  * see [Configuration Reference](#configuration-reference)

Adding a prompt to allow the user to decided whether the schematic will use color when executing its hello action is also very similar.
The schema with both prompts would be as follows:

```json
{
  "properties": {
    "name": {
      "type": "string",
      "minLength": 1,
      "default": "world",
      "x-prompt": "What is your name?"
    },
    "useColor": {
      "type": "boolean",
      "x-prompt": "Would you like the response in color?"
    }
  }
}
```

* Prompts types
  * display an input method / -- best represents the -- schematic option's potential values
  * `confirmation`
    * == **yes** or **no** question
    * use cases
      * boolean options
  * `input`
    * == textual input
    * use cases
      * string or number options
  * `list`
    * == predefined set of items

When using the _shorthand_ form, the most appropriate type will automatically be selected based on the property's schema.
In the example, the `name` prompt will use an `input` type because it is a `string` property.
The `useColor` prompt will use a `confirmation` type because it is a boolean property with `yes` corresponding to `true` and `no` corresponding to `false`.

It is also important that the response from the user conforms to the constraints of the property.
By specifying constraints using the JSON schema, the prompt runtime will automatically validate the response provided by the user.
If the value is not acceptable, the user will be asked to enter a new value.
This ensures that any values passed to the schematic will meet the expectations of the schematic's implementation and removes the need to add additional checks within the schematic's code.

## Configuration Reference

The `x-prompt` field supports two alternatives to enable a prompt for a schematic option.
A shorthand form when additional customization is not required and a longhand form providing the ability for more control over the prompt.
All user responses are validated against the property's schema. For example, string type properties can use a minimum length or regular expression constraint to control the allowed values.
In the event the response fails validation, the user will be asked to enter a new value.

### Longhand Form

In the this form, the `x-prompt` field is an object with subfields that can be used to customize the behavior of the prompt.
Note that some fields only apply to specific prompt types.

| Field     | Data Value                                | Default                           |
| --------- | ----------------------------------------- | --------------------------------- |
| `type`    | `confirmation`, `input`, `list`           | see shorthand section for details |
| `message` | string                                    | N/A (required)                    |
| `items`   | string and/or `label`/`value` object pair | only valid with type `list`       |

### Shorthand Form

`x-prompt` [type: string] --> Question to display to the user.

For this usage, the type of the prompt is determined by the type of the containing property.

| Property Schema     |  Prompt Type   |                Notes                |
| ------------------- | :------------: | :---------------------------------: |
| `"type": "boolean"` | `confirmation` |                                     |
| `"type": "string"`  |    `input`     |                                     |
| `"type": "number"`  |    `input`     |     only valid numbers accepted     |
| `"type": "integer"` |    `input`     |     only valid numbers accepted     |
| `"enum": [...]`     |     `list`     | enum members become list selections |

### `x-prompt` Schema

```json
{
  "oneOf": [
    { "type": "string" },
    {
      "type": "object",
      "properties": {
        "type": { "type": "string" },
        "message": { "type": "string" },
        "items": {
          "type": "array",
          "items": {
            "oneOf": [
              { "type": "string" },
              {
                "type": "object",
                "properties": {
                  "label": { "type": "string" },
                  "value": {}
                },
                "required": ["value"]
              }
            ]
          }
        }
      },
      "required": ["message"]
    }
  ]
}
```
