# NAME

json - JSON parser / builder for Calcium

# SYNOPSIS

```calcium
use ytnobody/json;

// Parse JSON string to Calcium hash/array
data = json.parse('{"name": "Alice", "age": 30}') !? {
    success(v) => v
    failure(e) => {}
};

data.name;  // "Alice"
data.age;   // 30

// Convert Calcium hash/array to JSON string
json_str = json.stringify({name: "Bob", scores: [85, 92, 78]});
// '{"name":"Bob","scores":[85,92,78]}'
```

# DESCRIPTION

This module provides functions to parse JSON strings into Calcium data structures
and to serialize Calcium hashes and arrays into JSON strings.

The parser is implemented in pure Calcium using recursive descent parsing.
It supports the full JSON specification including:

- Objects (hashes)
- Arrays
- Strings (with escape sequences)
- Numbers (integers and floats, including scientific notation)
- Booleans (`true`, `false`)
- Null values

# FUNCTIONS

## parse(s)

Parses a JSON string and returns the corresponding Calcium value.

```calcium
json.parse('{"key": "value"}');  // success({key: "value"})
json.parse('[1, 2, 3]');         // success([1, 2, 3])
json.parse('"hello"');           // success("hello")
json.parse('42');                // success(42)
json.parse('3.14');              // success(3.14)
json.parse('true');              // success(true)
json.parse('null');              // success(null)
```

Returns `success(value)` on successful parse, `failure(error_message)` on parse error.

## stringify(value)

Converts a Calcium value to a JSON string.

```calcium
json.stringify({name: "Alice"});      // '{"name":"Alice"}'
json.stringify([1, 2, 3]);            // '[1,2,3]'
json.stringify("hello");              // '"hello"'
json.stringify(42);                   // '42'
json.stringify(true);                 // 'true'
json.stringify(null);                 // 'null'
```

Returns the JSON string representation of the value.

# EXAMPLES

## Parsing API Response

```calcium
use ytnobody/json;
use core.http!;
use core.io!;

response = http.get("https://api.example.com/users/1", {});

response !? {
    success(res) => {
        user = json.parse(res.body) !? {
            success(data) => data
            failure(_) => {}
        };
        io.println("Name: " + user.name);
    }
    failure(e) => io.println("Error: " + e)
};
```

## Building JSON for POST Request

```calcium
use ytnobody/json;
use core.http!;

payload = json.stringify({
    name: "New User",
    email: "user@example.com",
    active: true
});

http.post_json("https://api.example.com/users", payload);
```

## Working with Nested Data

```calcium
use ytnobody/json;

data = json.parse("""
{
    "users": [
        {"name": "Alice", "age": 30},
        {"name": "Bob", "age": 25}
    ],
    "total": 2
}
""") !? { success(v) => v  failure(_) => {} };

data.users[0].name;  // "Alice"
data.total;          // 2
```

# INSTALLATION

```calcium
use ytnobody/json;
```

The module is automatically fetched from GitHub and cached locally.

# DEPENDENCIES

This module uses the following core (stdlib) modules:

- `core.string` - String manipulation functions
- `core.types` - Type conversion functions

> Note: Core modules are part of the standard library and are always available.
> They do not need to be declared in `meta.toml`'s `[dependencies]` section.

# LIMITATIONS

- Escape sequences in strings are passed through but not fully decoded
  (e.g., `\u0041` is not converted to `A`)
- Very deeply nested structures may hit recursion limits

# SEE ALSO

- `core.toml` - TOML parser in standard library
- Calcium Language Reference Manual

# REPOSITORY

https://github.com/ytnobody/json-calcium

# AUTHOR

YTNOBODY

# LICENSE

MIT License

Copyright (c) YTNOBODY

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
