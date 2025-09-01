
# File Formats

- Protobuf: binary format, requires code generators and libraries, supports schema shift. 
- Avro: Meant for Hadoop and Java.  Row-based, requires a pre-defined schema
- Arrow
- Orc: Columnar file format, supports acid transactions.
- Parquet: Columnar file format, compresses better than row based, performs better in analytical queries, 
  because it can easily skip many rows or columns.

## JSON

- Objects are wrapped in braces (squirrel brackets)
- Lists are wrapped in square brackets, and items are delimited by commas.
- Keys and values are separated by colons.
- Keys and values should be quoted if they are strings, not if they are integers.
- The last item in lists should not be followed by a comma.

## Yaml

- Superset of json that doesn’t need braces, but relies upon indentation to indicate sub-fields instead.  
  Use “:” to separate keys and values, including if the value is a nested object.  Nested objects 
  go on following lines and are indented an extra two spaces.
- Use “-” to represent items in a list, including for objects.
- If items are objects with multiple keys and values, you can place extra keys/values on subsequent lines, without the hyphen.
- Can still use json form, adding braces around objects, and wrapping lists in square brackets.
- Multiple line strings:
- Use “|” to preserve new lines, “>” to replace them with spaces. (Block style indicator)
- Add a “-” to remove all trailing new lines.  “+” keeps all trailing new lines.  
  Normally just one is always kept. (Block chomping indicator)
- Anchors: Use “&” followed by a name to define an anchor, and use “*” followed by
  that name to re-use it somewhere else in the yaml document.

## Toml

- most lines are like `key = value`
- values can be strings, numbers, floats, booleans, arrays, or dates.
- strings can be basic `""`, literal `''`, or multi-line `""" ... """`
- Use "#" to comment.
- Use periods to set sub-values.
- Arrays are declared with square brackets.  Types are not enforced and don't need to be consistent.  The last item can have a 
  trailing comma.
- Tables are declared using `[name]` on a line.  Values after that are considered members of that table.
- Declare an array of tables using `[[name]]` repeatedly, with the same name.  keys and values beneath each become a table that is
  assigned to one entry in an array.

## XML

- objects declared like `<tag att="value" ...>content</tag>`
- objects with no body are like `<tag att="value" .../>`
- less popular today than json.

## CSV

- normally values are separated with commas.
- header is optional
- records are separated with new lines.
- Any value can be quoted with double quotes, but they must be quoted if they contain line breaks, commas, or double quotes.
- The most confusing edge case occurs when values have double-quotes within them.  You must double them up to get the 
  right value.  For instance, `Dwayne "the Rock" Johnson` becomes `"Dwayne ""the Rock"" Johnson"`.
  This means the value `""` becomes `""""""` when written into a CSV file.
- You cannot simply split text by new lines, nor commas, because they may be quoted.

## Avro


## Protobuf
