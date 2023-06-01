# How to run: Measure SAST tools over patterns

## Overview

This command measures the SAST tools in the framework against the patterns in the catalogs. The goal is to have a precise idea of which patterns are problematic for a SAST tool and which ones are not.

## Command line

To measure a pattern already added to the catalogs using all available SAST in the arsenal run:

```bash
tpframework measure [-h] (-p PATTERN_ID [PATTERN_ID ...] | --pattern-range RANGE_START-RANGE_END | -a) -l LANGUAGE [--tp-lib TP_LIB_DIR] [-w NUMBER]

options:
  -h, --help            show this help message and exit
  -p PATTERN_ID [PATTERN_ID ...], --patterns PATTERN_ID [PATTERN_ID ...]
                        Specify pattern(s) ID(s) to measures
  --pattern-range RANGE_START-RANGE_END
                        Specify pattern ID range separated by`-` (ex. 10-50)
  -a, --all-patterns    Specify to run measurement on all patterns
  -l LANGUAGE, --language LANGUAGE
                        Programming Language used in the pattern
  --tp-lib TP_LIB_DIR   Path to alternative lib, default is placed in `./testability_patterns`
  -w NUMBER, --workers NUMBER
                        Number of workers running measurements in parallel
```

You have to at least provide the language (`LANGUAGE`) and the pattern ids (`PATTERN_ID`) to be measured.
Instead of specifying certain pattern ids, you can use `-a`.
**Warning** if `-a` is set, the framework will run a measurement on every pattern present in the catalog relatively to the specified language.

*Optional*: By default the framework will use `./testability_pattern` folder as root for TP Catalog, but you can specify a different one by adding the *optional* argument `--tp-lib TP_LIB_DIR` to your command.

**Output**: The result of the measurement will be available in the folder `./TP_LIB_DIR/measurements/LANGUAGE` that simply mirrors the structure of the `TP_LIB_DIR/LANGUAGE/` and will only list the patterns that have been measured. For each pattern instance measured, the command will produce a json file like the following, where two SAST tools have been measured and only one provided the correct expected result:

```json
[
    {
        "date": "2022-06-01 15:41:25",
        "result": true,
        "tool": "codeql",
        "version": "2.9.2",
        "instance": "./JS/1_unset_element_array/1_instance_1_unset_element_array/1_instance_1_unset_element_array.json"
    },
    {
        "date": "2022-06-01 15:45:25",
        "result": false,
        "tool": "another_sast",
        "version": "0.0.0",
        "instance": "./JS/1_unset_element_array/1_instance_1_unset_element_array/1_instance_1_unset_element_array.json"
    }
]
```

The value in `result` is a boolean value, where 'true' signifies the tool's correct output matching the expected result, while 'false' indicates an incorrect outcome.
For example, if it is expected, that the pattern does not contain a vulnerability, ('expectation': false) and the tool result is 'no vulnerability', than the value of `result` will be true.
If it is expected that the pattern contains a vulnerability ('expectation': true) and the tool does not detect that, the `result` field will be false.

## Example

Here a simple example that will measure patterns 1, 2, 4 and 7 from the PHP catalog with 3 workers:

```bash
tpframework measure -l PHP -p 1 2 4 7 -w 3
```

## Required fields in instance `json` metadata

The explanation for the instance `json` metadata can be found [here](https://github.com/testable-eu/sast-testability-patterns/blob/master/docs/testability-patterns-structure.md)
TODO: write here which json fields are required so that the framework can implement measure
