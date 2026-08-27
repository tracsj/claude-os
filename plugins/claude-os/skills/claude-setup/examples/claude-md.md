# CLAUDE.md Examples

## Good root CLAUDE.md (an independent bookshop)

```markdown
# Fenwick Books — Root Context

## Project
A neighborhood bookshop with a weekly newsletter, an events program,
and a small online store. Everything customer-facing should sound like
a bookseller talking, not like marketing.

## Audience
Local readers, mostly regulars. They come for recommendations they
would not have found themselves. They can tell when enthusiasm is
manufactured, so never manufacture it.

## Voice
- Conversational and specific. Name the book, not the category.
- Use "we" for the shop, "I" when a bookseller is signing a pick.
- Never use: "must-read", "unputdownable", "dive into", exclamation
  marks in headers, or a blurb lifted from the publisher.
- Say what a book is actually like, including who will not enjoy it.

## Always
- Check @context/audience.md before writing anything customer-facing
- Follow the process in @.claude/skills/newsletter/SKILL.md
- Commit after every meaningful change with a descriptive message
- Run `/wrap-up` at the end of every session

## Never
- Post or publish anything without my explicit approval
- Modify .env or any credentials file
- Quote a review we have not actually read

## Stack
Shopify storefront, Mailchimp for the newsletter, a shared calendar
for events. See @context/tools.md for API details.
```

---

## Good context/audience.md

```markdown
# Audience Profile

## Who They Are
Regulars within walking distance, plus a mailing list of about 900.
Committed readers rather than completists — they finish maybe a book a
fortnight and would rather be handed one good thing than a list of ten.

## What They're Struggling With
- Too much published, no way to tell what is worth the evening
- Algorithmic recommendations that all point the same direction
- Having read the obvious books already
- No one to talk to about a book once it is finished

## Language They Use
"I want something like X but not X", "I bounced off it",
"I don't have the patience for a slow first hundred pages",
"what are *you* reading"

## What They Respond To
- A named bookseller standing behind a specific pick
- Honest caveats — pacing, length, difficulty
- Connections between books they already love
- Events where the author actually takes questions

## What Turns Them Off
- Publisher blurb reprinted as a recommendation
- Superlatives with nothing underneath them
- Being sold the same bestseller as everyone else
- Pretending a difficult book is easy
```
