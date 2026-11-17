# Workload Imbalance Report

A C++17 command-line solution to the **2024 CROC Summer IT School selection task**. It reads weekly time entries and reports employees whose recorded hours differ from the weekly target by more than 10%.

**Solution author:** Artem Alekseevich Stepanov (Степанов Артём Алексеевич), [ST3PAN0V](https://github.com/ST3PAN0V).

## Task

A time-tracking system exports a weekly `report.txt`. The first line contains the target hours per employee; each following line contains one time entry in the TSR-3 format:

```text
<employee UUID> <last name> <first name> <patronymic> <dd.MM.YYYY> <hours>
```

Multiple entries may belong to the same employee. The program totals the hours and writes employees outside the allowed range to `result.txt`:

```text
<last name> <first initial>.<patronymic initial>. <signed difference from target>
```

Employees below the target appear first, followed by those above it. Each group is ordered lexicographically by name. Exactly 10% deviation is within the allowed range.

For a 40-hour target, totals below 36 or above 44 are included. The reported difference is measured from 40 hours, not from the tolerance boundary.

## Implementation

- C++17 and the standard library, built with CMake.
- File-based input and output; no interactive prompts.
- Aggregation through `std::map<Employee, float>`.
- Two passes over the ordered results: negative deviations, then positive deviations.
- UTF-8 name conversion to produce Cyrillic initials.

| Path | Purpose |
| --- | --- |
| [src/main.cpp](src/main.cpp) | Entry point and exception reporting |
| [src/tools.cpp](src/tools.cpp) | Parsing, aggregation, filtering, and output formatting |
| [src/tools.h](src/tools.h) | Data types, file paths, and the 10% threshold |
| [report.txt](report.txt) | Sample input |
| [result.txt](result.txt) | Sample output, overwritten when the program runs |
| [CMakeLists.txt](CMakeLists.txt) | Build configuration |

## Build

Requirements: a C++17 compiler, CMake 3.11 or newer, and a system with the **`ru_RU.UTF-8` locale installed**. The program constructs that locale explicitly; changing a shell environment variable alone does not install it.

The repository contains older build directories. Create a fresh directory so CMake can configure paths for your machine:

```bash
git clone https://github.com/ST3PAN0V/school2024-test-task6.git
cd school2024-test-task6
mkdir -p build-local
cd build-local
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
```

## Run

Run from the build directory created above:

```bash
./Imbalance_monitoring
cat ../result.txt
```

The program reads `../report.txt` and writes `../result.txt`, relative to its **working directory**. It does not accept file paths as command-line arguments. Running it replaces the output file.

To use another report, place it at `report.txt` in the repository root, keeping the format described above.

## Check the result

No automated test suite is included. Use the supplied report as a sample, inspect the output, and check stderr for errors. The entry point catches exceptions and still returns `0`, so the process exit status alone is not a reliable success check.

Useful boundary cases are a total exactly at the target, totals exactly 10% above or below it, and totals just outside that range.

## Current scope

This is a selection-task solution for a report that already covers one working week. Dates are read but are not used to filter the data.

The current map comparison uses the employee's full name rather than UUID. Distinct employees with identical full names would therefore be grouped together. File paths and the tolerance are defined in `src/tools.h`, and the parser expects well-formed input.
