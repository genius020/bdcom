# Scanf in C - Complete Explanation

[← Back to About](ABOUT.md) | [Home](README.md)

The `scanf` function is the input analog of `printf`, providing many of the same conversion facilities in the opposite direction. 

## Function Prototype

```c
int scanf(char *format, ...)
```

## What scanf Does

`scanf` reads characters from the standard input, interprets them according to the specification in `format`, and stores the results through the remaining arguments.  The format argument is described below; the other arguments, **each of which must be a pointer**, indicate where the corresponding converted input should be stored.

## Return Value

`scanf` stops when it exhausts its format string, or when some input fails to match the control specification. **It returns as its value the number of successfully matched and assigned input items. ** This can be used to decide how many items were found.  

**On the end of file, EOF is returned**; note that this is different from 0, which means that the next input character does not match the first specification in the format string. 

The next call to `scanf` resumes searching immediately after the last character already converted.

### Return Value Examples

```c
#include <stdio.h>

int main() {
    int a, b, c;
    int result;
    
    printf("Enter three integers: ");
    result = scanf("%d %d %d", &a, &b, &c);
    
    printf("Successfully read %d items\n", result);
    // If user enters:  10 20 30  → result = 3
    // If user enters: 10 20 abc → result = 2
    // If user enters: abc 20 30 → result = 0
    // If EOF (Ctrl+D/Ctrl+Z) → result = EOF (-1)
    
    return 0;
}
```

## The sscanf Variant

There is also a function `sscanf` that reads from a string instead of the standard input:

```c
int sscanf(char *string, char *format, arg1, arg2, ...)
```

It scans the string according to the format in `format` and stores the resulting values through `arg1`, `arg2`, etc. **These arguments must be pointers.**

### sscanf Example

```c
#include <stdio.h>

int main() {
    char input[] = "25 Dec 1988";
    int day, year;
    char month[20];
    
    sscanf(input, "%d %s %d", &day, month, &year);
    
    printf("Day: %d, Month: %s, Year: %d\n", day, month, year);
    // Output: Day: 25, Month: Dec, Year: 1988
    
    return 0;
}
```

## Format String Components

The format string usually contains conversion specifications, which are used to control conversion of input. The format string may contain:

1. **Blanks or tabs, which are not ignored.**
2. **Ordinary characters (not %), which are expected to match the next non-white space character of the input stream.**
3. **Conversion specifications**, consisting of: 
   - The character `%`
   - An optional assignment suppression character `*`
   - An optional number specifying a maximum field width
   - An optional `h`, `l` or `L` indicating the width of the target
   - A conversion character

## How Conversion Specifications Work

A conversion specification directs the conversion of the next input field.  Normally the result is placed in the variable pointed to by the corresponding argument. If assignment suppression is indicated by the `*` character, however, the input field is skipped; no assignment is made. 

**An input field is defined as a string of non-white space characters**; it extends either to the next white space character or until the field width, if specified, is exhausted.  This implies that `scanf` will read across boundaries to find its input, since newlines are white space.  **(White space characters are blank, tab, newline, carriage return, vertical tab, and formfeed.)**

## Basic Conversion Characters - Table 7.2

| Character | Input Data; Argument type |
|-----------|---------------------------|
| **d** | decimal integer; `int *` |
| **i** | integer; `int *`. The integer may be in octal (leading 0) or hexadecimal (leading 0x or 0X). |
| **o** | octal integer (with or without leading zero); `int *` |
| **u** | unsigned decimal integer; `unsigned int *` |
| **x** | hexadecimal integer (with or without leading 0x or 0X); `int *` |
| **c** | characters; `char *`. The next input characters (default 1) are placed at the indicated spot. The normal skip-over white space is suppressed; to read the next non-white space character, use `%1s` |
| **s** | character string (not quoted); `char *`, pointing to an array of characters long enough for the string and a terminating `'\0'` that will be added. |
| **e,f,g** | floating-point number with optional sign, optional decimal point and optional exponent; `float *` |
| **%** | literal %; no assignment is made. |

### Size Modifiers

The conversion characters `d`, `i`, `o`, `u`, and `x` may be preceded by:
- **`h`** to indicate that a pointer to `short` rather than `int` appears in the argument list
- **`l`** (letter ell) to indicate that a pointer to `long` appears in the argument list

## Example 1: Rudimentary Calculator

As a first example, the rudimentary calculator of Chapter 4 can be written with `scanf` to do the input conversion:

```c
#include <stdio.h>

int main() /* rudimentary calculator */
{
    double sum, v;
    
    sum = 0;
    while (scanf("%lf", &v) == 1)
        printf("\t%.2f\n", sum += v);
    
    return 0;
}
```

**How it works:**
- Reads floating-point numbers one at a time
- Adds each to the running sum
- Prints the new sum
- Stops when `scanf` returns something other than 1 (invalid input or EOF)

**Sample run:**
```
10.5
    10.50
20.3
    30.80
5. 2
    36.00
```

## Example 2: Reading a Date

Suppose we want to read input lines that contain dates of the form: 
```
25 Dec 1988
```

The `scanf` statement is: 

```c
int day, year;
char monthname[20];

scanf("%d %s %d", &day, monthname, &year);
```

**No `&` is used with `monthname`, since an array name is a pointer.**

### Complete Example

```c
#include <stdio.h>

int main() {
    int day, year;
    char monthname[20];
    
    printf("Enter date (dd mmm yyyy): ");
    scanf("%d %s %d", &day, monthname, &year);
    
    printf("Day: %d, Month: %s, Year: %d\n", day, monthname, year);
    
    return 0;
}
```

**Input:**
```
25 Dec 1988
```

**Output:**
```
Day: 25, Month: Dec, Year: 1988
```

## Example 3: Literal Characters in Format String

Literal characters can appear in the `scanf` format string; they must match the same characters in the input. So we could read dates of the form `mm/dd/yy` with the `scanf` statement:

```c
int day, month, year;

scanf("%d/%d/%d", &month, &day, &year);
```

### Complete Example

```c
#include <stdio.h>

int main() {
    int day, month, year;
    
    printf("Enter date (mm/dd/yy): ");
    scanf("%d/%d/%d", &month, &day, &year);
    
    printf("Month: %d, Day: %d, Year:  %d\n", month, day, year);
    
    return 0;
}
```

**Input:**
```
12/25/88
```

**Output:**
```
Month: 12, Day:  25, Year: 88
```

## White Space Handling

`scanf` ignores blanks and tabs in its format string. Furthermore, it skips over white space (blanks, tabs, newlines, etc.) as it looks for input values. 

### Example: White Space

```c
#include <stdio.h>

int main() {
    int a, b, c;
    
    printf("Enter three integers: ");
    scanf("%d %d %d", &a, &b, &c);
    
    printf("Values: %d, %d, %d\n", a, b, c);
    
    return 0;
}
```

**All these inputs work the same:**
```
10 20 30
10    20    30
10
20
30
```

All produce:  `Values: 10, 20, 30`

## Example 4: Reading Lines with Multiple Formats

To read input whose format is not fixed, it is often best to read a line at a time, then pick it apart with `scanf`. For example, suppose we want to read lines that might contain a date in either of the forms above. Then we could write:

```c
while (getline(line, sizeof(line)) > 0) {
    if (sscanf(line, "%d %s %d", &day, monthname, &year) == 3)
        printf("valid: %s\n", line); /* 25 Dec 1988 form */
    else if (sscanf(line, "%d/%d/%d", &month, &day, &year) == 3)
        printf("valid:  %s\n", line); /* mm/dd/yy form */
    else
        printf("invalid: %s\n", line); /* invalid form */
}
```

### Complete Example (using fgets instead of getline)

```c
#include <stdio.h>

int main() {
    char line[100];
    int day, month, year;
    char monthname[20];
    
    printf("Enter dates (one per line, Ctrl+D to end):\n");
    
    while (fgets(line, sizeof(line), stdin) != NULL) {
        if (sscanf(line, "%d %s %d", &day, monthname, &year) == 3)
            printf("valid: %s", line); /* 25 Dec 1988 form */
        else if (sscanf(line, "%d/%d/%d", &month, &day, &year) == 3)
            printf("valid:  %s", line); /* mm/dd/yy form */
        else
            printf("invalid: %s", line); /* invalid form */
    }
    
    return 0;
}
```

**Sample run:**
```
Enter dates (one per line, Ctrl+D to end):
25 Dec 1988
valid: 25 Dec 1988
12/25/1988
valid: 12/25/1988
invalid date
invalid:  invalid date
```

## Mixing scanf with Other Input Functions

Calls to `scanf` can be mixed with calls to other input functions. The next call to any input function will begin by reading the first character not read by `scanf`.

### Example

```c
#include <stdio.h>

int main() {
    int num;
    char ch;
    
    printf("Enter a number: ");
    scanf("%d", &num);
    
    // scanf leaves the newline in the input buffer
    
    printf("Enter a character: ");
    ch = getchar();  // This will read the newline! 
    
    printf("Number: %d, Character: %c (ASCII:  %d)\n", num, ch, ch);
    
    return 0;
}
```

**Input:**
```
42
A
```

**Output:**
```
Number: 42, Character: 
 (ASCII: 10)
```

The character read is the newline, not 'A'!

## THE MOST COMMON ERROR - FINAL WARNING

**A final warning: the arguments to `scanf` and `sscanf` must be pointers.  By far the most common error is writing:**

```c
scanf("%d", n);  // WRONG! 
```

**instead of:**

```c
scanf("%d", &n);  // CORRECT!
```

**This error is not generally detected at compile time.**

### Example of the Error

```c
#include <stdio.h>

int main() {
    int n = 0;
    
    printf("Enter a number: ");
    scanf("%d", n);  // WRONG! Should be &n
    // This will try to write to address 0, causing a crash! 
    
    printf("You entered: %d\n", n);
    
    return 0;
}
```

**Result:** Segmentation fault or undefined behavior!

**Correct version:**

```c
#include <stdio.h>

int main() {
    int n = 0;
    
    printf("Enter a number: ");
    scanf("%d", &n);  // CORRECT!  Use address-of operator
    
    printf("You entered: %d\n", n);
    
    return 0;
}
```

## Exception: Arrays Don't Need &

**Arrays are already pointers, so they don't need the `&` operator:**

```c
#include <stdio.h>

int main() {
    char name[50];
    int age;
    
    printf("Enter name and age: ");
    scanf("%s %d", name, &age);  // name without &, age with &
    
    printf("Name: %s, Age:  %d\n", name, age);
    
    return 0;
}
```

## Examples of All Conversion Characters

```c
#include <stdio.h>

int main() {
    int decimal, integer, octal, unsigned_num, hex;
    char single_char, string[50];
    float floating;
    
    // %d - decimal integer
    printf("Enter decimal integer: ");
    scanf("%d", &decimal);
    
    // %i - integer (accepts decimal, octal with 0, hex with 0x)
    printf("Enter integer (dec/oct/hex): ");
    scanf("%i", &integer);
    
    // %o - octal integer
    printf("Enter octal integer:  ");
    scanf("%o", &octal);
    
    // %u - unsigned decimal
    printf("Enter unsigned decimal: ");
    scanf("%u", &unsigned_num);
    
    // %x - hexadecimal
    printf("Enter hexadecimal:  ");
    scanf("%x", &hex);
    
    // %c - single character (doesn't skip whitespace)
    printf("Enter a character: ");
    scanf(" %c", &single_char);  // Space before %c to skip whitespace
    
    // %s - string (skips leading whitespace, stops at whitespace)
    printf("Enter a string: ");
    scanf("%s", string);
    
    // %f - floating-point
    printf("Enter a float: ");
    scanf("%f", &floating);
    
    // %% - literal %
    printf("Enter a percentage (e.g., 50%%): ");
    int percent;
    scanf("%d%%", &percent);  // Matches "50%"
    
    printf("\nResults:\n");
    printf("Decimal: %d\n", decimal);
    printf("Integer: %d\n", integer);
    printf("Octal: %o (decimal: %d)\n", octal, octal);
    printf("Unsigned: %u\n", unsigned_num);
    printf("Hex: %x (decimal:  %d)\n", hex, hex);
    printf("Character: %c\n", single_char);
    printf("String: %s\n", string);
    printf("Float: %f\n", floating);
    printf("Percent: %d%%\n", percent);
    
    return 0;
}
```

## Assignment Suppression with *

```c
#include <stdio.h>

int main() {
    int day, year;
    char month[20];
    
    // Read date but skip the month name
    printf("Enter date (dd mmm yyyy): ");
    scanf("%d %*s %d", &day, &year);  // %*s skips the month
    
    printf("Day: %d, Year: %d (month was skipped)\n", day, year);
    
    return 0;
}
```

**Input:**
```
25 Dec 1988
```

**Output:**
```
Day: 25, Year: 1988 (month was skipped)
```

## Field Width Specification

```c
#include <stdio.h>

int main() {
    char code[10];
    
    // Read only first 5 characters
    printf("Enter code:  ");
    scanf("%5s", code);  // Reads max 5 characters
    
    printf("Code read: %s\n", code);
    
    return 0;
}
```

**Input:**
```
ABCDEFGHIJ
```

**Output:**
```
Code read: ABCDE
```

## Summary of Key Facts

1. **scanf returns** the number of successfully matched items, or EOF on end-of-file
2. **Arguments must be pointers** (use `&` operator for variables, not for arrays)
3. **scanf skips whitespace** when looking for input (except with `%c`)
4. **Format string can contain** blanks/tabs, ordinary characters, and conversion specifications
5. **sscanf** reads from a string instead of stdin
6. **Conversion specifications** can include `*` (suppression), field width, size modifiers (`h`, `l`)
7. **Most common error:** Forgetting `&` before variable names
8. **White space** in input is flexible - scanf skips over it
9. **Literal characters** in format string must match input exactly
10. **Mixing scanf with other input** - next function continues from where scanf stopped

---

[← Back to About](ABOUT.md) | [Home](README.md)
