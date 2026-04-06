# Benchmarks

4 tasks. Real responses. Token counts via tiktoken `cl100k_base`. No rounding, no cherry-picking.

> **Two numbers matter:** *Output reduction* — how much shorter responses get. *Session savings* — the real-world number after accounting for input tokens (which don't change). We report both.

---

## 1. Web Search

**Prompt:** "Search for the latest Node.js LTS version"

<table>
<tr>
<td width="50%">

**Normal (82 tokens)**
```
I'll search for the latest Node.js LTS version for you.

Based on my search, the latest Node.js LTS version is
Node.js 22.x (codename "Jod"). It entered LTS status
in October 2024 and will be maintained until April 2027.
The current LTS release is v22.14.0.

Would you like me to help you install or upgrade to
this version?
```

</td>
<td width="50%">

**Caveman (26 tokens)**
```
Node.js 22.x LTS ("Jod"), current v22.14.0.
Maintained until April 2027.
```

</td>
</tr>
</table>

**Result:** 82 → 26 tokens. **68% reduction.** Est. session savings: 14–27%.

All facts preserved. Preamble ("I'll search for"), offer ("Would you like me to") — gone.

---

## 2. Code Edit

**Prompt:** "Add input validation to this function" (given `createUser(name, email)`)

<table>
<tr>
<td width="50%">

**Normal (192 tokens)**
```
I'll add input validation to the createUser function.
Here's what I'm adding:
- Name validation: checking it's a non-empty string
- Email validation: checking it matches a basic email pattern
- Type checking for both parameters

Here's the updated function:

function createUser(name, email) {
  if (!name || typeof name !== 'string') {
    throw new Error('Name is required and must be a string');
  }
  if (!email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
    throw new Error('A valid email is required');
  }
  return { name: name.trim(), email: email.toLowerCase() };
}

I've added validation for both the name and email
parameters. The name must be a non-empty string, and
the email must match a basic email format pattern.
Let me know if you'd like me to add any additional
validation!
```

</td>
<td width="50%">

**Caveman (96 tokens)**
```
Added name and email validation:

function createUser(name, email) {
  if (!name || typeof name !== 'string') {
    throw new Error('Name is required and must be a string');
  }
  if (!email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
    throw new Error('A valid email is required');
  }
  return { name: name.trim(), email: email.toLowerCase() };
}
```

</td>
</tr>
</table>

**Result:** 192 → 96 tokens. **50% reduction.** Est. session savings: 10–20%.

Code identical. The pre-explanation ("Here's what I'm adding"), post-summary ("I've added validation for both..."), and offer ("Let me know if...") — all waste.

---

## 3. File Exploration

**Prompt:** "Find all TypeScript test files"

<table>
<tr>
<td width="50%">

**Normal (148 tokens)**
```
I'll search for all TypeScript test files in your
project. Let me look through the codebase for files
matching common test patterns.

I found the following TypeScript test files:

- src/__tests__/auth.test.ts
- src/__tests__/api.test.ts
- src/__tests__/utils.test.ts
- src/components/__tests__/Button.test.tsx
- src/components/__tests__/Modal.test.tsx
- e2e/login.spec.ts
- e2e/checkout.spec.ts

That's 7 test files total — 5 unit tests in
src/__tests__/ and src/components/__tests__/, plus
2 end-to-end tests in e2e/. Would you like me to
look at any of these?
```

</td>
<td width="50%">

**Caveman (71 tokens)**
```
Found 7 test files:
- src/__tests__/auth.test.ts
- src/__tests__/api.test.ts
- src/__tests__/utils.test.ts
- src/components/__tests__/Button.test.tsx
- src/components/__tests__/Modal.test.tsx
- e2e/login.spec.ts
- e2e/checkout.spec.ts
```

</td>
</tr>
</table>

**Result:** 148 → 71 tokens. **52% reduction.** Est. session savings: 10–21%.

Same 7 paths. The narration ("Let me look through..."), redundant summary ("That's 7 test files total — 5 unit tests..."), and follow-up offer — all cut.

---

## 4. Q&A Explanation

**Prompt:** "Explain what this error means: `TypeError: Cannot read properties of undefined (reading 'map')`"

<table>
<tr>
<td width="50%">

**Normal (209 tokens)**
```
This error means you're trying to call the .map()
method on a value that is undefined. This is one of
the most common JavaScript errors, and it typically
happens when:

1. Data hasn't loaded yet: You're trying to map over
   an array that comes from an API call or async
   operation, but it hasn't been fetched yet.
2. Wrong property path: You're accessing a nested
   object property that doesn't exist.
3. Missing default value: A function parameter or
   state variable wasn't initialized with a default
   array value.

Common fixes:
- Add optional chaining: data?.map(...)
- Provide a default: (data || []).map(...)
- Add a guard: if (data) { data.map(...) }
- Initialize state with an empty array: useState([])

Would you like me to help find where this is
happening in your code?
```

</td>
<td width="50%">

**Caveman (58 tokens)**
```
Calling .map() on undefined — something expected
to be an array isn't set yet.

Fixes:
- data?.map(...) — optional chaining
- (data || []).map(...) — default to empty array
- useState([]) — initialize state as array
```

</td>
</tr>
</table>

**Result:** 209 → 58 tokens. **72% reduction.** Est. session savings: 14–29%.

Root cause explained in one line. Three fixes listed. Redundant cause taxonomy and follow-up offer — removed.

---

## Summary

| Task | Normal | Caveman | Output Reduction |
|------|-------:|--------:|-----------------:|
| Web Search | 82 | 26 | 68% |
| Code Edit | 192 | 96 | 50% |
| File Exploration | 148 | 71 | 52% |
| Q&A Explanation | 209 | 58 | 72% |
| **Average** | **158** | **63** | **61%** |

**Estimated total session savings: 12–24%** (input tokens make up 60–80% of session totals — they don't change).

### Where savings are highest

- **Q&A and search** — lots of ceremony per response, little code. Caveman cuts deep.
- **Short exchanges** — filler is a larger percentage of short answers.

### Where savings are lowest

- **Code generation** — the code itself doesn't shrink, only the wrapper. A 200-line function is 200 lines either way.
- **Long debugging sessions** — explanations are often necessary, not waste.

### The compounding effect

Every output token becomes an input token on the next turn. Over a 50-turn session, verbose responses accumulate 1,000–2,000 tokens of filler in the context window — re-processed every turn. Terse output keeps the whole session leaner.
