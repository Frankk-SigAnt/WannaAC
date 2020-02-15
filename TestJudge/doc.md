# TestJudge: WannaAC Back-end

The current version of TestJudge is restructured from and _~~(hopefully)~~_ adapted to the old version of WannaAC (See the initial commit).

## Supporting Functions

 - [x] Single-file judge with existing data
 - [x] Two-file beating with random data (Must provide **compiled** data generator)
 - [ ] User-standard beating with random data (Must provide **compiled** standard program and data generator)

   Judge:
   - [x] Normal diff (`diff`)
   - [x] Float-point number (`real`)
   - [ ] Special judge interface (`spj`)
 - [ ] Test data submission & saving

## Description

### Problem List

All problem information are stored in `info.json`. e.g.

``` json
{
    "test": {
        "title": "A + B Problem",
        "timeout": 1,
        "method": "diff",
        "precision": 0,
        "std_type": "c++",
        "data_type": "python"
    }
}
```

The corresponding test data, std program (if exists) and data generator (if exists) are all stored in `data/test/`.

The value of `method` must be one of: `"diff"`, `"real"`, `"spj"`. If the value is `"real"`, then the `precision` must be a floating-point number. _Special judge interface is temporarily not supported._

The value of `std_type` and `data_type` must be one of: `null`, `"c++"`, `"java"`, `"python"`. If the value is not `null`, the corresponding **compiled** program must be provided.

**Note:** The naming **must be** `Data` / `Data.class` / `Data.py` / `Std` / `Std.class` / `Std.py`.

### Submit Configuration

Functions `judge()` and `beat()` both receives a parameter `config_dict`, which must contain `probInfo`, `language` and `content`. For `beat()`, the `language` and `content` must be a list containing the information of both codes. See the example configurations on top of `testjudge.py`.

The content of `labguage` must be one/two of: `"c++"` and `"java"`. _Python is temporarily not supported._

For `beat()`, the default number of random test cases is 20, which can be specified manually.

### Compile/Execute Commands

- C++:

``` bash
g++ FILE_PATH/Main.cpp -o FILE_PATH/Main -std=c++14 -lm
FILE_PATH/Main
```

- Java:

```bash
javac FILE_PATH/Main.java
java -cp FILE_PATH/ Main
```

### Return Value

The return value is a Python dict at least containing `runtimeStatus`, which value could be one of:

**Single file judge case**

- `"AC"`: All given test data passed.
- `"WA"`: Wrong answer in some test data. `testData`, `expectedOutput` and `actualOutput` will be provided.
- `"TLE"`: Time limit exceeded in some test data. `testData`, `expectedOutput` and empty `actualOutput` will be provided.
- `"RE"`: Runtime error in some test data. `testData`, `expectedOutput` and empty `actualOutput` will be provided.
- `"CE"`: Compilation error. `compileInfo` will be provided.
- `"UE"`: Unexpected error.

**Two files beating case**

- `"IDT"`: Two files give identical outputs.
- `"DIFF"`: Two files give different outputs. `testData` and `outputs` (a list containing outputs of both files) will be provided.
- `"TLE"`: Time limit exceeded in some test data. `testData` will be provided.
- `"RE"`: Runtime error in some test data. `testData` will be provided.
- `"CE"`: Compilation error. `compileInfo` (a list containing messages of both files) will be provided.
- `"N/A"`: Uncertain state. The other file encounters an error or the data generator is not found. `errorMessage` will be provided in the latter case.
- `"UE"`: Unexpected error.

