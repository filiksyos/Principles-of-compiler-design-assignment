Principles of compiler design assignment

Part 1: Student 53 (Assignment 01)
The following tasks correspond to the requirements for Student 53 found in the "Principles of
Compiler Design on Syntax Analysis Individual Assignment"
1
.
1. (Theory): What is syntax-directed translation?
Syntax-Directed Translation (SDT) is a method of compiler implementation where the
translation of a language construct is completely guided by the syntax of the language (its
Context-Free Grammar). In SDT, we associate attributes (such as data types or values) with
grammar symbols and attach semantic rules (or actions) to the productions of the grammar.
When the parser matches a production (e.g., $E \to E + T$), the associated semantic rule is
executed (e.g., E.val = E1.val + T.val). This allows the compiler to generate intermediate code,
construct syntax trees, or perform type checking simultaneously while parsing the input.
2. (C++): Write a C++ function that evaluates a string containing a simple
arithmetic expression with + only.
The following C++ function parses a string (e.g., "10+20+5") and calculates the sum.
#include <iostream>
#include <string>
#include <vector>
#include <sstream>
// Function to evaluate a string with only non-negative integers and '+'
int evaluateExpression(const std::string& expression) {
std::stringstream ss(expression);
std::string segment;
int sum = 0;
// Use getline with delimiter '+' to split the string
while (std::getline(ss, segment, '+')) {
// Convert the substring to an integer and add to sum
// Note: In a real compiler, we would handle whitespace and errors here.
if (!segment.empty()) {
sum += std::stoi(segment);
}
}
return sum;
}
int main() {
std::string input = "10+5+2+100";
std::cout << "Input: " << input << "\n";
std::cout << "Result: " << evaluateExpression(input) << std::endl;
return 0;
}
3. (Problem-solving): Parse Tree Construction
Grammar:
$$S \to AB$$
$$A \to aA \mid \epsilon$$
$$B \to bB \mid \epsilon$$
Target String: "aaab"
Derivation Step-by-Step:
To generate "aaab", $A$ must generate "aaa" and $B$ must generate "b".
1. $S \to AB$
2. $A \to aA \to aaA \to aaaA \to aaa\epsilon$ (effectively "aaa")
3. $B \to bB \to b\epsilon$ (effectively "b")
Parse Tree Visual Description:
● Root: $S$ has two children: $A$ and $B$.
● Left Branch ($A$):
○ $A$ expands to $a$ and $A$.
○ The second $A$ expands to $a$ and $A$.
○ The third $A$ expands to $a$ and $A$.
○ The fourth $A$ expands to $\epsilon$.
● Right Branch ($B$):
○ $B$ expands to $b$ and $B$.
○ The second $B$ expands to $\epsilon$.
Part 2: Lexical Analysis (Assignment 02)
1. How are identifiers and keywords distinguished?
This question refers to the Lexical Analysis presentation topics
2
.
Identifiers and keywords are distinguished during the lexical analysis (scanning) phase
using one of the following standard methods:
1. Symbol Table/Reserved Word Lookup (Most Common): The scanner treats everything
that matches the pattern for a "word" (e.g., [a-zA-Z_][a-zA-Z0-9_]*) initially as an
identifier. Once a word is recognized, the scanner checks it against a predefined list (or
hash table) of reserved keywords (e.g., if, while, int).
○ If the word exists in the table, it is returned as a Keyword Token.
○ If it does not exist, it is returned as an Identifier Token.
2. Order of Priority in Regular Expressions: If using a tool like Lex or Flex, rules are often
prioritized. The rules for keywords are placed before the rule for identifiers.
○ Rule 1: "if" { return IF; }
○ Rule 2: "while" { return WHILE; }
○ Rule 3: [a-zA-Z]+ { return ID; }
○ Since lexers typically match the longest prefix (and first rule in case of ties), the input
"if" matches Rule 1. The input "iff" would not match Rule 1 (too long) but would match
Rule 3.
Part 3: Semantic Analysis & Scopes (Assignment 03)
1. Add support for loop scopes with continue/break labels.
This task focuses on implementing semantic checks for labeled control flow
3
.
Implementation Strategy:
To implement this in a compiler's semantic analysis phase, you need a data structure to track
the "active" control flow contexts (loops and switches).
1. Symbol Table Augmentation:
○ Maintain a Scope Stack specifically for control structures.
○ When entering a loop (e.g., for, while), push a new context onto the stack. If the loop
has a label (e.g., outerLoop: while(...)), record the label name in that context.
2. Handling break / continue:
○ Unlabeled: Check if the current stack is empty. If not empty, the statement is valid (it
applies to the innermost loop). If empty, report an error (e.g., "break statement not
within loop or switch").
○ Labeled: When the compiler encounters break labelName;, it must traverse the
scope stack from top to bottom (innermost to outermost).
■ Compare labelName with the label recorded in each stack entry.
■ Valid: If a match is found, the statement is valid.
■ Invalid: If the stack is exhausted without finding the label, report a semantic
error (e.g., "Label 'labelName' not found or not enclosing this statement").
3. Validation Logic (Pseudocode):
Plaintext
function checkBreak(targetLabel):
if targetLabel is null:
if loopStack is empty:
error("break used outside of loop")
else:
found = false
for scope in loopStack (reversed):
if scope.label == targetLabel:
found = true
break
if not found:
error("Target label '" + targetLabel + "' does not exist for this break")
