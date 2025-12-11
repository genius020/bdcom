# Understanding scanf Return Values and Continuation

## The Two Different Return Values

### Return Value:  0

**Fact:** `scanf` returns **0** when the **next input character does not match the first specification** in the format string.

**Example:**

```c
#include <stdio.h>

int main() {
    int num;
    int result;
    
    printf("Enter a number: ");
    result = scanf("%d", &num);
    
    printf("scanf returned: %d\n", result);
    
    return 0;
}
```

**Test 1 - Input:** `abc`
```
scanf returned: 0
```
**Why?** The first character 'a' doesn't match `%d` (which expects a digit). No conversion happened.  **Returns 0.**

**Test 2 - Input:** `123`
```
scanf returned: 1
```
**Why?** Successfully read 1 integer. **Returns 1.**

### Return Value: EOF

**Fact:** `scanf` returns **EOF** (usually -1) when **end-of-file** is encountered.

**Example:**

```c
#include <stdio.h>

int main() {
    int num;
    int result;
    
    printf("Enter a number (Ctrl+D for EOF): ");
    result = scanf("%d", &num);
    
    if (result == EOF) {
        printf("End of file detected\n");
    } else {
        printf("scanf returned: %d\n", result);
    }
    
    return 0;
}
```

**Test - Input:** Press `Ctrl+D` (Linux/Mac) or `Ctrl+Z` (Windows) immediately
```
End of file detected
```

**Why?** EOF encountered before any input. **Returns EOF (-1).**

## scanf Resumes After Last Converted Character

**Fact:** The next call to `scanf` resumes searching **immediately after the last character already converted.**

**Example:**

```c
#include <stdio.h>

int main() {
    int a, b;
    
    printf("Enter two numbers: ");
    
    // First scanf
    int result1 = scanf("%d", &a);
    printf("First scanf returned: %d, read: %d\n", result1, a);
    
    // Second scanf continues from where first stopped
    int result2 = scanf("%d", &b);
    printf("Second scanf returned: %d, read: %d\n", result2, b);
    
    return 0;
}
```

**Input (single line):** `42 99`

**Output:**
```
First scanf returned: 1, read: 42
Second scanf returned: 1, read: 99
```

**What happened:**
1. First `scanf` reads `42`, stops after `42` (at the space)
2. Second `scanf` **resumes from the space**, skips it, reads `99`

## Example Showing Both Concepts

```c
#include <stdio.h>

int main() {
    int num1, num2;
    
    printf("Enter input: ");
    
    int result1 = scanf("%d", &num1);
    printf("First scanf returned: %d\n", result1);
    
    if (result1 == 0) {
        printf("First character didn't match %%d\n");
        // scanf is stuck at the non-matching character
    }
    
    int result2 = scanf("%d", &num2);
    printf("Second scanf returned: %d\n", result2);
    
    return 0;
}
```

**Test 1 - Input:** `abc`
```
First scanf returned: 0
First character didn't match %d
Second scanf returned: 0
```
**Why?** First `scanf` fails at 'a', returns 0. Second `scanf` **resumes at 'a'** (same position), also fails, returns 0. **Infinite loop if repeated! **

**Test 2 - Input:** `10 20`
```
First scanf returned: 1
Second scanf returned: 1
```
**Why?** First reads `10`, stops. Second **resumes after `10`**, reads `20`.

## Summary - Facts Only

1. **Returns 0:** First input character doesn't match the format specification
2. **Returns EOF:** End-of-file encountered
3. **0 ≠ EOF:** They mean different things
4. **scanf continues:** Next `scanf` starts where the previous one stopped
5. **If scanf returns 0:** The non-matching character remains in input, causing the next `scanf` to fail at the same spot
