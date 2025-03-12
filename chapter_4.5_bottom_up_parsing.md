# Complete Guide to Bottom-Up Parsing

## Section 1: Key Concepts

### 1.1 Bottom-Up Parsing Fundamentals

- **Bottom-up parsing** is the construction of a parse tree from the leaves (bottom) up to the root (top)
- Also known as **shift-reduce parsing**
- Creates a **rightmost derivation in reverse**
- Bottom-up parsing is implemented through a **stack** and an **input buffer**

### 1.2 Core Terminology

- **Reduction**: The reverse of a derivation step; replacing a substring matching a production body with the production head
- **Handle**: A substring that matches a production body and whose reduction is the next step in the reverse of a rightmost derivation
- **Handle pruning**: The process of repeatedly finding and reducing handles
- **Right-sentential form**: Any string that can appear in a rightmost derivation from the start symbol

### 1.3 Shift-Reduce Parser Operations

1. **Shift**: Move the next input symbol onto the top of the stack
2. **Reduce**: Replace a string at the top of the stack with a nonterminal according to a grammar production
3. **Accept**: Successfully complete parsing when the start symbol is on the stack and the input is empty
4. **Error**: Detect a syntax error when no valid action is possible

### 1.4 LR Parsing

- **LR parsing** is a systematic approach to shift-reduce parsing

  - **L**: Left-to-right scanning of input
  - **R**: Constructing a rightmost derivation in reverse
  - **k**: Number of lookahead symbols (usually 1)

- **LR parser types**:

  - **Simple LR (SLR)**: Uses FOLLOW sets to determine when to reduce
  - **Canonical LR**: Uses precise lookahead sets calculated for each item
  - **LALR**: Merges similar states from canonical LR, maintaining lookahead information

- **LR parsing advantages**:
  - Can handle virtually all programming language constructs
  - More powerful than LL parsing (can handle left recursion)
  - Detects syntax errors as early as possible

## Section 2: Step-by-Step Example

Let's trace through the example from the textbook for the input string "id \* id" using grammar:

```
E → T | E + T
T → F | T * F
F → id | (E)
```

### Initial Configuration

```
Stack: $
Input: id * id $
```

### Step 1: Shift

```
Stack: $ id
Input: * id $
Action: shift
```

### Step 2: Reduce by F → id

```
Stack: $ F
Input: * id $
Action: reduce by F → id
```

### Step 3: Reduce by T → F

```
Stack: $ T
Input: * id $
Action: reduce by T → F
```

### Step 4: Shift

```
Stack: $ T *
Input: id $
Action: shift
```

### Step 5: Shift

```
Stack: $ T * id
Input: $
Action: shift
```

### Step 6: Reduce by F → id

```
Stack: $ T * F
Input: $
Action: reduce by F → id
```

### Step 7: Reduce by T → T \* F

```
Stack: $ T
Input: $
Action: reduce by T → T * F
```

### Step 8: Reduce by E → T

```
Stack: $ E
Input: $
Action: reduce by E → T
```

### Step 9: Accept

```
Stack: $ E
Input: $
Action: accept
```

## Section 3: Parsing Conflicts

### 3.1 Shift/Reduce Conflicts

Occurs when the parser cannot decide whether to shift the next input symbol or reduce what's on the stack.

**Example**: The dangling-else problem with grammar:

```
stmt → if expr then stmt
      | if expr then stmt else stmt
      | other
```

In the configuration:

```
Stack: ... if expr then stmt
Input: else ... $
```

The parser cannot determine whether to reduce "if expr then stmt" to "stmt" or to shift "else".

### 3.2 Reduce/Reduce Conflicts

Occurs when there are multiple possible reductions for the same substring.

**Example**: Procedure calls vs. array references:

```
stmt → id ( parameter_list )
expr → id ( expr_list )
```

With input "id(id)", the parser cannot determine which production to use when reducing "id" on the stack.

## Section 4: Solved Problems

### 4.1 Exercise 4.5.1

For the grammar S → 0 S 1 | 0 1, indicate the handle in each right-sentential form:

#### a) 000111

To find the handle, we trace the rightmost derivation:

```
S ⇒ 0 S 1 ⇒ 0 0 S 1 1 ⇒ 0 0 0 1 1 1
```

The handle is the substring "01" at positions 3-4 (matching the production S → 0 1).

#### b) 00S11

The rightmost derivation is:

```
S ⇒ 0 S 1 ⇒ 0 0 S 1 1
```

The handle is "S" since it would be reduced next in the reverse derivation.

### 4.2 Exercise 4.5.2

For grammar S → S S + | S S \* | a, find handles in the following right-sentential forms:

#### a) SSS + a \* +

The handle is "a" (which would be reduced to S using S → a).

#### b) SS + a \* +

The handle is "a" (which would be reduced to S using S → a).

#### c) aaa \* a + +

The initial handles are each "a" (reduced to S). Then we'd have SSS _ S + +, and the handle would be "S S _" (reduced to S using S → S S \*).

### 4.3 Exercise 4.5.3

Complete bottom-up parse for input 000111 according to grammar S → 0 S 1 | 0 1:

| Stack | Input   | Action              |
| ----- | ------- | ------------------- |
| $     | 000111$ | shift               |
| $0    | 00111$  | shift               |
| $00   | 0111$   | shift               |
| $000  | 111$    | shift               |
| $0001 | 11$     | reduce by S → 0 1   |
| $00S  | 11$     | shift               |
| $00S1 | 1$      | reduce by S → 0 S 1 |
| $0S   | 1$      | shift               |
| $0S1  | $       | reduce by S → 0 S 1 |
| $S    | $       | accept              |

The rightmost derivation (in reverse) is:

```
S ⇒ 0 S 1 ⇒ 0 0 S 1 1 ⇒ 0 0 0 1 1 1
```

## Section 5: Important Properties of LR Parsing

### 5.1 The Handle Property

- The handle always eventually appears on top of the stack, never inside
- This is crucial for the proper operation of shift-reduce parsers

### 5.2 LR Parser Operation

1. Uses two tables:

   - **ACTION table**: Determines whether to shift, reduce, accept, or report an error
   - **GOTO table**: Determines which state to transition to after a reduction

2. The stack contains both symbols and states:

   - States guide the parsing decisions
   - Symbols represent the grammar symbols being parsed

3. Parsing decisions are deterministic:
   - Based on current state and lookahead symbol(s)
   - No backtracking required

### 5.3 LR Grammar Properties

- A grammar is LR(k) if we can construct a deterministic LR parser for it with k symbols of lookahead
- Most programming language constructs can be expressed with LR(1) grammars
- Non-LR grammars typically have shift/reduce or reduce/reduce conflicts

### 5.4 Constructing LR Parsers

- Building LR parsers by hand is complex and error-prone
- Parser generators (e.g., YACC, Bison) automate the construction of parsing tables
- The process involves:
  1. Building a collection of LR(0) items
  2. Computing lookahead sets
  3. Constructing ACTION and GOTO tables

## Section 6: Key Distinctions Between Parser Types

### 6.1 Bottom-Up vs. Top-Down Parsing

| Bottom-Up (LR) Parsing                          | Top-Down (LL) Parsing                           |
| ----------------------------------------------- | ----------------------------------------------- |
| Constructs rightmost derivation in reverse      | Constructs leftmost derivation                  |
| Starts from input and works toward start symbol | Starts from start symbol and works toward input |
| Uses shift and reduce operations                | Uses predict and match operations               |
| Can handle left recursion                       | Cannot handle left recursion directly           |
| Can handle a larger class of grammars           | More restrictive on grammar structure           |
| More complex to implement by hand               | Easier to implement by hand                     |

### 6.2 Types of LR Parsers

| Parser Type  | Power     | Table Size | Construction Complexity |
| ------------ | --------- | ---------- | ----------------------- |
| SLR          | Weakest   | Smallest   | Simplest                |
| LALR         | Medium    | Medium     | Medium                  |
| Canonical LR | Strongest | Largest    | Most complex            |

Most parser generators use LALR parsing as it offers a good balance between power and efficiency.

## Section 7: Tips for Working with Bottom-Up Parsers

1. **Grammar Design**: Design grammars to avoid ambiguities and conflicts
2. **Conflict Resolution**: Use precedence and associativity rules to resolve shift/reduce conflicts
3. **Error Recovery**: Implement error recovery strategies to continue parsing after errors
4. **Semantic Actions**: Attach semantic actions to productions for translation
5. **Testing**: Test with both valid and invalid inputs to verify correctness

---

_This guide is based on concepts from the Dragon Book (Compilers: Principles, Techniques, and Tools) and focuses on bottom-up parsing techniques, particularly LR parsing._
