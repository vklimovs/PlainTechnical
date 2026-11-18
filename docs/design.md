# Design

## Levels

- An `error` holds in every sentence it matches. It checks a form that a pattern
  names exactly. A semicolon, an emoji and a modal verb are such forms. So are
  the length of a sentence and a chat closing. A gate fails on it. A guard of an
  error rule names an exact form too, such as a mark of code or a name. A fix
  that needs a guess at the context makes the rule narrower, or a warning.
  No repeated group in an error rule runs without a bound.
- A `warning` needs a reader. Its rule depends on grammar or meaning, and a
  pattern matches these roughly. It can also be a phrase with a literal sense,
  as in 'the patch rewrote the rules'. A commit hook can fail on a warning,
  since a commit message is short and new.
- A `suggestion` is a readability measure. Pass `--minAlertLevel=suggestion`
  to see it.

## Sources

| Layer | Source | Version | License |
| --- | --- | --- | --- |
| Rules of the standard | [SimpleEnglish](https://github.com/AminBlg/SimpleEnglish), a paraphrase of ASD-STE100 Issue 9 | a6fcb4fde098 | MIT |
| Machine-writing patterns | [vale-ai-tells](https://github.com/tbhb/vale-ai-tells) | v1.37.0 | MIT |
| Machine-writing patterns | [Voices](https://github.com/jdkato/voices) | v0.2.0 | MIT |

Voices takes some of its patterns from no-ai-slop by Peter Yang, and its Direct
voice follows awesome-claude-output-styles by Serge Shima. Both are MIT. A rule
that names vale-ai-tells a96c5fe takes a fix from that commit, which follows
v1.37.0.

Each rule file names its source on its first line. A line that starts with
`Changed:` notes where the rule matches other text than its source. The
messages and the levels are this style's own.

The dictionary of the standard is not part of the style, because its license
does not permit copies.

## Coverage

Two public corpora pair text that ChatGPT wrote with text that people wrote on
the same subject. Some phrases occur far more often on the ChatGPT side, and no
rule matched them. They became tokens and one new rule, ChatReply:

- [HC3](https://huggingface.co/datasets/Hello-SimpleAI/HC3), in its wiki_csai
  part, answers 842 questions on computer science. It gave ChatReply, the
  `different` of FillerPhrase, the general `It is important to` of Hedge and
  `make informed decisions` in StockPhrase.
- [M4](https://github.com/mbzuai-nlp/M4) holds 335 wikiHow articles with a
  technical title. These gave the phrases of a tutorial, such as `By following
  these steps`. Its 3,000 arXiv abstracts gave phrases such as `provides
  insights into`.

ChatReply is an error. The tokens are warnings. In HC3, ChatReply flags 4
ChatGPT answers and 0 human answers. A corpus of 7,360 files that people wrote
holds the documentation, the manual pages and the HTML pages of free software
packages. On it, ChatReply gives 0 alerts, and the tokens from M4 add 6
warnings.

## Word count

SentenceLength, InstructionLength and ProceduralLength count words as rules 8.5
and 8.6 do. Each of these counts as one word:

- a path and a Windows path
- a parenthesis, also one that wraps a line
- a number with an abbreviated unit such as `5 MB`, and a number such as
  1,000,000
- an e-mail address, an address such as `10.0.0.1:80` and a time
- a `key=value` pair and an option such as `--port`
- a hyphenated word, after rule 8.7

InstructionLength counts a code span as one word, as rule 8.6 counts an
identifier. Vale hides a code span and a quotation from SentenceLength and
ProceduralLength, so they count neither. InstructionLength then reports a long
list item that SentenceLength does not reach.

## Conflicts between the sources

The standard decides each conflict.

| Conflict | Resolution |
| --- | --- |
| The standard bans contractions. Some upstream messages prescribe `doesn't`. | The messages use the full form. The style leaves out ContractionAvoidance. |
| The standard requires connectors such as `thus`, `as a result` and `for example`. FormalTransitions flags them. | FormalTransition keeps those connectors. |
| A safety label such as `CAUTION:` takes a capital letter after the colon. ColonUsage flags that capital. | The style leaves out ColonUsage. |
| The standard puts one fact in each sentence. Staccato and SentenceLengthVariance flag runs of short sentences. | Both are warnings. Join related facts with a connector. |
| Rule 3.6 sets the active voice, and permits the passive voice in a description when the agent is unknown. | PassiveVoice is a warning, since no pattern knows the agent. |
| The standard puts an article before a noun. SentenceStartRepetition counts `The` as an opener. | Articles do not count as openers. |
| The upstream remedy for an overclaim is to soften it. A softened claim needs a modal verb, and the standard bans modal verbs. | Overclaim asks for the scope of the claim. |
| vale-ai-tells and Voices name other replacements than SimpleEnglish, such as `strong` for `robust` and `speed up` for `streamline`. | InflatedWord uses the replacements of SimpleEnglish. |
| An instruction has 20 words at most, and a description has 25. A list item can be either. | SentenceLength is an error above 25 words. InstructionLength and ProceduralLength warn above 20 words, in a list item and in a sentence that opens on a command. They leave a sentence that SentenceLength reports. |

## Left out

| Upstream rule | Reason |
| --- | --- |
| BareHolds, BareNames, BareReaches | `holds the lock`, `names the host` and `can reach the port` are literal in systems prose. |
| CommitFigurativeVerbs | `the packet arrives`, `the thread joins` and `edited by hand` are literal in a commit of systems code. |
| ShipOveruse, MortalityMetaphors, FigurativeFires, FigurativeFalls, FigurativeRuns, FigurativeSweeps, FigurativeWins, FigurativeHolds, FigurativeStays | A package ships a file, a value survives a reboot, an alert fires, a case falls through, and a variable outranks another. |
| AnthropomorphicAdjectives, EvasionMetaphors, EnforcementMetaphors | `healthy exporter`, `routes around a member` and `gated task` are terms of the trade. |
| UniversalSubject, UniversalObject, FillerIntensifier | `every host` and `a single file` are exact counts. |
| FormalRegister, VocabularySwap, CommitGitJargon | They flag `implementation`, `unlock` and `the tree`. |
| CataphoricForecasting, VerbTricolon, VerbTricolonDensity, TricolonDensityDocument, ContrastiveNegation | They read a list of nouns or a count as a tell. |
| HeadingTitleCase, ComplexWordDensity, LongWordDensity, AverageSentenceLength, ContentDuplication, PassiveDensity, Length (Voices) | SentenceLength and PassiveVoice give the same signal. Technical words are long. |
| EmDashUsage, SemicolonUsage, DoubleHyphen, Dashes (Voices) | Dash and Semicolon flag a dash and a semicolon between two words of prose. A double hyphen with no space around it is a flag, such as `--force`. |
| Recap (Voices) | ConclusionMarker flags the same openers. |
| AIAdjectiveNounPairs, OverusedVocabularyVerbs, InflatedWords (Voices), WeakVerbs (Voices) | InflatedWord, OverusedWord, FillerPhrase and Nominalization flag the same words. |
| ConsequenceParticiple | ParticipleTail flags the same tails. |
| EmptyPaddingStacked | A sequence rule has no optional word, and a skipped word can be the colon in `This much is certain: delays`. |
| RedundantPrecaution | Idiom flags the same phrases. |
| SequencingMarkers | FormalTransition flags `Firstly` and `Lastly`. `The second step` is part of a procedure. |
| Hedging (Voices) | Hedge, ModalVerb and ValueVerdict flag the same phrases. |
| ColonUsage | A safety label such as `WARNING: Do not open the valve.` takes a capital after the colon. |
| ContractionAvoidance | Rule 4.2 asks for the full form. |
| FigurativeAnchor | An anchor is literal in a regular expression and in a link. |
| HouseStyle | `house style` is a term of the trade. |
| Weasel (Voices) | VagueAttribution holds its tokens. |
| NounString | Its tagger reads some verbs as nouns and misses `the connection pool timeout configuration value`, and its time grows with the square of the sentence. Rule 2.1 stays a check for the reader. |
| TransitionRepetition | FormalTransition flags each formal transition, and rule 4.4 asks for the connectors it counts. |
| SentenceStartEntropy | SentenceStartRepetition gives the same signal. |
| BinaryContrast (Voices) | ContrastiveFormula flags the same frames, and the `but` form flags a correct statement, as in `A POST is not idempotent by default, but`. |
| CommitChangelogStyle, CommitFileListing | Git strips a line that starts with `#`. Vale reads each list item on its own. |
| CommitEmoji | Emoji reads a commit too. |
| The Claude, Coach, GenZ and Simple voices, and their shared Slang | They describe chat replies and personas, not documents. |

## Formats

The configuration of the package maps a View to each format that Vale does not
read as prose by itself.

- Hash reads the comments of shell, ebuilds, OpenRC scripts, nftables rulesets,
  Makefiles and configuration files. It reads the line comments of PowerShell. A
  `#` and a space, or a `;` at the start of a line, open a comment, also after
  code. Hash skips a modeline and the directive of a common linter. It also
  reads a git hook in `.githooks` by its name, and `.ansible-lint`.
- WholeLine reads a comment that fills its line. In a systemd unit, an INI
  file and a Dockerfile, a `#` after a value is part of the value. A drop-in
  of a unit ends in `.conf`, so Hash reads it.
- Jinja reads the comments and the docstrings of a template, a docstring with
  a section such as `Args:` included. Like Hash, it reads a `#` after a value
  as a comment. A template of a unit is no exception.
- Python, TypeScript, JavaScript, C, C++, Go, Rust and TOML read the comments
  of their language. Python also reads a docstring that has no section such as
  `Args:`. A NumPy section such as `Parameters` over a line of hyphens counts
  too. So does a field list such as `:param x:`. Markdown joins the entries of
  a section into one paragraph. C, C++ and Rust read a doc comment such as
  `///`. C also reads a kafel policy.
- Tsx reads the comments, the text and the readable attributes of a component.
- Commit reads a commit message. A hook passes it with
  `--path=COMMIT_EDITMSG`.
- Comments captures nothing. A project maps it to YAML, as the next section
  describes.

A license header in a comment reads as prose too, so its long sentences are
errors. A line such as `SPDX-License-Identifier: Apache-2.0` names a license
in a few words.

Each View reads a comment as Markdown, and Vale skips a quotation in it. A
comment marker with no space after it, or with four spaces or more, marks a
line of code. The View skips that line.

In C, C++, Go, JavaScript, TypeScript and Rust, a comment that ends on a `;` or
a brace is code too. The View checks the first and the last line of a block
comment, and drops the whole comment when either looks like code. A line in the
middle reads as prose, also after ` *` and a tab, as in the style of the kernel.
Put such code after ` *` and four spaces.

In a run of line comments, the View drops each line of code. Then a Markdown
fence around that line breaks, and the rest of the fence reads as prose.

In C and C++, a `//` comment after `#define` is part of the macro, and the View
does not read it.

Comments at the end of consecutive lines of code join into one paragraph.
Without a full stop, a column of such labels reads as one long sentence.

A patch, a license, CSV and TSV data and a compressed archive take no style.
Vale reads a file whole as prose when its extension is missing or unknown, as
for a script in `bin/`. A project maps it, and turns off the rules that the
package turns off for code:

```ini
[**/bin/*]
View = Hash
PlainTechnical.ColonReveal = NO
PlainTechnical.Emphasis = NO
PlainTechnical.InstructionLength = NO
PlainTechnical.Metacommentary = NO
PlainTechnical.ParagraphLengthVariance = NO
PlainTechnical.ReadingEase = NO
PlainTechnical.RhetoricalQuestion = NO
PlainTechnical.SentenceLengthVariance = NO
PlainTechnical.SentenceStartRepetition = NO
```

Hash also reads a line that opens on `#` inside a heredoc or a string of many
lines as a comment. It misses a comment after a `$'…'` string, and it reads a
`#` in a string inside `$( )` as a comment.

A `.txt` file reads as Markdown, and Hash reads `CMakeLists.txt`,
`requirements*.txt` and `robots.txt`. Vale does not apply `TokenIgnores` to a
file with no extension or an unknown one. The rules read a quotation there, and
UnclosedQuote flags only a mark that lacks a partner in its paragraph.

## YAML

When a configuration maps a View to any format, Vale reads a YAML file only
through a View of its own. Vale then reads each comment of the file itself, as
raw text. So it does not skip a quotation or a line of code in a YAML comment.

When two sections give one file a View, Vale picks one of them in no fixed
order. A section of the package and a section of a project race the same way.
So the package maps the YAML of an Ansible role alone, by its path.
AnsibleTasks reads the names and the messages in the tasks and the handlers of
a role, and ArgumentSpecs reads the descriptions of its options. Both read the
text as Markdown.

A project maps its other YAML. The View Comments captures nothing, and Vale
then reads each comment once:

```ini
[**/*.{yaml,yml}]
View = Comments
```

A project with roles names its other YAML paths instead, so that no file
matches two sections:

```ini
[{**/{group_vars,host_vars,playbooks}/**/*.{yaml,yml},**/roles/*/{defaults,vars}/**/*.{yaml,yml}}]
View = Comments
```

## XML

Vale reads XML only through an XSLT transform, and it reads the path of the
transform from the directory of the configuration that names it. So the package
skips XML and ships the transform `config/templates/text.xsl`. Vale runs the
transform with `xsltproc`, which has to be on the PATH. A configuration
that reads XML names it by its absolute path under StylesPath:

```ini
[*.xml]
BasedOnStyles = PlainTechnical
Transform = /home/user/.vale/styles/config/templates/text.xsl
```

Vale does not apply `TokenIgnores` to XML. So the rules read a quotation
there, and UnclosedQuote flags only a mark that lacks a partner in its
paragraph.

## Notes on Vale

The tests run on Vale 3.26.0. Older releases are untested.

- Vale loads a rule or a View from a `.yml` file only. It skips a `.yaml`
  file and does not warn.
- `vale test` finds a View or a script through the StylesPath of the loaded
  configuration. The commit rules and the rhythm rules do not run without one.
- A case of `vale test` that names a View also reads its input as plain text.
  So the cases of a View that skips text map an extension to it in
  `tests/views/.vale.ini`.
- `vale test` names the file of a case after its format. So a section that
  matches a file name, such as `LICENSE` or `COMMIT_EDITMSG`, has no case.
- Vale loads each `.vale-config/*.ini` file under the StylesPath as the
  configuration of a package. In `tests/pkg`, links give that layout to
  `.vale.ini` and `styles`. So the cases in `tests/views`, `tests/errors` and
  `tests/commits` run under the configuration of the package.
- The configuration skips a quotation in double quotes, so the rules do not
  read a phrase inside them. A quote after a digit is an inch mark, and
  does not open a quotation.
- Inside a raw block of HTML, a code span and a quotation read as text, since
  Markdown stops there. UnclosedQuote flags a quote mark only when its
  paragraph lacks a partner for it. A tag such as `<script>` in prose
  opens such a block, and Vale skips the rest of the file. Write a tag in a
  code span.
- Vale's Markdown reads the text from one `$` to the next as math, which hides
  it. So the configuration skips a shell variable such as `$dst`, and the
  quotation in `copy $src to "$dst"` pairs.
- A quote mark or a backtick that never closes can stop the run. When some
  10,000 lines with no blank line follow it, it overflows the stack of
  `TokenIgnores`. Vale splits a value of the configuration at a comma, so that
  pattern cannot bound it. The rules bound their own scans.
- Vale applies the `TokenIgnores` of every matching section, in no fixed
  order. So `.vale.ini` holds every pattern in the one alternation of `[*]`,
  where two patterns cannot race. A project's own `TokenIgnores` adds to it,
  and can race it where the two overlap.
- Each `TokenIgnores` pattern sees the original text, so a quote inside a code
  span opens a second skip. The configuration joins the patterns into one
  alternation, and the code span matches first.
- A TextFSM pattern in a View ends on a bare `$`. Vale does not read the
  TextFSM escape `$$` as an anchor.
- The dasel selectors use version 3. The form `search(...).map(field)` works,
  and the version 2 form `search(...).field` fails when Vale loads the View.
- A View that reads the comments of a YAML file, such as Hash, reads each
  comment a second time.
- A dasel View that searches stops the run at a YAML key that is not a
  string, such as `1: a`. AnsibleTasks and ArgumentSpecs search, and Comments
  uses TextFSM, which reads lines.
- A dasel View reports an alert in a value of many lines at the line of its
  key.
- A TextFSM View joins the lines that one pattern of the template matched.
  Two patterns give two separate texts.
- Vale stops a script after 2 seconds. In a script, `text.re_replace` costs
  time for each match, and a large file has many. So a script replaces text
  with a split and a join, and collapses space with `text.fields`.
- In JavaScript and TypeScript, Vale skips a comment that opens on a pragma,
  such as `eslint`, `@ts-`, `prettier-ignore` or `istanbul`.
- In a file of code, Vale can drop an alert in a later comment. It drops the
  alert when its column in the comment equals that of an earlier alert of the
  same rule.
- A dasel View reads the keys of a mapping in no fixed order. TermRotation
  then can name a different term in each run.
- In XML, Vale places an alert at the first match of its text, which can be in
  a comment.
- In an HTML page, Vale skips an `alt` that follows another attribute, and
  it skips the description in `<meta>`. It reads attribute values such as
  `class` and `href` as prose, and it does not read a comment.
