# GlobalEnglish

[Vale](https://vale.sh) styles for global English: source text that is
ready for translation, and for readers whose first language is not English.
Idioms, phrasal verbs, Latin abbreviations, contractions, slang, and jokes
each have a plainer form, and most rules carry it as a fix. The other rules
find sentences that a reader, or a translation tool, has to parse twice.
They look for a conjunction with two meanings, a pronoun with no noun, a
stack of nouns, a dropped `that`, or too many words.

Works on [Markdown](https://docs.vale.sh/formats/markdown),
[reStructuredText](https://docs.vale.sh/formats/restructuredtext),
[AsciiDoc](https://docs.vale.sh/formats/asciidoc),
[HTML](https://docs.vale.sh/formats/html), [Org](https://docs.vale.sh/formats/org),
and [plain text](https://docs.vale.sh/formats/text) (Vale 3.23.0 or later).

## Install

Requires Vale 3.21.0 or later.

```ini
StylesPath = styles
Packages = https://github.com/jdkato/global-english/releases/latest/download/GlobalEnglish.zip

[*.md]
BasedOnStyles = GlobalEnglish, Strict
```

```console
$ vale sync
```

To pin a version, use a release URL instead:
`https://github.com/jdkato/global-english/releases/download/v0.1.0/GlobalEnglish.zip`.

`GlobalEnglish` is the core style and is always on. `Strict` is an opinion
you can take or leave.

| Style | What it checks |
| ----- | -------------- |
| `GlobalEnglish` | Idioms, phrasal verbs, Latin, slang, humor, words with two meanings, and sentence structure. See below. |
| `Strict` | Contractions, written out as two words, and sentences over 25 words. |

## Rules

| Group | Rule | Examples flagged | Fix |
| ----- | ---- | ---------------- | --- |
| Words | `Idioms` | `piece of cake`, `low-hanging fruit`, `drop the ball`, `silver bullet`, `ballpark figure` | `easy`, `easy improvements`, `make a mistake`, ... |
| | `PhrasalVerbs` | `find out`, `figure out`, `turn off`, `carry out`, `make sure` | `learn`, `determine`, `disable`, `perform`, `ensure` |
| | `SetUp` | `To setup the server`, `Setup your account` | `set up` |
| | `Latin` | `e.g.`, `i.e.`, `etc.`, `via`, `vs.`, `ad hoc` | `for example`, `that is`, `and so on`, ... |
| | `Slang` | `gonna`, `awesome`, `folks`, `tons of`, `FYI` | `going to`, `excellent`, `people`, `many`, `note` |
| | `Humor` | `lol`, `no pun intended`, `;)` | |
| Meaning | `Since` | `Since the cache is shared, ...` | `because` |
| | `While` | `The CLI is fast, while the API is flexible.` | `whereas`, `although` |
| | `As` | `As the token is shared, ...` | `because` |
| | `May` | `You may lose data.` | `can`, `might` |
| Structure | `BarePronoun` | `This clears the cache.`, `It is slow.` | |
| | `NounStack` | `the user account settings page` | |
| | `OptionalThat` | `Ensure the file exists.`, `Verify you have access.` | `Ensure that` |
| Strict | `Contractions` | `don't`, `it's`, `won't`, `you'll` | `do not`, `it is`, `will not`, `you will` |
| | `SentenceLength` | a sentence of more than 25 words | |

Every rule file starts with a comment that says what it checks and why it
matters to a reader in a second language. Most rules link to the guidance
they come from. `Idioms`, `Latin`, `Slang`, and `SetUp` are warnings. The others are suggestions, because the alert asks a
question that only the writer can answer.

`Since`, `While`, and `As` skip the uses that have one meaning: `since
version 2`, `while running`, `as shown`, `as soon as`. `May` reads only the
lowercase word, so the month is not flagged.

## Configuration

Limits are parameters, so you can change them without editing the rule:

```ini
[*.md]
BasedOnStyles = GlobalEnglish, Strict
Strict.SentenceLength[max] = 20
GlobalEnglish.May = NO
```

## What it does not check

Dates, numbers, units, and currency formats; terms used for one thing in
one place and another thing elsewhere; and text inside images. A style
cannot tell which `This` has a clear antecedent in the sentence before it,
so `BarePronoun` asks every time; for `It`, it asks only about `It is` and
`It was`. `NounStack` reads the part-of-speech tags of each word, and a
verb tagged as a noun can still trigger it.

## Sources

- [Writing for a global audience](https://developers.google.com/style/translation),
  in Google's style guide for developer documentation: `PhrasalVerbs`,
  `Slang`, `Humor`, `NounStack`, `SentenceLength`.
- [Word list](https://developers.google.com/style/word-list), in the same
  guide: `SetUp`, `Latin`, `Since`, `While`, `As`, `May`, `BarePronoun`.
- [Global communications: Writing tips](https://learn.microsoft.com/en-us/style-guide/global-communications/writing-tips),
  Microsoft Writing Style Guide: `Idioms`, `OptionalThat`.

`Contractions` has no source. Both guides allow common contractions; the
rule is for teams whose translation process does not.

## Development

`./test.sh` runs each rule's own cases with `vale test --coverage`, then
checks `fixtures/page.md` and `fixtures/page.rst` against the golden files
in `testdata/` and requires their rewrites in `fixtures/clean/` to pass
clean. `./test.sh -u` rewrites the golden files.
