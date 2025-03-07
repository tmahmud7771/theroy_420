# LEXICAL ANALYSIS - SECTION 3.1 NOTES

## THE ROLE OF THE LEXICAL ANALYZER

### Basic Definition

The lexical analyzer is the **first phase** of a compiler that reads input characters from source code, groups them into lexemes, and produces tokens for the parser.

### Key Terminology

**Token**: A pair consisting of a token name and an optional attribute value

- Example: `<id, pointer to symbol table entry for count>`

**Lexeme**: The actual character sequence in source code matching a token pattern

- Example: In `count = 0`, the lexeme `count` matches the identifier pattern

**Pattern**: A rule describing what form the lexemes of a token can take

- Example: For identifiers, "letter followed by letters and digits"

### Core Functions

1. Read input characters
2. Group characters into lexemes
3. Produce tokens for the parser
4. Remove comments and whitespace
5. Track line numbers for error reporting
6. May expand macros in some compilers

### Interaction with Other Components

- Parser calls lexical analyzer using `getNextToken()`
- Lexical analyzer interacts with symbol table
- Output tokens are sent to the parser for syntax analysis

## MEMORY TRICK: "TOKEN-LPT"

```
          TOKEN
         /     \
        /       \
   LEXEME       PATTERN
```

### Example Using "TOKEN-LPT":

For code: `int count = 0;`

| TOKEN     | LEXEME  | PATTERN                           |
| --------- | ------- | --------------------------------- |
| keyword   | `int`   | The keyword "int"                 |
| id        | `count` | Letter followed by letters/digits |
| assign_op | `=`     | The character "="                 |
| number    | `0`     | One or more digits                |
| semicolon | `;`     | The character ";"                 |

### The "CLIPS" Functions:

What does the lexical analyzer do? It CLIPS:

- **C**ollects characters
- **L**abels them as tokens
- **I**dentifies patterns
- **P**asses tokens to parser
- **S**trips comments & whitespace

### Why Separate? Remember "SEP":

- **S**implicity: Easier design
- **E**fficiency: Specialized techniques
- **P**ortability: Device-specific code isolated

### Token Classes: "KOPCP"

- **K**eywords (if, while)
- **O**perators (+, -, ==)
- **P**unctuators (;, (), {})
- **C**onstants (numbers, strings)
- **P**rogram names (identifiers)

## IMPORTANT QUESTIONS AND ANSWERS

### Q1: What is the difference between a token, lexeme, and pattern?

**Answer**: A token is a category or type (like "identifier"), a lexeme is the actual character sequence in the source code (like "count"), and a pattern is the rule that describes what form lexemes of a token can take (like "letter followed by letters and digits").

### Q2: Why is lexical analysis separated from parsing?

**Answer**: For simplicity of design, compiler efficiency, and portability. This separation allows specialized techniques for each phase and isolates device-specific code to the lexical analyzer.

### Q3: What information is typically stored as token attributes?

**Answer**: For identifiers: pointer to symbol table entry
For numbers: the numerical value or representation
For strings: pointer to the string table
For keywords and operators: typically no attribute needed

### Q4: How does a lexical analyzer handle errors?

**Answer**: Common strategies include:

- "Panic mode": Delete characters until a valid token is found
- Character transformations (delete, insert, replace, transpose)
- Most lexical analyzers have limited error detection without parser context

### Q5: What is the relationship between the lexical analyzer and the parser?

**Answer**: The parser calls the lexical analyzer using `getNextToken()`. The lexical analyzer reads input characters, identifies the next token, and returns it to the parser. This process continues until the entire input is processed.

### Q6: What other tasks besides tokenization might a lexical analyzer perform?

**Answer**: Removing comments and whitespace, tracking line numbers for error reporting, creating a clean copy of the source program, and possibly expanding macros.

### Q7: How are tokens represented in a compiler?

**Answer**: As a pair containing a token name (or token code) and optional attribute value. For example, an identifier might be represented as `<id, pointer>` where the pointer refers to the symbol table entry for that identifier.

## VENDING MACHINE ANALOGY

Think of a lexical analyzer as a vending machine:

- **INPUT**: You insert coins and press buttons (source code characters)
- **PROCESS**: Machine recognizes valid coin combinations (grouping characters)
- **OUTPUT**: Machine dispenses labeled products (tokens for the parser)

## EXAMPLE OF LEXICAL ANALYSIS

### Source Code:

```c
while (count < 10) {
    sum = sum + count;
    count = count + 1;
}
```

### Token Stream Output:

```
<while, ->
<(, ->
<id, pointer to symbol table entry for count>
<relop, LT>
<number, 10>
<), ->
<{, ->
<id, pointer to symbol table entry for sum>
<assign_op, ->
<id, pointer to symbol table entry for sum>
<add_op, ->
<id, pointer to symbol table entry for count>
<;, ->
<id, pointer to symbol table entry for count>
<assign_op, ->
<id, pointer to symbol table entry for count>
<add_op, ->
<number, 1>
<;, ->
<}, ->
```
