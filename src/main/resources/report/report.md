# Lab Report: Lexer & Scanner Implementation

### Course: Formal Languages & Finite Automata
### Author: Maxim Costov

---

## Overview
A lexer (or scanner) is a fundamental component in compilers and interpreters that performs lexical analysis. It processes a stream of characters from source code and converts them into a sequence of tokens, which are the smallest meaningful units of a programming language. Tokens can be keywords, identifiers, literals, operators, or punctuation. The lexer categorizes each lexeme (raw string of characters) into a token type, enabling subsequent parsing and interpretation phases.

---

## Objectives
1. **Understand lexical analysis**: Learn how raw source code is transformed into tokens.
2. **Explore lexer internals**: Study the structure and workflow of a lexer/scanner.
3. **Implement a lexer**: Create a lexer capable of handling multiple token types, including integers, floats, strings, keywords, and operators.

---

## Theoretical Background
**Lexical Analysis**:
- **Lexeme**: A sequence of characters in the source code (e.g., `"42"`, `"print"`).
- **Token**: A categorized lexeme with metadata (e.g., `NUMBER 42`, `KEYWORD print`).

**Finite Automata in Lexing**:  
The lexer uses finite automata concepts to match character patterns. For example:
- **Numbers**: Transition between states for digits and decimal points.
- **Identifiers**: Transition through alphanumeric characters.
- **Strings**: Track opening/closing quotes and escape characters.

**Regular Expressions**:  
Token rules are often defined using regex (e.g., `[a-zA-Z_][a-zA-Z0-9_]*` for identifiers). The lexer implements these rules via state transitions.

---

## Implementation
The lexer is implemented in Java, consisting of three main classes: `Scanner`, `Token`, and `TokenType`.

### Key Components:
1. **Scanner Class**:
    - **State Management**: Tracks the current position (`current`), start of the token (`start`), and line number.
    - **Token Extraction**: Uses a state machine to process characters and generate tokens.

   ```java
   public List<Token> scanTokens() {
       while (!isAtEnd()) {
           start = current;
           scanToken();
       }
       tokens.add(new Token(EOF, "", null, line));
       return tokens;
   }
   ```

2. **Token Types**:
    - **Single-character tokens**: `(`, `)`, `{`, `}`, `+`, `-`, etc.
    - **Multi-character tokens**: `!=`, `==`, `>=`, `<=`.
    - **Literals**: Strings (`"hello"`), numbers (`42`, `3.14`).
    - **Keywords**: `if`, `while`, `true`, `false`.

3. **Handling Different Lexemes**:
    - **Strings**:
      ```java
      private void string() {
          while (peek() != '"' && !isAtEnd()) {
              if (peek() == '\n') line++;
              advance();
          }
          // Add token with trimmed quotes
      }
      ```
    - **Numbers**: Supports integers and floats.
      ```java
      private void number() {
          while (isDigit(peek())) advance();
          if (peek() == '.' && isDigit(peekNext())) {
              advance(); // Consume '.'
              while (isDigit(peek())) advance();
          }
          addToken(NUMBER, Double.parseDouble(source.substring(start, current)));
      }
      ```
    - **Identifiers/Keywords**:
      ```java
      private void identifier() {
          while (isAlphaNumeric(peek())) advance();
          String text = source.substring(start, current);
          TokenType type = keywords.getOrDefault(text, IDENTIFIER);
          addToken(type);
      }
      ```

4. **Error Handling**:
    - Reports unterminated strings and invalid characters.
   ```java
   static void error(int line, String message) {
       System.err.println("line[" + line + "] Error: " + message);
       hadError = true;
   }
   ```

---

## Results
The lexer successfully tokenizes input strings. Below are example outputs:

### Input 1:
```java
var x = 42 + 3.14;
print "Hello, world!";
```  

### Output 1:
```
VAR var null  
IDENTIFIER x null  
EQUAL = null  
NUMBER 42 42.0  
PLUS + null  
NUMBER 3.14 3.14  
SEMICOLON ; null  
PRINT print null  
STRING "Hello, world!" Hello, world!  
SEMICOLON ; null  
EOF  null  
```  

### Input 2:
```java
if (x >= 5) { print "Done!"; }
```  

### Output 2:
```
IF if null  
LEFT_PAREN ( null  
IDENTIFIER x null  
GREATER_EQUAL >= null  
NUMBER 5 5.0  
RIGHT_PAREN ) null  
LEFT_BRACE { null  
PRINT print null  
STRING "Done!" Done!  
SEMICOLON ; null  
RIGHT_BRACE } null  
EOF  null  
```  

---

## Conclusion
This lab involved implementing a lexer that converts source code into tokens. The lexer handles:
- **Operators and punctuation** (e.g., `+`, `>=`).
- **Literals** (strings, integers, floats).
- **Keywords and identifiers**.
- **Error reporting** for invalid syntax.

Challenges included managing state transitions for multi-character tokens (e.g., `==`) and handling edge cases like unterminated strings. Future improvements could include support for escape characters in strings and multi-line comments.

---

## References
1. [Crafting Interpreters](https://craftinginterpreters.com/) by Robert Nystrom.
2. [Lexical Analysis - Wikipedia](https://en.wikipedia.org/wiki/Lexical_analysis).

---

## Repository Link
[GitHub Repository](https://github.com/MaxKostov/dsl)