# LLM Review — Top Words Specification

## Model Used

OpenAI ChatGPT, GPT-6.

Review performed on October 23, 2026, using the attached `spec.md` in this conversation. This review replaces the unfilled template in the original pull request.

## Review Prompt

Review the attached `spec.md` for the Top Words problem as a strict software specification reviewer. Check for ambiguous or missing requirements, contradictory examples, case-folding and Unicode edge cases, deterministic counting and sorting, the correct Python version, and testable acceptance criteria. For each issue, explain why it matters and suggest a concrete correction. Then review the corrected specification and check each expected output against a Python implementation.

The complete source for this review was the `spec.md` file in this same submission. The implementation and test checks were performed after updating that specification.

## Initial Review — Issues Found

1. **Python version was underspecified.** The original document said "Python 3", but its annotation `list[tuple[str, int]]` requires Python 3.9 or later. On some older Python 3 versions, the annotation will not work as written.
   - **Correction:** Specify Python 3.9 or newer.

2. **Case folding can change the number or identity of Unicode characters.** Although the original text said to call `casefold()` after tokenizing, there was no edge-case example that distinguished this from case folding before tokenizing.
   - **Counterexample:** `İ i` produces `[('i', 1), ('i\u0307', 1)]` if tokens are obtained before case folding. The second key contains a combining dot; re-tokenizing afterward could give a different result.
   - **Correction:** State explicitly that no second tokenization takes place, and include this example.

3. **Unicode normalization was specified but not illustrated with a distinguishing example.** A developer could wrongly assume that visually similar composed and decomposed forms should be equivalent.
   - **Counterexample:** `café cafe\u0301` produces `[('cafe', 1), ('café', 1)]` under the chosen rule because the combining mark is a separator and normalization is not performed.
   - **Correction:** Add the concrete test case and explain the behavior.

4. **The ten-result limit needed stronger boundary examples.** The original specification covered eleven tied words, but not a case where the same two words have very different frequencies.
   - **Correction:** Add an example with `apple` occurring ten times and `zebra` twice to confirm that counts are preserved.

5. **The existing word-splitting decision is unusual for natural language but unambiguous.** For example, `don't` becomes `don` and `t`. This follows the explicitly defined `isalpha()` rule, so it is not a contradiction.
   - **Decision:** Keep the rule rather than silently replace it with a different linguistic convention.

## My Evaluation and Changes

I accepted issues 1–4 and revised `spec.md` to clarify the Python version, processing order, Unicode behavior, and test coverage. I kept the apostrophe and hyphen behavior because the assignment allows a precise documented choice, and changing it is not required.

I did not accept the idea that all visually equivalent Unicode strings must be normalized. The chosen no-normalization rule is intentional, deterministic, and has explicit counterexamples. No review claims need to be struck through as factually false.

## Final LLM Review of Revised Specification

After the changes, the specification defines:

- the exact interface and supported Python versions;
- a word boundary using `str.isalpha()` and separation on other characters;
- no Unicode normalization and a defined tokenization-before-case-folding order;
- deterministic counting, descending frequency, Unicode string-order tie-breaking, and truncation to ten results;
- multilingual examples, empty input, and invalid input handling;
- concrete cases that disambiguate each significant design choice.

**Final review outcome:** No unresolved contradictions or missing behavior were found in the reviewed requirements. The selected approach is not a full linguistic tokenizer, but that limitation is explicitly specified and testable. This is a review conclusion, not a claim that all conceivable Unicode cases were formally proven.

## Implementation Verification

A Python implementation was generated for verification (not submitted as part of the PR). It used a character-by-character `isalpha()` tokenizer, `casefold()` on completed tokens, `collections.Counter`, and sorting by `(-count, word)`, returning the first ten tuples. It rejected non-string inputs with `TypeError`.

The implementation was executed against **25 input/output example rows** extracted from the revised `spec.md`:

- **25 passed, 0 failed.**
- Three additional invalid-input checks (`None`, `123`, `[]`) correctly raised `TypeError`.

In particular, the implementation matched the examples for punctuation, apostrophes, hyphens, digits, English/Russian/Kazakh case folding, Unicode combining marks, tied scores, and the ten-result limit.

The local verification code is not part of this assignment's commit because the requested submission consists of `spec.md` and `llm-review.md` only.
