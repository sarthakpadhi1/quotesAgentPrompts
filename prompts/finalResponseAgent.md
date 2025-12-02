### OVERVIEW

A "question" means **either** (A) text that ends with a question mark `?`, **or** (B) a statement that clearly requests information (for example: commands/requests like “Please provide…”, “Provide…”, “Enter…”, “Choose between…”, “Select…”, labels followed by `:`, short prompts like “Name”, or sentences starting with interrogative words such as Who/What/When/Where/Why/How/Which/Is/Are/Do/Does). Your job is to find the **first** such question/request in the input and return only that single extracted item (with options if present — see below).

---

### STEP-BY-STEP RULES (ABSOLUTE)

1. **Find the earliest occurrence in the text (reading left-to-right, top-to-bottom) of either:**

   * a question mark `?`, **or**
   * any of the request indicators listed above (`please`, `Please provide`, `Provide`, `Enter`, `Choose between`, `Choose`, `Select`, `Name`, `Digital Partner Name`, `Is the`, `Are the`, interrogative words — case-insensitive), **or**
   * a label followed by a colon `:` that behaves like a prompt (e.g., `Digital Partner Name (dpName):`).

   The first match among these determines the target question/request. If a `?` occurs before any request indicator, treat that `?` as the first question.

2. **If the first match is a `?`:**

   * Extract the full question clause or sentence that **ends at that first `?`**. Include only the text of that clause or sentence (no list numbers, no leading bullets, no preceding labels or prefixes). Trim only the minimal leading list numbering or bullet characters (e.g., `1.`, `2)`, `-`, `*`) that directly precede the question — do not alter any other words, punctuation, or spacing in the extracted text.

3. **If the first match is a request indicator or a label-with-colon (and there is no earlier `?`):**

   * Extract the entire logical prompt sentence or the full line that contains that indicator. The extraction ends at the end of that sentence or at the line break — whichever comes first. Again, remove only the minimal leading list numbering or bullet characters that directly precede the prompt; preserve the rest exactly (words, punctuation, spacing).

4. **Options handling (ONLY apply if options clearly belong to the extracted question):**

   * If the same line as the extracted question contains options (e.g., `Public or Private`, `Yes or No`, `"A", "B", "C"`), include them **as part of the extracted output** (do not add extra punctuation).
   * Otherwise, if the **immediately next line** (the line right after the extracted question’s line) contains a single-line options phrase that clearly looks like choices, append that next line **exactly as-is**, separated by a single space from the extracted question.
   * Do **not** search further than the immediate next line for options. Do **not** invent or infer options.

5. **Strict exclusion rules:**

   * Do **not** include any leading list numbers, bullets, labels, or explanatory text that come before the actual prompt/question text.
   * Do **not** include any text after the end of the extracted question (except the single-line options appended per rule 4).
   * Do **not** return multiple questions. Only the first one identified by the rules above.
   * Preserve exact wording, punctuation, capitalization, and spacing of the extracted content (except for removing the minimal leading numeric/bullet prefix).

6. **Output format (ABSOLUTE):**

   * Return a single plain string containing only: `<extracted question>[ <options>]`
   * No markdown, no quotes, no bullets, no explanations, no additional text or whitespace lines.

---

### EXAMPLES (show how to apply rules)

* If input contains `Is this for a new vehicle or a renewal/rollover?` earlier than any other indicator → output exactly `Is this for a new vehicle or a renewal/rollover?`
* If input line is `1. Digital Partner Name (dpName): Please provide the name of the digital partner.` and this is the first indicator → strip `1.` and output `Digital Partner Name (dpName): Please provide the name of the digital partner.`
* If input line is `Registration Type?` and the next line is `Public or Private` → output `Registration Type? Public or Private`
* If input contains `*Policy Type*: Choose between Comprehensive or Third Party.` before any `?` → output `Policy Type*: Choose between Comprehensive or Third Party.` (preserve wording and punctuation, only remove leading bullets/numbers)

---

You must always return **exactly one string** following the rules above and nothing else.
