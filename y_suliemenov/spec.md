# Top Words Specification

## 1. Purpose

The goal is to find the 10 most frequently used words in a news article. The program must support English, Russian, and Kazakh text.

The specification defines exactly how words are identified, counted, sorted, and returned.

## 2. Function Interface

The program must be written in Python 3.

The function must be named `top_words` and located in `top_words.py`.

```python
def top_words(text: str) -> list[tuple[str, int]]:
```

Input: a string containing article text.

Output: a list of tuples. Each tuple contains a word and the number of times it appears.

The function must return the result, not print it.

## 3. Word Identification

A word is a continuous sequence of Unicode alphabetic characters.

A character belongs to a word if Python's `str.isalpha()` returns `True`.

Spaces, punctuation, digits, symbols, and other non-alphabetic characters separate words.

Apostrophes and hyphens are treated as separators. Combining marks that do not satisfy `isalpha()` are also separators. Unicode normalization is not performed.

Examples:

| Input | Expected output |
|---|---|
| `hello, world!` | `[("hello", 1), ("world", 1)]` |
| `don't stop` | `[("don", 1), ("stop", 1), ("t", 1)]` |
| `well-known` | `[("known", 1), ("well", 1)]` |
| `hello123world` | `[("hello", 1), ("world", 1)]` |
| `12345 !!!` | `[]` |

## 4. Case Sensitivity

The function must ignore letter case.

After splitting the text into words, each word must be converted using Python's `str.casefold()` method.

Words with the same resulting string are counted together.

Examples:

| Input | Expected output |
|---|---|
| `Apple apple APPLE` | `[("apple", 3)]` |
| `HELLO hello` | `[("hello", 2)]` |
| `Москва МОСКВА` | `[("москва", 2)]` |
| `Қазақ қазақ ҚАЗАҚ` | `[("қазақ", 3)]` |

## 5. Word Counting

Every occurrence of a word increases its frequency by one.

Words are compared after case conversion.

Examples:

| Input | Expected output |
|---|---|
| `cat dog cat bird dog cat` | `[("cat", 3), ("dog", 2), ("bird", 1)]` |
| `red blue red green red` | `[("red", 3), ("blue", 1), ("green", 1)]` |
| `one one one` | `[("one", 3)]` |

## 6. Sorting Rules

Words must first be sorted by frequency in descending order.

If multiple words have the same frequency, they must be sorted in ascending lexicographical order according to Python's default Unicode string comparison.

The original order of words in the article must not affect the final order.

Examples:

| Input | Expected output |
|---|---|
| `dog cat bird` | `[("bird", 1), ("cat", 1), ("dog", 1)]` |
| `banana apple banana apple` | `[("apple", 2), ("banana", 2)]` |
| `cat dog cat apple dog` | `[("cat", 2), ("dog", 2), ("apple", 1)]` |

## 7. Number of Results

The function must return a maximum of 10 unique words.

If fewer than 10 unique words exist, it must return all of them.

If there are no valid words, it must return an empty list.

Examples:

| Input | Expected output |
|---|---|
| `cat dog` | `[("cat", 1), ("dog", 1)]` |
| `a b c d e f g h i j k` | `[("a", 1), ("b", 1), ("c", 1), ("d", 1), ("e", 1), ("f", 1), ("g", 1), ("h", 1), ("i", 1), ("j", 1)]` |
| Empty string | `[]` |
| `123 !!!` | `[]` |

## 8. Multilingual Support

The function must support English, Russian, and Kazakh words in the same input.

All languages follow the same rules for word identification, counting, and sorting.

No translation, stemming, or removal of common words is performed.

Examples:

| Input | Expected output |
|---|---|
| `hello привет сәлем hello` | `[("hello", 2), ("привет", 1), ("сәлем", 1)]` |
| `қала қала мектеп` | `[("қала", 2), ("мектеп", 1)]` |
| `кот кот собака` | `[("кот", 2), ("собака", 1)]` |

## 9. Input Validation

The function accepts only string values.

If the input is not a string, it must raise `TypeError`.

Examples:

```python
top_words(None)     # Raises TypeError
top_words(123)      # Raises TypeError
top_words([])       # Raises TypeError
top_words("")       # Returns []
```

## 10. Additional Requirements

- The function must not modify its input.
- The function must not read or write files.
- The function must not access the network.
- The function must not print anything.
- The result must be deterministic.
- Each returned frequency must be a positive integer.
- Every returned word must be normalized using `casefold()`.

## 11. Acceptance Criteria

The implementation is correct if:

1. It follows all word identification rules.
2. It counts words without case sensitivity.
3. It correctly supports English, Russian, and Kazakh.
4. It sorts by frequency and then alphabetically.
5. It returns no more than 10 words.
6. It handles empty input and invalid input types.
7. It produces the expected output for every example in this specification.