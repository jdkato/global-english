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

Requires Vale 3.21.0 or later. GlobalEnglish builds on
[Std](https://github.com/vale-cli/Std), the standard library its Latin and
sentence-length rules extend. Sync pulls Std in, and it stays present
without being enabled.

```ini
StylesPath = styles
Packages = GlobalEnglish

[*.md]
BasedOnStyles = GlobalEnglish, Strict
```

```console
$ vale sync
```

`GlobalEnglish` is in the [package library](https://vale.sh/explorer), so
the name is enough. To pin a version, give its release URL instead:
`https://github.com/jdkato/global-english/releases/download/<version>/GlobalEnglish.zip`.

`GlobalEnglish` is the core style and is always on. `Strict` and
`Controlled` are opinions you can take or leave. `ControlledVocabulary`
holds one rule that needs a dictionary you supply; see
[Approved vocabulary](#approved-vocabulary).

| Style | What it checks |
| ----- | -------------- |
| `GlobalEnglish` | Idioms, phrasal verbs, Latin, slang, humor, words with two meanings, and sentence structure. See below. |
| `Strict` | Contractions, written out as two words, and sentences over 25 words. |
| `Controlled` | Procedures and maintenance text: one command per step, steps written as commands, short steps and paragraphs, articles, -ing forms, and the passive voice in steps. |
| `ControlledVocabulary` | Words outside an approved vocabulary that you provide. |

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
| Controlled | `StepLength` | a sentence of more than 20 words in a numbered step | |
| | `OneAction` | `Remove the cover and disconnect the hose.` | |
| | `Imperative` | `You must close the valve.`, `Please close the valve.` | `Close` |
| | `StepPassive` | `The filter is removed by hand.` in a numbered step | |
| | `Article` | `Remove cover.` | |
| | `IngForms` | `Using a wrench, turn the filter.`, `before installing` | |
| | `ParagraphLength` | a paragraph of more than six sentences | |
| ControlledVocabulary | `Approved` | any word not in your `Approved` dictionary | |

Every rule file starts with a comment that says what it checks and why it
matters to a reader in a second language. Most rules link to the guidance
they come from. `Idioms`, `Latin`, `Slang`, `SetUp`, and `Approved` are
warnings. The others are suggestions, because the alert asks a question
that only the writer can answer.

`Since`, `While`, and `As` skip the uses that have one meaning: `since
version 2`, `while running`, `as shown`, `as soon as`. `May` reads only the
lowercase word, so the month is not flagged.

### Controlled

`Controlled` is for procedures and maintenance-style documentation. It
follows principles common to controlled languages such as ASD-STE100. This
package is not affiliated with, approved by, or endorsed by ASD, and
ASD-STE100 is a trademark of ASD. The rules come from general guidance on
procedures and plain language, listed under [Sources](#sources). The
package contains no part of any controlled language's specification.

The step rules read numbered list items, which is how a procedure is
written. A bulleted list is description and is left alone. `Article` needs Vale
3.27.0 or later to read a short step; earlier versions miss it.
`Strict.SentenceLength` stays the limit for descriptive text.

### Approved vocabulary

`ControlledVocabulary.Approved` reports every word that is not in a Hunspell
dictionary named `Approved`, so a team with a licensed or in-house
vocabulary can enforce it. No dictionary ships with this package. Put
`Approved.dic` and `Approved.aff` in `config/dictionaries/` on your
`StylesPath`, then turn the rule on:

```ini
[*.md]
BasedOnStyles = GlobalEnglish, Controlled
ControlledVocabulary.Approved = YES
```

When the dictionary files are missing, Vale stops with an `E201` error
that names the directory it searched, and lints nothing. For this reason,
the rule is in its own style and is not part of `Controlled`.

## Configuration

Limits are parameters, so you can change them without editing the rule:

```ini
[*.md]
BasedOnStyles = GlobalEnglish, Strict
Strict.SentenceLength[max] = 20
Controlled.StepLength[max] = 15
Controlled.ParagraphLength[condition] = "> 8"
GlobalEnglish.May = NO
```

## What it does not check

Dates, numbers, units, and currency formats; terms used for one thing in
one place and another thing elsewhere; and text inside images. A style
cannot tell which `This` has a clear antecedent in the sentence before it,
so `BarePronoun` asks every time; for `It`, it asks only about `It is` and
`It was`. `NounStack`, `OneAction`, and `Article` read the part-of-speech
tags of each word, and a word tagged wrong can still trigger them or slip
past.

`Controlled` ships no approved vocabulary. No rule can check that a word is
used in only one meaning, or that a technical name is the one your project
uses.

## Sources

- Google's style guide for developers, the page
  [Writing for a global audience](https://developers.google.com/style/translation):
  `PhrasalVerbs`, `Slang`, `Humor`, `NounStack`, `SentenceLength`.
- The same guide, the [Word list](https://developers.google.com/style/word-list):
  `SetUp`, `Latin`, `Since`, `While`, `As`, `May`, `BarePronoun`.
- The same guide, [Procedures](https://developers.google.com/style/procedures):
  `StepLength`, `OneAction`, `Imperative`.
- The Microsoft Writing Style Guide, the page
  [Global communications: Writing tips](https://learn.microsoft.com/en-us/style-guide/global-communications/writing-tips):
  `Idioms`, `OptionalThat`, `IngForms`, `Article`, `StepPassive`.
- The guide to plain language on Digital.gov, the page
  [Clear and short](https://digital.gov/guides/plain-language/writing/clear-short#write-short-paragraphs):
  `ParagraphLength`.

`Contractions` has no source. Both guides allow common contractions; the
rule is for teams whose translation process does not. `Approved` has no
source; it enforces whatever vocabulary you give it.

## Development

`./test.sh` runs each rule's own cases with `vale test --coverage`, then
checks `fixtures/page.md`, `fixtures/page.rst`, `fixtures/procedure.md`,
and `fixtures/approved.md` against the golden files in `testdata/` and
requires their rewrites in `fixtures/clean/` to pass clean. The
`Approved` cases read a small test dictionary from `fixtures/dictionaries/`,
which is not part of the package. `./test.sh -u` rewrites the golden files. The script expects a Std
checkout beside this repository, or `STD` pointing at its `Std` directory.
