# h2n_res_checker
**h2n_res_checker** checks the consistency of the data in resource files of H2N (Hand2Note). 

The resource files are located in `H2N/Config`. Their extensions:
- .filter2 — stat
- .fitler2l — locked stat
- .exst — expression stat
- .fgroup2 — filter group
- .hprofile — hud profile
- ... (many others)

### Currently supported resource file:
- [x] .filter2
- [x] .filter2l

## Motivation
Resource files may conflict with each other causing side effects, like loss of edited stats, HUDS, etc.

Often this could happen when you import stat files from an external source.
They may have the same name or IDs with those that are already in your `Config` folder.
The latter will cause missing of random files while working with H2N. 

If you are interested in more details about the issue, follow
[this link](./docs/problem_descr.md).

## How to use

You can run the script directly as any other .exe or by CMD.

### 1. Directly run .exe

You will be prompted to enter the directory path and whether locked filters
should be scanned.

### 2. CMD

```
h2n_res_checker.exe [-h] [-s [SOURCE_PATH]] [-l]
```

#### Examples:

```
# Check all unlocked files
h2n_res_checker.exe -s "C:\Users\Kent\h2n_res_checker\tests"
```

```
# Check both unlocked and locked files.
h2n_res_checker.exe -l -s "C:\Users\Kent\h2n_res_checker\tests"
```

#### Options
```
-h, --help       Print this help text and exit.

 -s              Script scans all files in this directory and subdirs recursively.
 
 -l              Allow taking Stat name and ID from the filename if it's locked.
                 Otherwise, locked files won't be scanned.
```

## Script results

The script scans all nested folders starting from the root folder recursively.

The scan results can be found in .txt files in the same folder as h2n_res_checker.exe. 
After each run the script, they are refreshed (old data is deleted).

In scan results, you will see categories of issues. Down below you can see the list.

Few levels of the importance of the issue are used for convenience:
1. **Critical** — it's highly recommended taking actions on that
2. **Warning** — can be ignored but tends to create Critical issues in further
3. **Unwilling** — can be ignored

### Resource id is same but data differs

> **Status:** Critical

If several stats have same ID, then H2N will import only one arbitrary. 
You don't want to have multiple stats with same ID,
because you never know which will be imported by H2N.
And you may only guess.

If you edited one of that file in H2N, then relaunched software, H2N may import other file.
You will get no warnings and continue to work with another file.

Worse case if files with the same IDs are placed in one folder. 
And if you edit and save changes to one file, H2N will delete others without any notification.
It seems this won't happen if they are in separate folders.

[Stats example](./tests/test_files/test_unlocked/hash_same_data_diff).

### Resource id is same, and data is same

> **Status:** Warning

The same as previous, but now the files content is the same. You may wish to delete unnecessary copies to
avoid editing one file and falling into the previous issue,
when ID is same but content is different.

[Stats example](./tests/test_files/test_unlocked/hash_same_data_same).

### Resource id differs, but the name of the resource files is same

> **Status:** Unwilling

As mentioned above H2N differs files by their ID. So name collision is totally ok.
But from my experience, its inconvenient, and it's better to work with stats that have unique name.
So it's handy to check such cases.

[Stats example](./tests/test_files/test_unlocked/hash_diff_name_same).

### Name discrepancy

> **Status:** Unwilling

Stat name from file content differs from stat name in the filename (numbers in brackets).

This may lead to some confusion when looking at the names in H2N and Windows explorer.

[Stats example](./tests/test_files/test_unlocked/name_discrepancy).

### Hash discrepancy

> **Status:** Unwilling

ID from file content differs from ID in the filename (numbers in brackets).

It will be hard to detect other problems if you lock files.
Because all data is encrypted in lock files, we can only rely on the assumption that 
data in the filename matches the internal data of that file.

[Stats example](./tests/test_files/test_unlocked/hash_discrepancy).
