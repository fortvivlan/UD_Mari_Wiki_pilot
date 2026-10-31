# Mari: Universal Dependencies Annotation Guide

**For human annotators of Meadow/Eastern Mari (`mhr`).**
Guide version: 2026-10-31; UD v2.

Additions based on official UD documentation include
source links. This is a local project guide, rather than a Mari specification approved
by the UD maintainers.

Status labels:

- **Working rule** — a decision from the author's file to apply consistently.
- **UD** — a rule or constraint from official UD documentation.
- **For adjudication** — a contradiction, provisional hypothesis, or expert question;
  record the case in the disagreement log with its `sent_id`, word ID, and context.
- **Source example** — an illustration of the author's analysis. 

## Contents

1. [Annotator workflow](#workflow)
2. [Tokenization and CoNLL-U format](#format)
3. [Quick guide to syntactic decisions](#decisions)
4. [Parts of speech and morphology: the complete source material](#morphology)
5. [Syntax: definitions, examples, and special cases](#syntax)
6. [Questions for adjudication and editorial clarifications](#issues)
7. [Related-language comparisons and UD requirements](#ud-comparison)
8. [Annotation quality checks](#quality)
9. [Official references](#references)

<a id="workflow"></a>
## 1. Annotator workflow

1. Read the entire sentence and, when needed, its surrounding context. Preserve the
   original spelling and provenance. A translation is an aid to analysis, rather
   than a reason to alter the Mari text.
2. Check sentence, word, and punctuation boundaries. Do not split suffixes into
   separate nodes. Flag disputed clitics for adjudication under section 2.
3. Assign the lemma and UPOS, then morphological features. Distinguish word form
   from phrase function: a temporal noun does not become an adverb merely because
   it functions as an adverbial.
4. Identify the predicative head. In a pure copular construction, the nominal,
   adjectival, or adverbial predicate is the head; in an auxiliary construction,
   the lexical verb is the head.
5. Annotate arguments and adverbials, then dependents within nominal phrases,
   function words, coordination, and punctuation. Attach dependents to their
   lexical heads, rather than mechanically to the nearest word.
6. Check ambiguous forms against the tables below. Do not generalize a decision
   that depends on context to every occurrence of the form.
7. Log disputed cases and submit them for adjudication. Once a decision is made,
   update the rule and comparable occurrences, preserving the change history.
8. Perform the checks in section 8. Parser predictions and morphological analyses
   require manual review and do not themselves establish the correct analysis.

<a id="format"></a>
## 2. Tokenization and CoNLL-U format

### 2.1. Word boundaries and original spelling

**Working baseline:** words are normally separated by spaces; external punctuation
forms separate tokens. An internal hyphen in `Йошкар-Ола`, `кож-влак`, `Букетов-Сайн`,
`6-шо`, or `физика-математика` does not by itself create a word boundary. The range
`1941–1945` is represented in the author's example as `1941`, `–`, `1945`; preserve
the absence of spaces with `SpaceAfter=No`. Document decisions for new types of
hyphenated spelling.

Case, number, and possessive morphemes do not become separate nodes. However, UD
distinguishes orthographic tokens from syntactic words: an independent clitic or
contraction may require a **multiword token (MWT)** containing several words.
[UD tokenization](https://universaldependencies.org/u/overview/tokenization.html).
Current pilot version of Mari dataset doesn't have MWT segmentation. 

Separately written words in `тӱҥ шотышто`, `туге гынат`, and geographical names
remain separate nodes; `fixed`, `flat:name`, and `compound` describe their relations.
Spaces within a token are permitted only for explicitly documented exceptions.
Komi, Erzya, and Moksha allow an exception for numbers such as `1 000 000`; for Mari,
this is a **candidate for adjudication**, rather than an automatically inherited rule.
[UD tokenization](https://universaldependencies.org/u/overview/tokenization.html).

Preserve Unicode and distinctions between alphabets. For example, the source form
`Шöртньö` contains Latin `ö`: retain FORM and record `CorrectForm=Шӧртньӧ` in MISC;
when annotating a typo, add `Typo=Yes` in FEATS. Do not automatically replace
Northwestern Mari forms or another language with Meadow Mari. Document metadata
must preserve text provenance, licensing, and the source edition when known.

### 2.2. The ten columns

| Column | What to record |
|---|---|
| ID | Consecutive word numbers starting at 1 in each sentence |
| FORM | Original word form or punctuation mark |
| LEMMA | Dictionary form; the infinitive for an ordinary verb, or the form specified in the AUX table for negative forms |
| UPOS | Universal part of speech |
| XPOS | `_` unless a separate local tagset is used |
| FEATS | `Feature=Value` entries joined by `\|`; `_` if there are no features |
| HEAD | Head ID; `0` only for the root |
| DEPREL | Relation to the head; `root` only when HEAD=0 |
| DEPS | `_` in basic annotation; enhanced dependencies require a separately agreed layer |
| MISC | For example, `SpaceAfter=No`, `CorrectForm=…`; `_` if empty |

Separate columns with **tabs**, rather than alignment spaces. Use UTF-8, Unicode
NFC, and LF line endings; separate sentences with a blank line and include a final
blank line. Ordinary word rows require UPOS, HEAD, and DEPREL.
Give each sentence a unique `# sent_id = …` and `# text = …`; keep translations
separate, for example in `# text_en = …`. An earlier text version may be recorded
as `# text_orig = …`, but edits must not be silently applied to source data.
[UD CoNLL-U](https://universaldependencies.org/format.html).

Features are case-sensitive: use `Number[psor]`, rather than `number[psor]`. Sort
feature names and lists of values alphabetically, ignoring case. `_` means that
no value is recorded; do not substitute a guess about case, person, or number.
Do not transfer features between members of an analytic construction without
morphological justification.
[UD CoNLL-U](https://universaldependencies.org/format.html).

Some examples below are **fragments** retaining their original IDs and external
heads. They are not independent sentences and cannot be passed directly to the
validator. Complete examples also require `sent_id` and review of disputed decisions
before inclusion in the data. The author's heading “DEPS” is rendered here as
“Syntax”: it describes **HEAD/DEPREL**, rather than the ninth column for enhanced
dependencies.

<a id="decisions"></a>
## 3. Quick guide to syntactic decisions

| Question | Decision |
|---|---|
| Does the word denote an entity/participant or replace a nominal phrase? | Consider `NOUN`, `PROPN`, or `PRON`; its role determines the relation |
| Does a pronominal expression specify a noun or quantify a phrase? | Consider `DET` + `det`; see disputed `южо`, `икмыняр(е)` |
| An ordinary cardinal number? | `NUM NumType=Card`, usually `nummod` |
| An ordinal property? | `ADJ NumType=Ord`, usually `amod`; years require context |
| Is the temporal form a productively inflected nominal? | `NOUN` + justified `Case`, with `obl:tmod` |
| A lexicalized adverb? | `ADV`, usually `advmod`; do not infer `Case=Gen` from `-н` alone |
| Does a postposition belong to a nominal phrase? | `ADP AdpType=Post`, with `case` to the phrase head |
| Does a function word introduce a subordinate clause? | `mark` to the predicate; determine UPOS separately |
| Is the verb grammaticalized in this construction? | `AUX` + `aux` / `aux:aspect` to the lexical predicate |
| Is it a pure copula? | `AUX` + `cop` to the nonverbal predicate |
| Does the verb mean ‘become/turn into’? | `VERB` with a predicative complement; it is not a pure `cop` |
| Do two lexical verbs describe one event? | Check `compound:svc`; do not apply it to every converb–verb combination |
| Is the subordinate clause a core complement with obligatory subject control? | `xcomp`; otherwise consider `ccomp`, `advcl`, or `acl` |
| Does a marker restrict a specific phrase, as `гына` does? | `advmod:emph` to the phrase head |
| Does a marker organize discourse or highlight a topic? | Consider `discourse`, as for `гын` in the author's example |

References: [DET](https://universaldependencies.org/u/pos/DET.html),
[NUM](https://universaldependencies.org/u/pos/NUM.html),
[cop](https://universaldependencies.org/u/dep/cop.html),
[xcomp](https://universaldependencies.org/u/dep/xcomp.html),
[ccomp](https://universaldependencies.org/u/dep/ccomp.html),
[advmod](https://universaldependencies.org/u/dep/advmod.html).

<a id="morphology"></a>
## 4. Parts of speech and morphology

### Reading the features

| Feature | Meanings of the values used here |
|---|---|
| Case | Nom — nominative; Acc — accusative; Gen — genitive; Dat — dative; Ine — inessive; Ill — illative; Lat — lative; Cmp — comparative case; Com — comitative |
| Number, Number[psor] | Sing — singular, Plur — plural; the [psor] layer refers to the possessor, rather than the possessed entity |
| Person, Person[psor] | 1, 2, 3 — first, second, third person; the [psor] layer indicates the possessor's person |
| Mood | Ind — indicative, Imp — imperative, Des — desiderative |
| Tense | Pres — present, Past — past; the temporal meaning of the construction may differ from the morphological tense of the word |
| VerbForm | Fin — finite, Inf — infinitive, Part — participle, Conv — converb, Vnoun — verbal noun |
| Voice | Act — active, Pass — passive |
| Evident | Nfh — non-firsthand evidentiality; used for Past II in this project |
| Connegative | Yes — a special form of the lexical or auxiliary verb used with a negative auxiliary |
| PronType | Prs — personal, Dem — demonstrative, Int — interrogative, Rcp — reciprocal, Neg — negative, Ind — indefinite, Tot — total |
| Polite, Reflex | Polite=Form — formal address; Reflex=Yes — reflexive |
| Degree | Cmp — comparative, Sup — superlative; distinguish these from Case=Cmp |
| NumType | Card — cardinal, Ord — ordinal, Sets — number of sets, Assoc — proposed associative/group meaning |
| NumForm | Digit — written in digits; this does not indicate ordinal meaning |
| Polarity | Neg — negative form; PronType=Neg separately describes the type of pronominal word |
| AdpType | Post — postposition |

These are explanations of the notation, rather than permission to assign every
feature to every UPOS. The relevant subsection specifies its distribution; check
suffixes and syntactic roles against the particular form. Case definitions are
available under [UD Case](https://universaldependencies.org/u/feat/Case.html).

Sections 4.1–4.11 contain the complete existing material for Mari, formatted
as tables and examples. Provisional decisions are preserved; additions and
clarifications are explicitly marked. Partial FEATS lists in illustrations show
only the features relevant to the phenomenon under discussion.

### 4.1. NOUN and PROPN

**Clarification:** `Number` describes the number of entities, while `Number[psor]` describes the number of possessors. Do not automatically assign `Case=Gen` to temporal `-н`; `шочмын` and `кечын` require analysis. See Q1.

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Abbr | Yes | МАССР	МАССР	PROPN	_	Abbr=Yes |
| Case | Nom, Acc, Gen, Dat, Ine, Ill, Lat, Cmp, Com |  |
| Number | Sing, Plur |  |
| Number[psor] | Sing, Plur |  |
| Person[psor] | 1, 2, 3 |  |
| VerbForm | Vnoun | кодлымаш · кодлымаш · NOUN · _ · Case=Nom\|Number=Sing\|VerbForm=Vnoun |

**Notes:**

A noun normally has only Case and Number; [psor] features are added only when a possessive suffix is present. VerbForm is used only for verbal nouns in -маш. Case may be absent in certain temporal expressions.

**Special cases and source examples:**

```text
толшо-влаклан	толшо	NOUN	_	Case=Dat|Number=Plur
```

(Толшо is technically a participle, but we treat it here as an established nominalization.)

Cardinal direction terms: касвел, йудвел — NOUN.

марий, олык, шӧртньӧ — NOUN: unless dictionaries explicitly identify a word as an adjective, annotate it as a noun.

```text
кечынже	кече	NOUN	_	Number=Sing|Number[psor]=Sing|Person[psor]=3	18	obl:tmod
```

```text 
кечын	кече	NOUN	_	Number=Sing	18	obl:tmod
```

Annotate date-related expressions as NOUN, but omit Case: the grammar treats these more like adverbs, although they bear [psor] features in our annotation.

- Expressions of time (during the day, in the morning, in the evening):

The evidence for the instructive suffix in weekday adverbials suggests we should distinguish lexical weekday adverbs from productively inflected temporal nouns, rather than automatically treating every temporal -н as genitive. For Mari, I would therefore currently annotate июньышто, шошым, and ийыште as NOUN, while шочмын is the genuinely debatable case because there is evidence that the weekday forms have lexicalized into adverbs.

эрдене, кечывалым, кастене, йудым (in the morning, during the day, in the evening, at night): NOUN + Acc | NOUN + postposition дене.

- The Greek War of Independence:

```text
4	Грек	грек	NOUN	_	Case=Nom|Number=Sing	5	nmod	_	_
5	Шкевуя	шкевуя	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
6	верч	верч	ADP	_	AdpType=Post	5	case	_	_
7	Сарыш	сар	NOUN	_	Case=Ill|Number=Sing	8	obl	_	_
```

Dictionaries do not classify шкевуя as a noun, but it behaves exactly like a noun here.

### 4.2. ADJ

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Variant | Short, Long | йошкар |
| Degree | Cmp |  |
| NumType | Ord |  |
| Polarity | Neg | уке: ADJ · Polarity=Neg |

**Notes:**

An adjective normally has no FEATS. Annotate Variant only for adjectives participating in this paradigm (йошкар). Comparative adjectives may have Degree=Cmp; Polarity applies only to уке in this inventory.

```text
Derivatives in -се are adjectives: кундемысе	кундемысе	ADJ
```

**Special cases and source examples:**

See Q3:

```text
уке: ADJ	Polarity=Neg
```

Йолташ-влак уке улыт.

'There are no friends.'

```text
Йолташ-влак   NOUN   Number=Plur   nsubj → уке
уке           ADJ    Polarity=Neg   root
улыт          AUX    Mood=Ind|Number=Plur|Person=3|Tense=Pres|VerbForm=Fin   cop → уке
```

```text
Year number in cases like `1953-шо ийыште' — ADJ: 1953	1953	ADJ	_	NumForm=Digit|NumType=Ord
```

- ял/ола шотан илем:

```text
12	Вечын	Вечын	PROPN	_	Case=Nom|Number=Sing	15	compound	_	_
13	ял	ял	NOUN	_	Case=Nom|Number=Sing	14	obl	_	_
14	шотан	шотан	ADJ	_	_	15	amod	_	_
15	илемыш	илем	NOUN	_	Case=Ill|Number=Sing	16	obl	_	_
```

### 4.3. ADV

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Degree | Cmp, Sup | эн · ADV · Degree=Sup |
| PronType | ... | |

**Notes:**

Adverbs normally have empty FEATS. Exceptions are the superlative marker эн and rare cases with ExtPos.

**Special cases and source examples:**

- тораште — may be ADP or ADV.

```text
тораште илаш — live far away	ADV, advmod
ола деч тораште — far from the city	Provisionally ADV with nominal obl complement
ола деч тораште огыл — not far from the city	ADV, obl complement, negation PART
```

Many words derived from demonstrative pronouns are regarded by speakers as separate lexical items (lexicalization), so we annotate them as ADJ/ADV.

-нек numerals:

This suffix is coupled with (especially lower) numerals, and some other quantifiers and nouns, to create adverbs with the meaning ‘all …, the whole …’. Note that the formation is not always predictable – in some cases the suffix is attached to the long form of a numeral, in some cases to the short form with some alterations. Examples: шымынек 'all seven', тӱрыснек 'wholly, completely'

- The adverbials of the days of the week are formed with the adverbial (instructive) suffix in -н

In the adverbials the names of the months are used in the inessive

For the four seasons of the year the adverbials are formed with the accusative: телым, шошым, кенгежым, шыжым

### 4.4. NUM

**Clarification:** `NumType=Sets` is universal; `NumType=Assoc` is an extension attested in Erzya. 

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Variant | Short, Long | ик, кок, кум |
| Case | ... |  |
| NumType | Card, Ord, Sets |  |
| NumForm | Digit |  |

**Notes:**

A numeral normally has NumType and may have NumForm. Variant applies only to Card; in that paradigm, Case is possible only with Variant=Long.

So-called collective numerals can be formed by means of the adverbial (instructive) suffix in -н, an Ы-suffix from the long forms of the cardinal numbers starting with two, as well as some indefinite quantifiers and pronouns. Note the form коктын from кокыт. These numerals are annotated as NumType=Card.

Sets

For numbers from two to five, there are special forms used in reference to groups consisting of this number of people. The base forms (когынь- ‘2’, кумынь- ‘3’, нылынь- ‘4’ and визынь- ‘5’) do not occur on their own, but are obligatorily coupled with a plural possessive suffix, and are only used with postpositions, or with the following three case suffixes: genitive, dative, and accusative. While the genitive and accusative forms are predictable, the dative forms of two and three are irregular. The forms of two are used quite frequently; the other forms are used increasingly rarely.
These numerals express the commonality of two, three, four, or five people regarded as forming a group, i.e., ‘the two/three/four/five of us/you/them’.

(Not attested in the current corpus.)

### 4.5. PRON and DET

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Case | Nom, Acc, Gen, Dat |  |
| Number | Sing, Plur |  |
| Polite | Form | те |
| PronType | Prs, Dem, Int, Rcp, Neg, Ind, Tot |  |

**Notes:**

Only a restricted set of words is normally annotated PRON; many words traditionally treated as pronouns receive another part of speech. PronType=Int examples (in relevant contexts):

| Mari                  | Meaning                                        | UPOS                              | Main FEATS                  |
| --------------------- | ---------------------------------------------- | --------------------------------- | --------------------------- |
| **кушто**             | where                                          | `ADV`                             | `PronType=Int`              |
| **кушко**             | to where                                       | `ADV`                             | `PronType=Int`              |
| **кушан**             | where (lative)                                 | `ADV`                             | `PronType=Int`              |
| **кушечын**           | from where                                     | `ADV`                             | `PronType=Int`              |
| **кушкен**            | for how long / how far                         | `ADV`                             | `PronType=Int`              |
| **кунам**             | when                                           | `ADV`                             | `PronType=Int`              |
| **кунамлан**          | until/by when                                  | `ADV`                             | `PronType=Int`              |
| **кунамсек**          | since when                                     | `ADV`                             | `PronType=Int`              |
| **кунамсе**           | of what time                                   | **`ADJ`**                         | `PronType=Int`              |
| **кусо**              | of/from what place                             | **`ADJ`**                         | `PronType=Int`              |
| **кузе**              | how                                            | `ADV`                             | `PronType=Int`              |
| **могай**             | what kind of                                   | **`ADJ`**                         | `PronType=Int`              |
| **кунаре / мыняре**   | how many/much                                  | `DET` or `PRON`, depending on use | `PronType=Int`              |
| **кунарын / мынярын** | how many of them/us etc.                       | `PRON`                            | `Case=Gen\|PronType=Int`    |
| **мынярымше**         | which number / what-number-th                  | **`ADJ`**                         | `NumType=Ord\|PronType=Int` |
| **мынярылан**         | at what time / for what price / by what amount | `PRON`                            | `Case=Dat\|PronType=Int`    |

The Mari grammar explicitly notes that many of these same forms are used in clausal subordination and can have other PronType.

| Form | Meaning |
| --- | --- |
| кӧ | who |
| мо | what |
| могай | what, what kind of |
| мыняр(е) | how many, how much |
| кунар(е) | how many, how much |
| мочол(о) | (oh) how many, (oh) how much |
| кудо | which, which one |
| кушто | where (inessive) |
| куш(ко) | (to) where (illative) |
| кушан | where (lative) |
| кушеч(ын) | from where |
| кушкен | for how long; how far |
| кунам | when |
| кунамлан | till when |
| кунамсек | since when |
| кунамсе | of what time |
| кусо | from where, of what place |
| кузе | how |
| молан | why |
| кунарын | in a group of how many |
| мынярын | in a group of how many |
| мынярымше | which one, which number |

A number of these pronouns are also used as particles with secondary meanings: мо, могай, кунам, кузе - see PART

PronType=Neg:

Negative pronouns and adverbs are formed from interrogative pronouns with the prefix ни- (exception: иктат ‘nobody, not one’)

Notably neither current Erzya-JR nor Moksha-JR has PronType=Neg in its feature inventory. Mari isn't like them in this case, the use of PronType=Neg is allowed.

Rcp = икте-весе

Ind = кеч-, керек-, ала-, иктаж-, -гынат

ик(те), вес(е) may also be indefinite PRON.

| Form | UPOS | PronType | Comments |
| --- | --- | --- | --- |
| икмыняр(е) | DET | Ind | Indefinite quantity, ‘some/several’; PRON when independent |
| иктаж(е) | PRON / DET | Ind | ‘Someone/something/some’ |
| иктажышт | PRON | Ind | ‘One of them’ |
| кажне | DET | Tot | ‘Each/every’ |
| чумыр | DET / ADJ | Tot or _ | Depends on whether it is a total quantifier or a lexical adjective; ask Yulia whether Mari speakers distinguish the quantifier use from the lexical adjective use syntactically, or whether all these examples are better understood as one adjective (чумыр класс — ‘the whole class’, чумыр йылме — ‘common language’, чумыр положений — ‘general situation’, пашам чумыр ышташ — ‘to work as a team’). See Q2. |
| чыла | PRON / DET | Tot | ‘All/everyone’ |
| чылажат | ADV | _ | can be revised after real annotation |
| чылаже | ADV | _ | can be revised after real annotation |
| чылажлан | PRON | Tot | ‘For everything’, with Case=Dat |
| чылан | PRON | Tot | Collective ‘all/everyone’ |
| чыланже | ADV | _ | can be revised after real annotation |
| чылашт | PRON | Tot | ‘All of them’ |
| южгунам | ADV | Ind | ‘Sometimes/occasionally’ |
| южгунамже | ADV | Ind | ‘Only sometimes’ |
| южикте | PRON | Ind | ‘Some (of them)’ |
| южиктыже | PRON | Ind | ‘One of them’ |
| южиктыжышт | PRON | Ind | ‘Some of them’ |
| южиктышт | PRON | Ind | ‘Some of them’ |
| южо | DET / PRON | Ind | ‘Some’; typically attributive |
| южыжо | PRON | Ind | ‘Some (of them)’ |
| южышт | PRON | Ind | ‘Some (of them)’ |

Reflex=Yes: шке

The reflexive pronoun can be marked for person and case, though it is often unmarked, as discussed below. The paradigm is in [Riese et al. 2022].

There are dative forms шкалан and accusative forms шкем which are not marked for person. These generally occur in impersonal sentences, but occasionally also in personal sentences.

The pronoun has a wide range of functions:

a) Non-case marked forms are used adverbially in an emphatic meaning ‘oneself’ (‘myself’, ‘yourself’, etc.). In this function, it is generally simply *шке*, though in the third person (singular or plural), person-marked forms can optionally be used. The form шкеже can also be used as an emphatic form in other persons – for the emphatic usage of the possessive suffix third person singular.

b) The base form *шке* is used as an adjective ‘own’ (‘my own’, ‘your own’ etc.), in all persons. It is often combined with a noun marked with a possessive suffix. It is often used in contexts where ‘own’ does not seem natural in the English translation.

c) It is used as a reflexive pronoun ‘oneself’ (‘myself’, ‘yourself’, etc.), occurring in all persons. In this function, it can be marked for both case and person. 

### 4.6. ADP

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Number[psor] | Sing, Plur |  |
| Person[psor] | 1, 2, 3 |  |
| AdpType | Post |  |
| Degree | Cmp | ӱстел деке ‘to the table’ → ӱстел декырак ‘closer to the table’ |

**Notes:**

Annotate AdpType=Post consistently on all postpositions: Erzya and Moksha use AdpType=Post, as do Finnish, Estonian, Komi-Zyrian, Komi-Permyak, Veps, Skolt Sami, etc. The current UD survey lists all these languages under AdpType=Post. We therefore use AdpType=Post too.

[psor] features have not yet been attested on postpositions in the dataset.

**Special cases and source examples:**

- могырно: a postposition from the могыр group.

- тӱҥ шотышто — the idiom “mainly / for the most part”.

```text
4	тӱҥ	тӱҥ	ADV	_	_	11	advmod	_	_
5	шотышто	шотышто	ADP	_	AdpType=Post	4	fixed	_	_
```

- денат — a postposition with an attached clitic (analysed as a single token)

- тораште — may be ADP or ADV depending on the context.

```text
тораште илаш — live far away:	ADV, advmod
ола деч тораште — far from the city:	Provisionally ADV with nominal obl complement
ола деч тораште огыл — not far from the city:	ADV, obl complement, negation PART
```

### 4.7. CCONJ

| Feature | Values | Example / source entry |
| --- | --- | --- |
| - | - |  |

**Notes:**

No FEATS.

**Special cases and source examples:**

- туге гынат — however, despite.

```text
14 туге    ADV   ExtPos=CCONJ  → 17  cc
15 гынат   PART  _             → 14  fixed
```

### 4.8. VERB

Representing Past II with `Evident=Nfh` is a working convention. The question about `PartForm=FutNec` remains open (Q5).

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Connegative | Yes |  |
| ConvType | Gen, Ant, Post, Sim | кочмек(е) ‘after eating’ ConvType=Ant\|VerbForm=Conv |
| Evident | Nfh |  |
| InfForm | Nec | кочман ‘(one) must eat’ InfForm=Nec\|VerbForm=Inf |
| Mood | Ind, Des, Imp |  |
| Number | Sing, Plur |  |
| PartForm | FutNec | кочшаш ‘to eat; to be eaten’PartForm=FutNec\|VerbForm=Part |
| Person | 1, 2, 3 |  |
| Tense | Pres, Past |  |
| VerbForm | Fin, Inf, Part, Conv |  |
| Voice | Act, Pass | кочшо ‘eating’ VerbForm=Part\|Voice=Act |

**Notes:**

**Finite verb tenses**

Simple Present: Pres VERB

```text
кодлалтеш	кодлаш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin|Voice=Pass
```

Simple Future: AUX + Pres VERB

No examples in the corpus.

Simple Past I: Past VERB

Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin

Simple Past II: Past VERB

Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin

Compound Past I: Pres VERB + AUX (*ыле* улаш)

Not attested in the corpus.

Compound Past II(second periphrastic imperfect): Pres VERB + AUX (*улмаш* улаш)

Not attested in the corpus.

Compound Past III(first periphrastic perfect): Past II VERB + AUX (*ыле* улаш)

```text
7	почылтын	почылташ	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	_
8	ыле	улаш	AUX	_	Tense=Past	7	aux	_	_
```

Compound Past IV(second periphrastic perfect): Past II VERB + AUX (*улмаш* улаш)

Not attested in the corpus.

**Connegative**

A special form of a verb with the negative verb

```text
9	лийын	лияш	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
10	ок	огеш	AUX	_	Mood=Ind|Number=Sing|Person=3|Polarity=Neg|Tense=Pres|VerbForm=Fin	9	aux	_	_
11	керт	керташ	AUX	_	Connegative=Yes|VerbForm=Fin	9	aux	_	SpaceAfter=No
```

**Non-finite forms**

| Type | Example | Pilot FEATS | Notes |
|---|---|---|---|
| Infinitive | `кочкаш` ‘to eat’ | `VerbForm=Inf` |  |
| Necessitive infinitive | `кочман` ‘(one) must eat’ | `InfForm=Nec\|VerbForm=Inf` | language-specific `InfForm=Nec` |
| Active participle | `кочшо` ‘eating’ | `VerbForm=Part\|Voice=Act` |  |
| Passive participle | `кочмо` ‘eaten’ | `VerbForm=Part\|Voice=Pass` | provisional where interpretation is not transparently passive |
| Future-necessitive participle | `кочшаш` ‘to eat; to be eaten’ | `PartForm=FutNec\|VerbForm=Part` | see Q5; alternative `Aspect=Prosp\|VerbForm=Part` |
| Negative participle | `кочдымо` ‘not eating / not eaten’ | `Polarity=Neg\|VerbForm=Part` |  |
| General affirmative/instructive converb | `кочкын` ‘eating’ | `ConvType=Gen\|VerbForm=Conv` | |
| General negative converb | `кочде` ‘not eating’ | `ConvType=Gen\|Polarity=Neg\|VerbForm=Conv` | |
| Anterior (‘after’) converb | `кочмек(е)` ‘after eating’ | `ConvType=Ant\|VerbForm=Conv` | |
| Posterior (‘before’) converb | `кочмеш(ке)` ‘before eating’ | `ConvType=Post\|VerbForm=Conv` | also ‘instead of’ |
| Simultaneous (‘while’) converb | `кочшыла` ‘while eating’ | `ConvType=Sim\|VerbForm=Conv` | |

**Special cases and source examples:**

Infinitives may take possessive suffixes (this also occurs in Finnish):

```text
- малашышт	малаш	VERB	_	Number[psor]=Plur|Person[psor]=3|VerbForm=Inf
```

- 1940-ан ийлаште Келдаксола ял Изи Каракша ял деке ушалтын, кызыт Шкетан уремже **лиеш**:

| Function of лияш | UPOS | Dependency | Example |
| --- | --- | --- | --- |
| Future copula ‘will be’ | AUX | cop | Мый журналист лиям |
| Lexical ‘become, turn into’ | VERB | Head of predication | Шкетан уремже лиеш (provisional) |
| Aspectual construction with another verb | Depends on construction | aux:aspect only if grammaticalized | лийын шоген has шогаш as auxiliary |

```text
11	кызыт	кызыт	ADV	_	_	14	advmod	_	_
12	Шкетан	Шкетан	PROPN	_	Case=Nom|Number=Sing	13	nmod	_	_
13	уремже	урем	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	14	xcomp	_	_
14	лиеш	лияш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	9	parataxis	_	SpaceAfter=No
15	.	.	PUNCT	_	_	9	punct	_	SpaceAfter=No
```

### 4.9. AUX

**Clarification:** the inventory below lists candidate auxiliaries **in particular constructions**; these lemmas can still be VERB elsewhere. Komi-Zyrian modal verbs are not treated as AUX, so language relatedness alone does not justify transfer.  

| Feature | Values | Example / source entry |
| --- | --- | --- |
| Connegative | Yes |  |
| ConvType | Gen, Ant, Post, Sim |  |
| Evident | Nfh |  |
| InfForm | Nec |  |
| Mood | Ind, Des, Imp |  |
| Number | Sing, Plur |  |
| PartForm | FutNec |  |
| Person | 1, 2, 3 |  |
| Polarity | Neg |  |
| Tense | Pres, Past |  |
| VerbForm | Fin, Inf, Part, Conv |  |
| Voice | Act, Pass |  |

**Notes:**

Negation verb (ом, ок...) has no Inf form. Therefore, lemma conventions for the negative verb are:

| Function | FORM | LEMMA | UPOS |
| --- | --- | --- | --- |
| Present negative verb | ом, от, огеш, ок, etc. | огеш | AUX |
| Negative copula | омыл, отыл, огыл, etc. | улаш | AUX |
| Negative auxiliary in Past II | омыл, отыл, огыл, etc. | улаш | AUX |
| Independent negation particle | огыл | огыл | PART |

ыле (улаш) and улмаш (улаш) are traditionally considered particles, but UD often treats such elements as AUX (compare the Russian particle by ‘conditional marker’). Therefore, when functioning as particles according to traditional grammar, these AUX get only Tense=Past feature. 

These particles are the third person singular past-tense forms of ‘to be’, in the simple past tense I and simple past tense II respectively.

When улаш is AUX to a VERB, annotate only tense on it; the remaining FEATS are on the lexical verb. When it is a copula or an auxiliary with ADJ/ADV, etc., and especially when it is a lexical verb, annotate its morphology as for an ordinary verb.

```text
ыле	AUX	Tense=Past
```

However:

```text
ыле    улаш    AUX	Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin
```

when it is a copula without a lexical verb.

```text
улмаш	AUX	Evident=Nfh|Tense=Past
```

The Mari grammar also identifies the following auxiliary verbs:

| Lemma | Conjugation class, meaning, and function from the source |
| --- | --- |
| керташ | (-ам) ‘to be able to’ |
| мошташ | (-ем) ‘to be able to’ |
| лияш | (-ям) ‘to be, to become’ → ‘to be allowed |
| толашаш | (-ем) ‘to try, to strive’ → improper, inadequate execution |
| тӧчаш | (-ем) ‘to try, to attempt’ → improper, inadequate execution |
| кӱлаш | (-ам) ‘to have to’ |
| верешташ | (-ам) ‘to get, to catch’ → ‘to have to’ |
| логалаш | (-ам) ‘to get to, to find oneself (somewhere)’ → ‘to have to’ |
| возаш | (-ам) ‘to lie down’ → ‘to have to’ |
| пернаш | (-ем) ‘to strike against’ → ‘to have to’ |
| тӱкнаш | (-ем) ‘to touch (intr.)’ → ‘to have to’ |

They usually combine with converbs.

**Aspect auxiliaries**

| Lemma | Conjugation class, meaning, and function from the source |
| --- | --- |
| возаш | (-ам) ‘to lie down’ → resultative; added property; lying position |
| илаш | (-ем) ‘to live’ → continuous |
| каяш | (-ем) ‘to go’ → ingressive, tangible result |
| кияш | (-ем) ‘to lie’ → durative; lying position |
| кодаш | (-ам) ‘to stay’ → resultative, evident result, excessive |
| кодаш | (-ем) ‘to leave (something)’ → resultative, subject leaves |
| колташ | (-ем) ‘to send’ → inchoative, momentary |
| кошташ | (-ам) ‘to go’ → durative; many locations |
| кудалташ | (-ем) ‘to throw, to cast’ → resultative; impetuous, sudden |
| кышкаш | (-ем) ‘to throw, to cast’ → resultative; impetuous, sudden; scattering |
| лекташ | (-ам) ‘to go, to leave’ → delimitative; unexpectedness |
| лукташ | (-ам) ‘to lead out’ → exhaustive; creation of something |
| мияш | (-ем) ‘to go, to come’ → gradual |
| налаш | (-ам) ‘to take’ → delimitative; autobenefactive |
| ончаш | (-ем) ‘to look’ → momentary; activity is tried |
| опташ | (-ем) ‘to place (several items)’ → iterative, large scale |
| пуаш | (-эм) ‘to give’ → resultative; benefactive |
| пытараш | (-ем) ‘to finish, to complete’ → exhaustive |
| пыташ | (-ем) ‘to end, to stop (intr.)’ → exhaustive; all participate |
| пышташ | (-ем) ‘to put, to place, to lay’ → resultative; lying position; in advance |
| сеҥаш | (-ем) ‘to win, to defeat’ → exhaustive; ‘to manage to’ |
| темаш | (-ам) ‘to fill up (intr.)’ → exhaustive, sufficient |
| толаш | (-ам) ‘to come’ → gradual |
| шинчаш | (-ам) ‘to sit down’ → resultative; sitting position |
| шинчаш | (-ем) ‘to sit’ → durative; sitting position |
| шогалаш | (-ам) ‘to stand up’ → resultative; standing position |
| шогалташ | (-ем) ‘to place, to stand’ → resultative; standing position |
| шогаш | (-ем) ‘to stand’ → durative, standing position |
| шуаш | (-ам) ‘to arrive, to reach’ → exhaustive; attainment of a goal |
| шуаш | (-эм) ‘to throw, to cast’ → resultative; energetic, impetuous, sudden |
| шукташ | (-ем) ‘to carry out, to fulfill’ → exhaustive; ‘to manage to’ |
| шындаш | (-ем) ‘to put, to place, to set’ → resultative; sitting position |
| эртараш | (-ем) ‘to lead, to take’ → delimitative |

These should be AUX in certain lexicalized contexts, with aux:aspect in syntax and normal verb features.

**Benefactive/Autobenefactive aux**

| Lemma | Conjugation class, meaning, and function from the source |
| --- | --- |
| пуаш | (-эм) ‘to give’ → benefactive; resultative |
| налаш | (-ам) ‘to take’ → autobenefactive; delimitative |
| ончыкташ | (-ем) ‘to begin’ → benefactive (demonstrating) |

**Special cases and source examples:**

- Нине синоним-влак коклаште смысл шот дене нимогай кыл лийын ок керт.

```text
9	лийын	лияш	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
10	ок	огеш	AUX	_	Mood=Ind|Number=Sing|Person=3|Polarity=Neg|Tense=Pres|VerbForm=Fin	9	aux	_	_
11	керт	керташ	AUX	_	Connegative=Yes|VerbForm=Fin	9	aux	_	SpaceAfter=No
```

(Example with negation and a modal AUX.)

- огыл: usually AUX, but PART when it does not accompany a verb:

10	Энгельс	Энгельс	PROPN	_	Case=Nom|Number=Sing	11	compound	_	_
11	ола	ола	NOUN	_	Case=Nom|Number=Sing	13	obl	_	_
12	деч	деч	ADP	_	AdpType=Post	11	case	_	_
13	тораште	тораште	ADV	_	_	16	advmod	_	_
14	огыл	огыл	PART	_	Polarity=Neg	13	advmod	_	SpaceAfter=No

(тораште огыл = not far)

- Negation in Simple Past II:

(The negative forms of the simple past tense II are formed with the gerund in -н followed by the negated forms of улаш (-ам) ‘to be’, written as separate words.)

```text
17	налын	налаш	VERB	_	ConvType=Gen|VerbForm=Conv	12	conj	_	_
18	кертын	керташ	AUX	_	ConvType=Gen|VerbForm=Conv	17	aux	_	_
19	огытыл	улаш	AUX	_	Evident=Nfh|Mood=Ind|Number=Plur|Person=3|Polarity=Neg|Tense=Past|VerbForm=Fin	17	aux	_	SpaceAfter=No
```

### 4.10. PART

**Clarification:** this is the complete traditional inventory from [Riese et al. 2022]. In each context, consider ADV, INTJ, CCONJ, SCONJ, AUX, and other categories. Use `PartType=Mod` for modal uses after adjudication, rather than throughout the inventory. [UD PART](https://universaldependencies.org/u/pos/PART.html).

| Feature | Values | Example / source entry |
| --- | --- | --- |
| PartType | Mod |  |

**Notes:**

Айда, айста, тек, гына

List from [Riese et al. 2022]:

- Demonstrative: теве ‘here, look here’, тевыс ‘here, look here’, уна ‘there, look there’

- limiting, specifying: веле ‘only, just’, гына ‘only, just’, иже ‘not until’, керек ‘at least’, кеч ‘at least’, лач(ак) ‘only, exclusively’, тевак ‘almost, nearly’, теве ‘almost, nearly’, тевыс ‘almost, nearly’, улыжат ‘only, no more than’, чуч ‘almost, nearly’, чылт ‘right, just’, ялт ‘right, just’

- intensifying/emphatic: вара ‘well, then’, веле ‘more and more’, вет ‘so, indeed, well’, гын ‘as for …’, гына ‘rather, fairly’, да ‘really’, дык ‘well, so’, ито ‘even’, кеч ‘if only’, лаваса ‘certainly’, ман ‘how about that’, просто ‘just, simply’, садак ‘after all’, садыгак ‘after all’, то-то ‘there you are’, тудо ‘well, of couse’, тушто ‘there, here’, уж ‘indeed’, уже ‘already’, чай ‘probably’, э/эй ‘o how’, эсогыл ‘even’, эре ‘more and more’, эртак ‘more and more’

- expressing approximative number: ик ‘roughly, about’, иктаж ‘roughly, about’, лиеш ‘soon’

- additive: адак ‘still, yet, more’, да монь ‘and the like’, да мо да ‘and the like’, монь ‘and the like’

- interrogative: a ‘so, huh’, ала ‘maybe, probably, then’, але ‘really’, але вара ‘really, indeed’, вет ‘isn’t it’, гала ‘really’, кузе ‘what, come again’, кунам ‘how, when’, ма ‘are/is you/…’, мо ‘are/is you/…’, мо вара ‘then’, моли ‘perhaps’, неужели ‘really’, ну ‘is that so’, разве ‘then’

- affirmative, expressing trust, affirmation: ане ‘yes; so, well’, да ‘yes’, йӧра ‘fine, okay’, кернак(ак) ‘absolutely’, конешне ‘of course’, лаваса ‘sure’, лач(ак) ‘just’, но ‘yes, uh-huh’, ну ‘really’, -огыла ‘well then’, пеш йӧра ‘very good’, товат(ат) ‘for sure’, товро ‘certainly’, туге ‘yes’, туге вет ‘indeed’, туге шол ‘that’s how it is’, тыгак ‘yes, really’, тыге ‘yes, really’, чынак(ак) ‘indeed’, чынжым(ак) ‘indeed’, шол ‘indeed’

- expressing different degrees of doubt: ала ‘could be’, ала-ала ‘hardly’, ала мо ‘possibly’, аман ‘apparently’, векат ‘probably’, витне ‘apprently’, гай ‘it seems’, докан ‘it seems’, дыр ‘perhaps’, коеш ‘it seems’, кузе ‘how …’, мо ‘really’, можыч ‘maybe’, наверне ‘probably’, очыни ‘probably’, пожале ‘most likely’, ужамат ‘apparently’, ужат ‘apparently’, чай ‘apparently’, шонет ‘it seems’

- denial: уке ‘no’, уке шол ‘no (emphatic)’

- comparison: веле ‘like, as though’, пуйто ‘it’s like’ , чылт ‘just like’, ялт ‘quite like’

- exclamative: кузе ‘how’, дык ‘well, so’, могай ‘what kind of, how’

- indifference: керек ‘makes no difference’, садак ‘makes no difference’, садыгак ‘makes no difference’

- supposing: лиеш ‘if …’, лийже ‘let’s imagine’

- negative: -мочет ‘forgetting about …’, -моэт ‘forgetting about …’, огыл ‘not’

- miscellaneous other functions: икманаш ‘in a word’, -можо ‘it’s not about …’, мутлан ‘for example’, на ‘okay, here you are’, ну ‘so, well’, пожалуйста ‘please’, поро лий(за) ‘please’, тугат ‘even without that’, тугеже ‘that means’

The grammar provides a lengthy particle inventory (above), but not all these items necessarily qualify as particles in UD.

<a id="syntax"></a>
## 5. Syntax: relations and examples

Each subsection starts with an explanation for annotators, followed by the author’s
examples and notes. The label `standard` refers to a universal relation. 

### nsubj

Nominal subject: attaches to the predicative head, including a nonverbal head. Nominative case is a clue, but the construction determines the role.

*standard*

```text
# text = Храм типичный Калинга архитектурный стилеште ден ӱмбалыште 3 шӧртньӧ калаша уло.
# text_en = The temple is in the typical Kalinga architectural style and has three golden kalashas on top.
1	Храм	храм	NOUN	_	Case=Nom|Number=Sing	5	nsubj	_	_
2	типичный	типичный	ADJ	_	_	3	amod	_	_
3	Калинга	Калинга	PROPN	_	Case=Nom|Number=Sing	5	nmod	_	_
4	архитектурный	архитектурный	ADJ	_	_	5	amod	_	_
5	стилеште	стиль	NOUN	_	Case=Ine|Number=Sing	0	root	_	_
6	ден	ден	CCONJ	_	_	11	cc	_	_
7	ӱмбалыште	ӱмбал	NOUN	_	Case=Ine|Number=Sing	11	obl	_	_
8	3	3	NUM	_	NumForm=Digit|NumType=Card	10	nummod	_	_
9	шӧртньӧ	шӧртньӧ	NOUN	_	Case=Nom|Number=Sing	10	nmod	_	_
10	калаша	калаша	NOUN	_	Case=Nom|Number=Sing	11	nsubj	_	_
11	уло	улаш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	5	conj	_	SpaceAfter=No
12	.	.	PUNCT	_	_	5	punct	_	_
```

### obj

Direct nominal object of the lexical predicate. Do not substitute a case name for a syntactic role.

*standard*

```text
# text = Тыге кыдалаш образованийым налеш.
# text_en = Thus, they receive secondary education.
1	Тыге	тыге	ADV	_	_	4	advmod	_	_
2	кыдалаш	кыдалаш	ADJ	_	_	3	amod	_	_
3	образованийым	образований	NOUN	_	Case=Acc|Number=Sing	4	obj	_	_
4	налеш	налаш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	0	root	_	SpaceAfter=No
5	.	.	PUNCT	_	_	4	punct	_	_
```

### iobj

In UD, the indirect object of a verb is any nominal phrase that is a core argument of the verb but is not its subject or (direct) object. For Mari, there remains a question on the existence of dative core objects, therefore such objects in dative case are annotated as obl until further investigation. See Q6.

*standard*

Not attested in the corpus.

### csubj

A clause functioning as subject. Its predicate attaches to the main predicate; in this example, `лиеш → пале`.

*standard*

```text
# text = Романыште мо умбакыже лиеш пале огыл.
# text_en = It is unclear what will happen next in the novel.
1	Романыште	роман	NOUN	_	Case=Ine|Number=Sing	4	obl	_	_
2	мо	мо	PRON	_	Case=Nom|Number=Sing|PronType=Int	4	nsubj	_	_
3	умбакыже	умбакыже	ADV	_	_	4	advmod	_	_
4	лиеш	лияш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	5	csubj	_	_
5	пале	пале	ADJ	_	_	0	root	_	_
6	огыл	улаш	AUX	_	Mood=Ind|Number=Sing|Person=3|Polarity=Neg|Tense=Pres|VerbForm=Fin	5	cop	_	SpaceAfter=No
7	.	.	PUNCT	_	_	5	punct	_	_
```

### ccomp

A clausal complement without obligatory external subject control; it may be finite or nonfinite. 

*standard*

**Notes on the current corpus annotation sample:**

Three syntactic questions are marked for further investigation: the acl vs. nmod analysis of виктарыме шотышто, the ccomp vs. obl/advcl analysis of the postposition-marked interrogative clause кузе шуктен толмо нерген, and the status of нерген.

- `acl vs. nmod`: Шотышто is an established postposition meaning ‘concerning, regarding’. It is explicitly listed in the Mari grammar. Виктарыме is a participle with its own object, and the whole postpositional construction modifies кӱштымашыжым. This makes acl reasonable, although nmod could be considered if we decide to analyze such postposition-governed participles as nominalized expressions.

- `ccomp vs advcl`: Here we prefer ccomp(мутланыме, шуктен), since the clause expresses what was discussed. advcl would treat it as an adjunct rather than the reported topic/content.

- `mark vs case`: Нерген means ‘about, concerning’.

There is a genuine UD distinction to resolve here:

- mark if we analyze кузе шуктен толмо нерген as an interrogative complement clause, with нерген marking its subordination.
- case if we treat the postposition as marking a nominalized participial clause.

We choose mark in this instance because the embedded clause preserves a fully verbal structure and functions as the content of the discussion.

```text
# text = Тушто шуктымо пашам иктешлыме да Россий Прездентын кундемлаште догазификацийым виктарыме шотышто кӱштымашыжым кузе шуктен толмо нерген мутланыме.
# text_en = They discussed the work done there and how the Russian president's instruction on gasification in the regions had been carried out.
1	Тушто	тушто	ADV	_	PronType=Dem	4	advmod	_	_
2	шуктымо	шукташ	VERB	_	VerbForm=Part|Voice=Pass	3	acl	_	_
3	пашам	паша	NOUN	_	Case=Acc|Number=Sing	4	obj	_	_
4	иктешлыме	иктешлаш	VERB	_	VerbForm=Part|Voice=Pass	0	root	_	_
5	да	да	CCONJ	_	_	17	cc	_	_
6	Россий	Россий	PROPN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	Прездентын	президент	NOUN	_	Case=Gen|Number=Sing|Typo=Yes	12	nmod	_	_
8	кундемлаште	кундем	NOUN	_	Case=Ine|Number=Plur	10	obl	_	_
9	догазификацийым	догазификаций	NOUN	_	Case=Acc|Number=Sing	10	obj	_	_
10	виктарыме	виктараш	VERB	_	VerbForm=Part|Voice=Pass	12	acl	_	_
11	шотышто	шотышто	ADP	_	AdpType=Post	10	case	_	_
12	кӱштымашыжым	кӱштымаш	NOUN	_	Case=Acc|Number=Sing|Person[psor]=3|VerbForm=Vnoun	14	obj	_	_
13	кузе	кузе	ADV	_	PronType=Int	14	advmod	_	_
14	шуктен	шукташ	VERB	_	ConvType=Gen|VerbForm=Conv	17	ccomp	_	_
15	толмо	толаш	AUX	_	VerbForm=Part|Voice=Pass	14	aux:aspect	_	_
16	нерген	нерген	ADP	_	AdpType=Post	14	mark	_	_
17	мутланыме	мутланаш	VERB	_	VerbForm=Part|Voice=Pass	4	conj	_	SpaceAfter=No
18	.	.	PUNCT	_	_	17	punct	_	_
```

### xcomp

A predicative complement with obligatory subject control by an external argument. Nominal predicative complements are also possible. 

*standard*

Кугу Авамланде Сар маналтын - Сар is a nominal secondary predicate (compare *They called the war the Great Patriotic War*.)

```text
# text = Российлан тиде сар утарыше ыле, садлан Кугу Авамланде Сар маналтын.
# text_en = For Russia, this war was liberating, and therefore it was called the Great Patriotic War.
# text_orig = Российлан тиде сар ыле, садлан Кугу Авамланде Сар маналтын.
1	Российлан	Россий	PROPN	_	Case=Dat|Number=Sing	4	obl	_	_
2	тиде	тиде	DET	_	PronType=Dem	3	det	_	_
3	сар	сар	NOUN	_	Case=Nom|Number=Sing	4	nsubj	_	_
4	утарыше	утарыше	ADJ	_	_	0	root	_	_
5	ыле	улаш	AUX	_	Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	4	cop	_	SpaceAfter=No
6	,	,	PUNCT	_	_	11	punct	_	_
7	садлан	садлан	ADV	_	PronType=Dem	11	advmod	_	_
8	Кугу	кугу	ADJ	_	_	10	amod	_	_
9	Авамланде	авамланде	ADJ	_	_	10	amod	_	_
10	Сар	сар	NOUN	_	Case=Nom|Number=Sing	11	xcomp	_	_
11	маналтын	маналташ	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	4	parataxis	_	SpaceAfter=No
12	.	.	PUNCT	_	_	4	punct	_	_
```

### obl

An oblique nominal dependent of a predicate: an adverbial or non-core argument. In a postpositional phrase, the nominal remains the phrase head and the postposition takes case.

*standard*

```text
# text = Кыдал Озакъял ял деч 5 километр кечывалвел-эрвел могырно верланен.
# text_en = It is located 5 kilometers southeast of the village of Sredny Ozakyal.
1	Кыдал	кыдал	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	Озакъял	Озакъял	PROPN	_	Case=Nom|Number=Sing	3	compound	_	_
3	ял	ял	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
4	деч	деч	ADP	_	AdpType=Post	3	case	_	_
5	5	5	NUM	_	NumForm=Digit|NumType=Card	6	nummod	_	_
6	километр	километр	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	кечывалвел-эрвел	кечывалвел-эрвел	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
8	могырно	могырно	ADP	_	AdpType=Post	7	case	_	_
9	верланен	верланаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
10	.	.	PUNCT	_	_	9	punct	_	SpaceAfter=No
```

### obl:tmod

A temporal nominal dependent. Word features follow morphology: Case may be recorded or left unspecified in an explicitly defined temporal use. Temporal adverbs take advmod, rather than obl:tmod.

*language-specific: temporal expressions*

```text
# text = Аркадий Букетов-Сайн 1933 ийын шорыкйол тылзын 6-шо кечынже Марий АССР Морко кундем Эҥерӱмбал Шале ялеш кресаньык ешеш шочын.
# text_en = Arkady Buketov-Sain was born on 6 January 1933 into a peasant family in the village of Engerumbal Shale, Morkinsky District, Mari ASSR.
1	Аркадий	Аркадий	PROPN	_	Case=Nom|Number=Sing	18	nsubj	_	_
2	Букетов-Сайн	Букетов-Сайн	PROPN	_	Case=Nom|Number=Sing	1	flat:name	_	_
3	1933	1933	ADJ	_	NumForm=Digit|NumType=Ord	4	amod	_	_
4	ийын	ий	NOUN	_	Case=Gen|Number=Sing	6	nmod	_	_
5	шорыкйол	шорыкйол	NOUN	_	Case=Nom|Number=Sing	6	nmod	_	_
6	тылзын	тылзе	NOUN	_	Case=Gen|Number=Sing	8	nmod	_	_
7	6-шо	6	ADJ	_	NumForm=Digit|NumType=Ord	8	amod	_	_
8	кечынже	кече	NOUN	_	Number=Sing|Number[psor]=Sing|Person[psor]=3	18	obl:tmod	_	_
9	Марий	марий	NOUN	_	Case=Nom|Number=Sing	10	nmod	_	_
10	АССР	АССР	PROPN	_	Abbr=Yes	15	nmod	_	_
11	Морко	Морко	PROPN	_	Case=Nom|Number=Sing	12	compound	_	_
12	кундем	кундем	NOUN	_	Case=Nom|Number=Sing	15	nmod	_	_
13	Эҥерӱмбал	Эҥерӱмбал	PROPN	_	Case=Nom|Number=Sing	15	compound	_	_
14	Шале	Шале	PROPN	_	Case=Nom|Number=Sing	13	flat:name	_	_
15	ялеш	ял	NOUN	_	Case=Lat|Number=Sing	18	obl	_	_
16	кресаньык	кресаньык	NOUN	_	Case=Nom|Number=Sing	17	nmod	_	_
17	ешеш	еш	NOUN	_	Case=Lat|Number=Sing	18	obl	_	_
18	шочын	шочаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
19	.	.	PUNCT	_	_	18	punct	_	_
```

### vocative

An address to the addressee; attaches to the predicate. 

*standard*

Not attested in the corpus.

### expl

A semantically nonreferential expletive. Do not use this for an implicit subject: an absent word does not create a node. Its applicability to Mari has not been established.

*standard*

Not attested in the corpus. 

### dislocated

A dislocated topic outside an ordinary argument position within the clause. Distinguish it from an ordinary subject under free word order.

*standard*

Not attested in the corpus.

### advcl

An adverbial clause, including a converb clause. Distinguish a separate adverbial event from a single auxiliary or serial-verb complex.

*standard*

```text
# text = Налын мундыражым, Шöртньö шӱртылажым Юмын ӱдыр шаҥгак Кас кавам тӱрлен.
# text_en = Taking her ball, her golden threads, God's daughter long ago embroidered the evening sky.
1	Налын	налаш	VERB	_	ConvType=Gen|VerbForm=Conv	11	advcl	_	_
2	мундыражым	мундыра	NOUN	_	Case=Acc|Number=Sing|Number[psor]=Sing|Person[psor]=3	1	obj	_	SpaceAfter=No
3	,	,	PUNCT	_	_	5	punct	_	_
4	Шöртньö	шӧртньӧ	NOUN	_	Case=Nom|Number=Sing	5	nmod	_	CorrectForm=Шӧртньӧ
5	шӱртылажым	шӱртӧ	NOUN	_	Case=Acc|Number=Plur|Number[psor]=Sing|Person[psor]=3	2	conj	_	_
6	Юмын	юмо	NOUN	_	Case=Gen|Number=Sing	7	nmod	_	_
7	ӱдыр	ӱдыр	NOUN	_	Case=Nom|Number=Sing	11	nsubj	_	_
8	шаҥгак	шаҥгак	ADV	_	_	11	advmod	_	_
9	Кас	кас	NOUN	_	Case=Nom|Number=Sing	10	nmod	_	_
10	кавам	кава	NOUN	_	Case=Acc|Number=Sing	11	obj	_	_
11	тӱрлен	тӱрлаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
12	.	.	PUNCT	_	_	11	punct	_	SpaceAfter=No
```

### advmod

An adverbial modifier of a predicate or another modifier. Nominal adverbials take obl. 

*standard*

```text
# text = Таче Медведево районышто вич илыме верым: Нужъял посёлкым, тыгак Нужъял ялым, Изи Лӱльпаным, Яныкайсолам да Лӱльпан ялым – ушышо газ пучым почыныт.
# text_en = Today a connecting gas pipeline was opened to five settlements in Medvedevsky District: the settlement and village of Nuzhyal, Izi Lulpan, Yanykaysola, and the village of Lulpan.
1	Таче	таче	ADV	_	_	26	advmod	_	_
2	Медведево	Медведево	PROPN	_	Case=Nom|Number=Sing	3	compound	_	_
3	районышто	район	NOUN	_	Case=Ine|Number=Sing	6	nmod	_	_
4	вич	вич	NUM	_	NumType=Card|Variant=Short	6	nummod	_	_
5	илыме	илаш	VERB	_	VerbForm=Part|Voice=Pass	6	acl	_	_
6	верым	вер	NOUN	_	Case=Acc|Number=Sing	26	obj	_	SpaceAfter=No
7	:	:	PUNCT	_	_	9	punct	_	_
8	Нужъял	Нужъял	PROPN	_	Case=Nom|Number=Sing	9	compound	_	_
9	посёлкым	посёлко	NOUN	_	Case=Acc|Number=Sing	6	appos	_	SpaceAfter=No
10	,	,	PUNCT	_	_	13	punct	_	_
11	тыгак	тыгак	ADV	_	_	13	advmod	_	_
12	Нужъял	Нужъял	PROPN	_	Case=Nom|Number=Sing	13	compound	_	_
13	ялым	ял	NOUN	_	Case=Acc|Number=Sing	9	conj	_	SpaceAfter=No
14	,	,	PUNCT	_	_	16	punct	_	_
15	Изи	изи	ADJ	_	_	16	amod	_	_
16	Лӱльпаным	Лӱльпан	PROPN	_	Case=Acc|Number=Sing	9	conj	_	SpaceAfter=No
17	,	,	PUNCT	_	_	18	punct	_	_
18	Яныкайсолам	Яныкайсола	PROPN	_	Case=Acc|Number=Sing	9	conj	_	_
19	да	да	CCONJ	_	_	21	cc	_	_
20	Лӱльпан	Лӱльпан	PROPN	_	Case=Nom|Number=Sing	21	compound	_	_
21	ялым	ял	NOUN	_	Case=Acc|Number=Sing	9	conj	_	_
22	–	–	PUNCT	_	_	26	punct	_	_
23	ушышо	ушаш	VERB	_	VerbForm=Part|Voice=Act	25	acl	_	_
24	газ	газ	NOUN	_	Case=Nom|Number=Sing	25	nmod	_	_
25	пучым	пуч	NOUN	_	Case=Acc|Number=Sing	26	obj	_	_
26	почыныт	почаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Plur|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
27	.	.	PUNCT	_	_	26	punct	_	_
```

### advmod:emph

A focus or restrictive marker. In `кож гыч гына`, гына attaches to кож, the head of the restricted nominal phrase, rather than to гыч. This subtype is documented in UD but still needs to be enabled for the language.

*language specific: Restrictive/focus particles: PART + advmod:emph*

- гына

Here, гына restricts the phrase кож гыч (‘from spruce’), rather than expressing an independent discourse-level attitude. Following UD's content-head principle, it attaches to кож, not to the adposition гыч.

```text
# text = Кожла — нугыдын (пырля) кушкын шогышо кож-влак але утларакшым кож гыч гына лийын шогышо иман чодыра.
# text_en = A spruce forest is a group of densely growing spruces or a coniferous forest consisting mainly or exclusively of spruces.
1	Кожла	кожла	NOUN	_	Case=Nom|Number=Sing	9	nsubj	_	_
2	—	—	PUNCT	_	_	9	punct	_	_
3	нугыдын	нугыдын	ADV	_	_	7	advmod	_	_
4	(	(	PUNCT	_	_	5	punct	_	SpaceAfter=No
5	пырля	пырля	ADV	_	_	3	parataxis	_	SpaceAfter=No
6	)	)	PUNCT	_	_	5	punct	_	_
7	кушкын	кушкаш	VERB	_	ConvType=Gen|VerbForm=Conv	9	acl	_	_
8	шогышо	шогаш	AUX	_	VerbForm=Part|Voice=Act	7	aux:aspect	_	_
9	кож-влак	кож	NOUN	_	Case=Nom|Number=Plur	0	root	_	_
10	але	але	CCONJ	_	_	18	cc	_	_
11	утларакшым	утларакшым	ADV	_	_	15	advmod	_	_
12	кож	кож	NOUN	_	Case=Nom|Number=Sing	15	obl	_	_
13	гыч	гыч	ADP	_	AdpType=Post	12	case	_	_
14	гына	гына	PART	_	_	12	advmod:emph	_	_
15	лийын	лияш	VERB	_	ConvType=Gen|VerbForm=Conv	18	acl	_	_
16	шогышо	шогаш	AUX	_	VerbForm=Part|Voice=Act	15	aux:aspect	_	_
17	иман	иман	ADJ	_	_	18	amod	_	_
18	чодыра	чодыра	NOUN	_	Case=Nom|Number=Sing	9	conj	_	SpaceAfter=No
19	.	.	PUNCT	_	_	9	punct	_	_
```

### discourse

A discourse element, including topic-marking гын in this use. Do not automatically assign discourse to every particle.

*standard*

```text
# text = Тидын лӱмеш кугу пайремым эртареныт, кугу яллаште гын ярминка лийын.
# text_en = A large celebration was held in its honor, and in larger villages there was a fair.
1	Тидын	тиде	PRON	_	Case=Gen|Number=Sing|PronType=Dem	5	obl	_	_
2	лӱмеш	лӱмеш	ADP	_	AdpType=Post	1	case	_	_
3	кугу	кугу	ADJ	_	_	4	amod	_	_
4	пайремым	пайрем	NOUN	_	Case=Acc|Number=Sing	5	obj	_	_
5	эртареныт	эртараш	VERB	_	Evident=Nfh|Mood=Ind|Number=Plur|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
6	,	,	PUNCT	_	_	11	punct	_	_
7	кугу	кугу	ADJ	_	_	8	amod	_	_
8	яллаште	ял	NOUN	_	Case=Ine|Number=Plur	11	obl	_	_
9	гын	гын	PART	_	_	8	discourse	_	_
10	ярминка	ярминка	NOUN	_	Case=Nom|Number=Sing	11	nsubj	_	_
11	лийын	лияш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	5	parataxis	_	SpaceAfter=No
12	.	.	PUNCT	_	_	5	punct	_	_
```

### aux

An auxiliary accompanying a lexical verb. Negative and modal AUX take aux in the author’s scheme, without mandatory aux:neg. All functional members of the complex attach to the lexical head.

*standard*

```text
# text = Марий Чодыраял мут ончыктен кертеш:
# text_en = The term "Mari Chodyrayal" can mean:
1	Марий	марий	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	Чодыраял	Чодыраял	PROPN	_	Case=Nom|Number=Sing	3	nmod	_	_
3	мут	мут	NOUN	_	Case=Nom|Number=Sing	4	nsubj	_	_
4	ончыктен	ончыкташ	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
5	кертеш	керташ	AUX	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	4	aux	_	SpaceAfter=No
6	:	:	PUNCT	_	_	5	punct	_	_
```

### aux:aspect

The project’s subtype for a grammaticalized aspectual auxiliary; UPOS=AUX and the auxiliary’s own morphology is retained. Lexical uses of the same verb require a different analysis.

*language specific: aspect auxiliaries*

```text
# text = Азаново сола деч 3 километр кечывалвел-эрвелне лийын шоген.
# text_en = It was located 3 kilometers southeast of the village of Azanovo.
1	Азаново	Азаново	PROPN	_	Case=Nom|Number=Sing	2	compound	_	_
2	сола	сола	NOUN	_	Case=Nom|Number=Sing	7	obl	_	_
3	деч	деч	ADP	_	AdpType=Post	2	case	_	_
4	3	3	NUM	_	NumForm=Digit|NumType=Card	5	nummod	_	_
5	километр	километр	NOUN	_	Case=Nom|Number=Sing	7	obl	_	_
6	кечывалвел-эрвелне	кечывалвел-эрвелне	ADV	_	_	7	advmod	_	_
7	лийын	лияш	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
8	шоген	шогаш	AUX	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	7	aux:aspect	_	SpaceAfter=No
9	.	.	PUNCT	_	_	8	punct	_	_
```

### cop

A pure copula with a nonverbal predicate: the content predicate is the head and the copula is a dependent AUX. 

standard

```text
# text = Йӱдвел теҥыз кӱлешан Европысо корно да тӱҥ колым кучымаш вер кужеш лийын.
# text_en = The North Sea has long been an important European shipping route and a major fishing ground.
1	Йӱдвел	йӱдвел	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	теҥыз	теҥыз	NOUN	_	Case=Nom|Number=Sing	5	nsubj	_	_
3	кӱлешан	кӱлешан	ADJ	_	_	5	amod	_	_
4	Европысо	Европысо	ADJ	_	_	5	amod	_	_
5	корно	корно	NOUN	_	Case=Nom|Number=Sing	0	root	_	_
6	да	да	CCONJ	_	_	10	cc	_	_
7	тӱҥ	тӱҥ	ADJ	_	_	10	amod	_	_
8	колым	кол	NOUN	_	Case=Acc|Number=Sing	9	obj	_	_
9	кучымаш	кучымаш	NOUN	_	Case=Nom|Number=Sing|VerbForm=Vnoun	10	nmod	_	_
10	вер	вер	NOUN	_	Case=Nom|Number=Sing	5	conj	_	_
11	кужеш	кужеш	ADV	_	_	5	advmod	_	_
12	лийын	лияш	AUX	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	5	cop	_	SpaceAfter=No
13	.	.	PUNCT	_	_	11	punct	_	_
```

### mark

A subordinate-clause marker. In the author’s example, нерген is ADP functioning as mark with a clause; with a nominal phrase, it normally takes case. Distinguish morphological UPOS from syntactic role.

*standard*

```text
# text = Тушто шуктымо пашам иктешлыме да Россий Прездентын кундемлаште догазификацийым виктарыме шотышто кӱштымашыжым кузе шуктен толмо нерген мутланыме.
# text_en = They discussed the work done there and how the Russian president's instruction on gasification in the regions had been carried out.
1	Тушто	тушто	ADV	_	PronType=Dem	4	advmod	_	_
2	шуктымо	шукташ	VERB	_	VerbForm=Part|Voice=Pass	3	acl	_	_
3	пашам	паша	NOUN	_	Case=Acc|Number=Sing	4	obj	_	_
4	иктешлыме	иктешлаш	VERB	_	VerbForm=Part|Voice=Pass	0	root	_	_
5	да	да	CCONJ	_	_	17	cc	_	_
6	Россий	Россий	PROPN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	Прездентын	президент	NOUN	_	Case=Gen|Number=Sing|Typo=Yes	12	nmod	_	_
8	кундемлаште	кундем	NOUN	_	Case=Ine|Number=Plur	10	obl	_	_
9	догазификацийым	догазификаций	NOUN	_	Case=Acc|Number=Sing	10	obj	_	_
10	виктарыме	виктараш	VERB	_	VerbForm=Part|Voice=Pass	12	acl	_	_
11	шотышто	шотышто	ADP	_	AdpType=Post	10	case	_	_
12	кӱштымашыжым	кӱштымаш	NOUN	_	Case=Acc|Number=Sing|Person[psor]=3|VerbForm=Vnoun	14	obj	_	_
13	кузе	кузе	ADV	_	PronType=Int	14	advmod	_	_
14	шуктен	шукташ	VERB	_	ConvType=Gen|VerbForm=Conv	17	ccomp	_	_
15	толмо	толаш	AUX	_	VerbForm=Part|Voice=Pass	14	aux:aspect	_	_
16	нерген	нерген	ADP	_	AdpType=Post	14	mark	_	_
17	мутланыме	мутланаш	VERB	_	VerbForm=Part|Voice=Pass	4	conj	_	SpaceAfter=No
18	.	.	PUNCT	_	_	17	punct	_	_
```

### nmod

A nominal modifier of a nominal, including ordinary nominative and genitive modifiers. The current baseline for possessive phrases is nmod; this guide does not additionally introduce nmod:poss.

*standard*

```text
# text = Олык марий йылмым кучылтыт.
# text_en = They use the Meadow Mari language.
1	Олык	олык	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
2	марий	марий	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
3	йылмым	йылме	NOUN	_	Case=Acc|Number=Sing	4	obj	_	_
4	кучылтыт	кучылташ	VERB	_	Mood=Ind|Number=Plur|Person=3|Tense=Pres|VerbForm=Fin	0	root	_	SpaceAfter=No
5	.	.	PUNCT	_	_	4	punct	_	_
```

### appos

An apposition that renames the same referent. In a list of alternative names, the first alternative may take appos, with subsequent alternatives linked by conj.

*standard*

```text
# text = Йӱдвел-касвел марий йылме (Йӱдвел-касвел марий йылме, йӱдйымал-касвел марий йылме, марий йылмын йӱдвел-касвел наречийже; шкен лӱмжӧ йөтнӫмӓл-кӓсвел маре йӹлмӹ) – ик финн-угор йылме.
# text_en = Northwestern Mari (also called Northwestern Mari, northwestern Mari, or the northwestern dialect of Mari; its own name is yötńömäl-käsvel mare yïlmï) is a Finno-Ugric language.
1	Йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
2	марий	марий	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
3	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	27	nsubj	_	_
4	(	(	PUNCT	_	_	7	punct	_	SpaceAfter=No
5	Йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
6	марий	марий	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	3	appos	_	SpaceAfter=No
8	,	,	PUNCT	_	_	11	punct	_	_
9	йӱдйымал-касвел	йӱдйымал-касвел	NOUN	_	Case=Nom|Number=Sing	11	nmod	_	_
10	марий	марий	NOUN	_	Case=Nom|Number=Sing	11	nmod	_	_
11	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	7	conj	_	SpaceAfter=No
12	,	,	PUNCT	_	_	16	punct	_	_
13	марий	марий	NOUN	_	Case=Nom|Number=Sing	14	nmod	_	_
14	йылмын	йылме	NOUN	_	Case=Gen|Number=Sing	16	nmod	_	_
15	йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	16	nmod	_	_
16	наречийже	наречий	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	7	conj	_	SpaceAfter=No
17	;	;	PUNCT	_	_	20	punct	_	_
18	шкен	шке	PRON	_	Case=Gen|PronType=Prs|Reflex=Yes	19	nmod	_	_
19	лӱмжӧ	лӱм	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	20	nsubj	_	_
20	йөтнӫмӓл-кӓсвел	йөтнӫмӓл-кӓсвел	X	_	Foreign=Yes	7	parataxis	_	_
21	маре	маре	X	_	Foreign=Yes	20	flat:foreign	_	_
22	йӹлмӹ	йӹлмӹ	X	_	Foreign=Yes	20	flat:foreign	_	SpaceAfter=No
23	)	)	PUNCT	_	_	7	punct	_	_
24	–	–	PUNCT	_	_	27	punct	_	_
25	ик	ик	DET	_	PronType=Ind	27	det	_	_
26	финн-угор	финн-угор	ADJ	_	_	27	amod	_	_
27	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	0	root	_	SpaceAfter=No
28	.	.	PUNCT	_	_	27	punct	_	_
```

### nummod

A cardinal numerical modifier of a nominal. A noun following a numeral does not receive Number=Plur solely from the quantity expressed.

*standard*

```text
# text = Азаново сола деч 3 километр кечывалвел-эрвелне лийын шоген.
# text_en = It was located 3 kilometers southeast of the village of Azanovo.
1	Азаново	Азаново	PROPN	_	Case=Nom|Number=Sing	2	compound	_	_
2	сола	сола	NOUN	_	Case=Nom|Number=Sing	7	obl	_	_
3	деч	деч	ADP	_	AdpType=Post	2	case	_	_
4	3	3	NUM	_	NumForm=Digit|NumType=Card	5	nummod	_	_
5	километр	километр	NOUN	_	Case=Nom|Number=Sing	7	obl	_	_
6	кечывалвел-эрвелне	кечывалвел-эрвелне	ADV	_	_	7	advmod	_	_
7	лийын	лияш	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
8	шоген	шогаш	AUX	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	7	aux:aspect	_	SpaceAfter=No
9	.	.	PUNCT	_	_	8	punct	_	_
```

### acl

A clause modifying a nominal, including a participial clause. This pilot uses acl for participial modifiers; introducing acl:relcl requires documented criteria. [UD acl](https://universaldependencies.org/u/dep/acl.html).

*standard*

```text
# text = Калянур () — Российыште Марий Элын У Роҥго кундемыштыже верланыше Вечын ял шотан илемыш пурышо ял.
# text_en = Kalyanur is a village belonging to the Vechy rural settlement, which is located in Novy Toryal District, Mari El, Russia.
1	Калянур	Калянур	PROPN	_	Case=Nom|Number=Sing	17	nsubj	_	_
2	(	(	PUNCT	_	_	1	punct	_	SpaceAfter=No
3	)	)	PUNCT	_	_	1	punct	_	_
4	—	—	PUNCT	_	_	17	punct	_	_
5	Российыште	Россий	PROPN	_	Case=Ine|Number=Sing	11	obl	_	_
6	Марий	марий	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	Элын	эл	NOUN	_	Case=Gen|Number=Sing	10	nmod	_	_
8	У	у	ADJ	_	_	9	amod	_	_
9	Роҥго	Роҥго	PROPN	_	Case=Nom|Number=Sing	10	compound	_	_
10	кундемыштыже	кундем	NOUN	_	Case=Ine|Number=Sing|Number[psor]=Sing|Person[psor]=3	11	obl	_	_
11	верланыше	верланаш	VERB	_	VerbForm=Part|Voice=Act	15	acl	_	_
12	Вечын	Вечын	PROPN	_	Case=Nom|Number=Sing	15	compound	_	_
13	ял	ял	NOUN	_	Case=Nom|Number=Sing	14	obl	_	_
14	шотан	шотан	ADJ	_	_	15	amod	_	_
15	илемыш	илем	NOUN	_	Case=Ill|Number=Sing	16	obl	_	_
16	пурышо	пураш	VERB	_	VerbForm=Part|Voice=Act	17	acl	_	_
17	ял	ял	NOUN	_	Case=Nom|Number=Sing	0	root	_	SpaceAfter=No
18	.	.	PUNCT	_	_	17	punct	_	_
```

### amod

An adjectival modifier of a nominal, including ordinal ADJ. A nominal modifier tagged NOUN normally takes nmod, rather than amod.

*standard*

```text
# text = У верыште балто-финн ӱлыл тӱшкалан негызым пыштен.
# text_en = In the new place, it laid the foundation for the southern Baltic-Finnic subgroup.
1	У	у	ADJ	_	_	2	amod	_	_
2	верыште	вер	NOUN	_	Case=Ine|Number=Sing	7	obl	_	_
3	балто-финн	балто-финн	ADJ	_	_	5	amod	_	_
4	ӱлыл	ӱлыл	ADJ	_	_	5	amod	_	_
5	тӱшкалан	тӱшка	NOUN	_	Case=Dat|Number=Sing	7	obl	_	_
6	негызым	негыз	NOUN	_	Case=Acc|Number=Sing	7	obj	_	_
7	пыштен	пышташ	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
8	.	.	PUNCT	_	_	7	punct	_	_
```

### det

A pronominal determiner or quantifier of a nominal phrase. 

*standard*

```text
# text = Вӱд спорт тӱрлык дене Тӱнямбал чемпионатын программышкыж тыгай спорт тӱрлык-влак пурат:
# text_en = The following sports are included in the World Championships program for water sports:
1	Вӱд	вӱд	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	спорт	спорт	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
3	тӱрлык	тӱрлык	NOUN	_	Case=Nom|Number=Sing	6	nmod	_	_
4	дене	дене	ADP	_	AdpType=Post	3	case	_	_
5	Тӱнямбал	тӱнямбал	ADJ	_	_	6	amod	_	_
6	чемпионатын	чемпионат	NOUN	_	Case=Gen|Number=Sing	7	nmod	_	_
7	программышкыж	программе	NOUN	_	Case=Ill|Number=Sing|Number[psor]=Sing|Person[psor]=3	11	obl	_	_
8	тыгай	тыгай	DET	_	PronType=Dem	10	det	_	_
9	спорт	спорт	NOUN	_	Case=Nom|Number=Sing	10	nmod	_	_
10	тӱрлык-влак	тӱрлык	NOUN	_	Case=Nom|Number=Plur	11	nsubj	_	_
11	пурат	пураш	VERB	_	Mood=Ind|Number=Plur|Person=3|Tense=Pres|VerbForm=Fin	0	root	_	SpaceAfter=No
12	:	:	PUNCT	_	_	11	punct	_	_
```

### case

A postposition or other marker accompanying a nominal phrase; it depends on the phrase’s lexical head. A morphological Case feature on the nominal does not replace this relation.

*standard*

```text
# text = Кыдал Озакъял ял деч 5 километр кечывалвел-эрвел могырно верланен.
# text_en = It is located 5 kilometers southeast of the village of Sredny Ozakyal.
1	Кыдал	кыдал	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	Озакъял	Озакъял	PROPN	_	Case=Nom|Number=Sing	3	compound	_	_
3	ял	ял	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
4	деч	деч	ADP	_	AdpType=Post	3	case	_	_
5	5	5	NUM	_	NumForm=Digit|NumType=Card	6	nummod	_	_
6	километр	километр	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	кечывалвел-эрвел	кечывалвел-эрвел	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
8	могырно	могырно	ADP	_	AdpType=Post	7	case	_	_
9	верланен	верланаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
10	.	.	PUNCT	_	_	9	punct	_	SpaceAfter=No
```

### conj

The first conjunct is the head; subsequent conjuncts depend on it. A shared postposition attaches to the first phrase head in the basic tree.

*standard*

```text
# text = Илышыже да творчествыже нерген литератур
# text_en = Literature about his life and work.
1	Илышыже	илыш	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	5	nmod	_	_
2	да	да	CCONJ	_	_	3	cc	_	_
3	творчествыже	творчестве	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	1	conj	_	_
4	нерген	нерген	ADP	_	AdpType=Post	1	case	_	_
5	литератур	литератур	NOUN	_	Case=Nom|Number=Sing	0	root	_	_
```

### cc

A coordinating conjunction depends on the conjunct it introduces, usually the second or a later conjunct. The rule for да монь is retained below.

*standard*

```text
# text = Илышыже да творчествыже нерген литератур
# text_en = Literature about his life and work.
1	Илышыже	илыш	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	5	nmod	_	_
2	да	да	CCONJ	_	_	3	cc	_	_
3	творчествыже	творчестве	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	1	conj	_	_
4	нерген	нерген	ADP	_	AdpType=Post	1	case	_	_
5	литератур	литератур	NOUN	_	Case=Nom|Number=Sing	0	root	_	_
```

**Notes:**

- да монь:

Enumerative particles: монь (etc., and so on) is annotated PART and attached as conj to the first item in the enumeration. In да монь, да is annotated CCONJ, attached to монь using cc.

### fixed

Only grammaticalized fixed functional expressions lacking ordinary internal syntactic structure qualify. All subsequent components attach to the first. `ExtPos' FEAT is used if the multiword expression UPOS is not identical to the UPOS of the head node [UD fixed](https://universaldependencies.org/u/dep/fixed.html).

*standard*

```text
# text = Йошкар-Ола мут дене тӱҥ шотышто Марий Эл Республикын рӱдӧ олажым лӱмдат.
# text_en = The word "Yoshkar-Ola" primarily names the capital of the Mari El Republic.
1	Йошкар-Ола	Йошкар-Ола	PROPN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	мут	мут	NOUN	_	Case=Nom|Number=Sing	11	obl	_	_
3	дене	дене	ADP	_	AdpType=Post	2	case	_	_
4	тӱҥ	тӱҥ	ADV	_	_	11	advmod	_	_
5	шотышто	шотышто	ADP	_	AdpType=Post	4	fixed	_	_
6	Марий	марий	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	Эл	эл	NOUN	_	Case=Nom|Number=Sing	8	nmod	_	_
8	Республикын	республик	PROPN	_	Case=Gen|Number=Sing	10	nmod	_	_
9	рӱдӧ	рӱдӧ	ADJ	_	_	10	amod	_	_
10	олажым	ола	NOUN	_	Case=Acc|Number=Sing|Number[psor]=Sing|Person[psor]=3	11	obj	_	_
11	лӱмдат	лӱмдаш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	0	root	_	SpaceAfter=No
12	.	.	PUNCT	_	_	11	punct	_	_
```

### flat

For an expression without a uniquely identifiable head; it does not replace ordinary syntactic analysis of a transparent name. Subsequent components attach to the first. [UD flat](https://universaldependencies.org/u/dep/flat.html).

*standard*

Not attested in the corpus.

### flat:foreign

For an opaque foreign-language fragment whose internal structure is not analyzed in this annotation. 

*language specific*

In the Meadow Mari treebank, other dialects of Mari are intentionally analyzed as foreign. 

```text
# text = Йӱдвел-касвел марий йылме (Йӱдвел-касвел марий йылме, йӱдйымал-касвел марий йылме, марий йылмын йӱдвел-касвел наречийже; шкен лӱмжӧ йөтнӫмӓл-кӓсвел маре йӹлмӹ) – ик финн-угор йылме.
# text_en = Northwestern Mari (also called Northwestern Mari, northwestern Mari, or the northwestern dialect of Mari; its own name is yötńömäl-käsvel mare yïlmï) is a Finno-Ugric language.
1	Йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
2	марий	марий	NOUN	_	Case=Nom|Number=Sing	3	nmod	_	_
3	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	27	nsubj	_	_
4	(	(	PUNCT	_	_	7	punct	_	SpaceAfter=No
5	Йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
6	марий	марий	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	3	appos	_	SpaceAfter=No
8	,	,	PUNCT	_	_	11	punct	_	_
9	йӱдйымал-касвел	йӱдйымал-касвел	NOUN	_	Case=Nom|Number=Sing	11	nmod	_	_
10	марий	марий	NOUN	_	Case=Nom|Number=Sing	11	nmod	_	_
11	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	7	conj	_	SpaceAfter=No
12	,	,	PUNCT	_	_	16	punct	_	_
13	марий	марий	NOUN	_	Case=Nom|Number=Sing	14	nmod	_	_
14	йылмын	йылме	NOUN	_	Case=Gen|Number=Sing	16	nmod	_	_
15	йӱдвел-касвел	йӱдвел-касвел	NOUN	_	Case=Nom|Number=Sing	16	nmod	_	_
16	наречийже	наречий	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	7	conj	_	SpaceAfter=No
17	;	;	PUNCT	_	_	20	punct	_	_
18	шкен	шке	PRON	_	Case=Gen|PronType=Prs|Reflex=Yes	19	nmod	_	_
19	лӱмжӧ	лӱм	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	20	nsubj	_	_
20	йөтнӫмӓл-кӓсвел	йөтнӫмӓл-кӓсвел	X	_	Foreign=Yes	7	parataxis	_	_
21	маре	маре	X	_	Foreign=Yes	20	flat:foreign	_	_
22	йӹлмӹ	йӹлмӹ	X	_	Foreign=Yes	20	flat:foreign	_	SpaceAfter=No
23	)	)	PUNCT	_	_	7	punct	_	_
24	–	–	PUNCT	_	_	27	punct	_	_
25	ик	ик	DET	_	PronType=Ind	27	det	_	_
26	финн-угор	финн-угор	ADJ	_	_	27	amod	_	_
27	йылме	йылме	NOUN	_	Case=Nom|Number=Sing	0	root	_	SpaceAfter=No
28	.	.	PUNCT	_	_	27	punct	_	_
```

### flat:name

For name components without ordinary head–dependent structure; the first component is the head in Муржинов Григорий Петрович. Transparent adjectival and nominal components of names retain amod/nmod.

*language specific*

```text
# text = Муржинов Григорий Петрович – Йошкар Шӱдыр орденын кавалерже, 1941–1945 ийласе Кугу Ачамланде сарын участникше.
# text_en = Grigory Petrovich Murzhinov was a recipient of the Order of the Red Star and a participant in the Great Patriotic War of 1941–1945.
1	Муржинов	Муржинов	PROPN	_	Case=Nom|Number=Sing	8	nsubj	_	_
2	Григорий	Григорий	PROPN	_	Case=Nom|Number=Sing	1	flat:name	_	_
3	Петрович	Петрович	PROPN	_	Case=Nom|Number=Sing	1	flat:name	_	_
4	–	–	PUNCT	_	_	8	punct	_	_
5	Йошкар	йошкар	ADJ	_	Variant=Short	6	amod	_	_
6	Шӱдыр	шӱдыр	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	орденын	орден	NOUN	_	Case=Gen|Number=Sing	8	nmod	_	_
8	кавалерже	кавалер	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	0	root	_	SpaceAfter=No
9	,	,	PUNCT	_	_	17	punct	_	_
10	1941	1941	NUM	_	NumForm=Digit|NumType=Card	13	nummod	_	SpaceAfter=No
11	–	–	PUNCT	_	_	12	punct	_	SpaceAfter=No
12	1945	1945	NUM	_	NumForm=Digit|NumType=Card	10	nmod	_	_
13	ийласе	ийласе	ADJ	_	_	16	amod	_	_
14	Кугу	кугу	ADJ	_	_	16	amod	_	_
15	Ачамланде	ачамланде	ADJ	_	_	16	amod	_	_
16	сарын	сар	NOUN	_	Case=Gen|Number=Sing	17	nmod	_	_
17	участникше	участник	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	8	conj	_	SpaceAfter=No
18	.	.	PUNCT	_	_	8	punct	_	_
```

### list

For elements of a list structure without an ordinary syntactic relation. Do not use it instead of conj for normal coordination.

*standard*

Not attested in the corpus.

### parataxis

A relation between independent juxtaposed clauses or a parenthetical statement. 

*standard*

```text
# text = 1940-ан ийлаште Келдаксола ял Изи Каракша ял деке ушалтын, кызыт Шкетан уремже лиеш.
# text_en = In the 1940s, the village of Keldaksola was incorporated into the village of Izi Karaksha; it is now Shketan Street.
1	1940-ан	1940	ADJ	_	NumType=Ord	2	amod	_	_
2	ийлаште	ий	NOUN	_	Case=Ine|Number=Plur	9	obl:tmod	_	_
3	Келдаксола	Келдаксола	PROPN	_	Case=Nom|Number=Sing	4	compound	_	_
4	ял	ял	NOUN	_	Case=Nom|Number=Sing	9	nsubj	_	_
5	Изи	изи	ADJ	_	_	7	amod	_	_
6	Каракша	Каракша	PROPN	_	Case=Nom|Number=Sing	7	compound	_	_
7	ял	ял	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
8	деке	деке	ADP	_	AdpType=Post	7	case	_	_
9	ушалтын	ушалташ	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
10	,	,	PUNCT	_	_	14	punct	_	_
11	кызыт	кызыт	ADV	_	_	14	advmod	_	_
12	Шкетан	Шкетан	PROPN	_	Case=Nom|Number=Sing	13	nmod	_	_
13	уремже	урем	NOUN	_	Case=Nom|Number=Sing|Number[psor]=Sing|Person[psor]=3	14	xcomp	_	_
14	лиеш	лияш	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	9	parataxis	_	SpaceAfter=No
15	.	.	PUNCT	_	_	9	punct	_	SpaceAfter=No
```

### compound

A nominal component of a compound name. Under the current working scheme, a geographical name attaches to the generic designation; ordinary nominal modifiers take nmod by default. This distinction requires consistent review.

*standard*

```text
# text = Кыдал Озакъял ял деч 5 километр кечывалвел-эрвел могырно верланен.
# text_en = It is located 5 kilometers southeast of the village of Sredny Ozakyal.
1	Кыдал	кыдал	NOUN	_	Case=Nom|Number=Sing	2	nmod	_	_
2	Озакъял	Озакъял	PROPN	_	Case=Nom|Number=Sing	3	compound	_	_
3	ял	ял	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
4	деч	деч	ADP	_	AdpType=Post	3	case	_	_
5	5	5	NUM	_	NumForm=Digit|NumType=Card	6	nummod	_	_
6	километр	километр	NOUN	_	Case=Nom|Number=Sing	7	nmod	_	_
7	кечывалвел-эрвел	кечывалвел-эрвел	NOUN	_	Case=Nom|Number=Sing	9	obl	_	_
8	могырно	могырно	ADP	_	AdpType=Post	7	case	_	_
9	верланен	верланаш	VERB	_	Evident=Nfh|Mood=Ind|Number=Sing|Person=3|Tense=Past|VerbForm=Fin	0	root	_	SpaceAfter=No
10	.	.	PUNCT	_	_	9	punct	_	SpaceAfter=No
```

**Notes:**

- compound or nmod:

| Construction | My provisional choice | Reason |
| --- | --- | --- |
| Хлебниково сола | compound | Name + geographical type; compound is well motivated |
| графике информаций | nmod | графике functions as a nominal attribute |
| идым мут | nmod | Needs contextual and lexical analysis |
| физика-математика шанче | nmod | Compositional relation specifying scientific discipline |
| марий йылмын наречийже | nmod | Explicit genitive nominal modifier |

A preliminary policy for the treebank:

| Construction type | Dependency | Confidence |
| --- | --- | --- |
| Proper name + settlement type | compound | High |
| Geographical name with complex administrative designation | compound between the name and designation head | High |
| Genitive nominal modifier | nmod | High |
| Ordinary nominative nominal modifier | nmod by default | Moderate |
| Metalinguistic label + мут | nmod provisionally | Moderate |
| Lexical noun–noun compound | compound | Requires morphological or syntactic evidence |

### compound:svc

A serial complex of lexical verbs describing one event and sharing a subject. Do not apply this to grammaticalized AUX, or treat every converb as evidence of serialization. [UD compound:svc](https://universaldependencies.org/u/dep/compound-svc.html).

*language specific*

In this type of construction, several verbs are combined to describe the same action and this construction is attested in the dictionaries. Эрташ is not considered an auxiliary verb in Mari grammar.

```text
# text = Сола гоч Оръю эҥер йоген эрта.
# text_en = The Oryu River flows through the village.
1	Сола	сола	NOUN	_	Case=Nom|Number=Sing	5	obl	_	_
2	гоч	гоч	ADP	_	AdpType=Post	1	case	_	_
3	Оръю	Оръю	PROPN	_	Case=Nom|Number=Sing	4	compound	_	_
4	эҥер	эҥер	NOUN	_	Case=Nom|Number=Sing	5	nsubj	_	_
5	йоген	йогаш	VERB	_	ConvType=Gen|VerbForm=Conv	0	root	_	_
6	эрта	эрташ	VERB	_	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	5	compound:svc	_	SpaceAfter=No
7	.	.	PUNCT	_	_	6	punct	_	_
```

### orphan

Links remnants under predicate ellipsis when an ordinary relation would imply an incorrect analysis. First consider promotion of a surviving component; do not add empty nodes to the basic tree.

*standard*

Not attested in the corpus.

### goeswith

Parts of a word incorrectly split in writing; not ordinary separately written compound names. Preserve the original parts and mark the typo.

*standard*

Not attested in the corpus.

### reparandum

A fragment replaced in a self-correction; it depends on the replacement fragment. This is not for editorial changes to source text.

*standard*

Not attested in the corpus.

### punct

A punctuation mark depends on the head of the unit it delimits. Do not make punctuation the head of a content word; check brackets, coordination, and clause endings.

*standard*

### root

Exactly one root in the basic tree, with HEAD=0. The root may be VERB, NOUN, ADJ, or another content-word category, as licensed by the construction.

*standard*


<a id="other-upos"></a>
## 5.1. Parts of speech without a separate source table

| UPOS | Annotator guidance |
|---|---|
| SCONJ | A subordinating function word, usually taking mark. The source provides no closed inventory: do not treat every occurrence of гын or an interrogative word as a conjunction; a pronominal word with a role inside the clause retains that role |
| INTJ | An independent interjection, response, or reaction formula in the appropriate use. Items in the traditional particle inventory require contextual review |
| PUNCT | A separate punctuation mark, normally with LEMMA=FORM, FEATS=_, DEPREL=punct |
| SYM | A symbol with its own function, rather than ordinary punctuation; quantity digits remain NUM |
| X | A word without an available ordinary analysis. The source foreign-language example uses X Foreign=Yes, but an analyzable loan or proper name receives ordinary UPOS |

This addition describes the available universal categories; it does not claim that
all 17 are already represented in the current Mari sample.
[UD tags and features](https://universaldependencies.org/guidelines.html).

<a id="issues"></a>
## 6. Questions for adjudication and editorial clarifications

### 6.1. Open-decision log

| ID | Case | Procedure pending a final decision |
|---|---|---|
| Q1 | `кечынже`, `кечын`, `шочмын`, `эрдене`, `кастене` | Preserve distinction between a productively inflected nominal and a lexicalized adverb. `июньышто`, `ийыште`: NOUN Case=Ine; `шошым`, `телым`, `кенгежым`, `шыжым`: NOUN Case=Acc. We now leave Case unspecified for `кечынже` and `кечын`. Establish whether forms in -дене are single words or require segmentation, and confirm the analyses of `кечывалым`, `йудым` |
| Q2 | `шуко`, `ятыр`, `шагал`, `икмыняр(е)`, `южо`, `иктаж-мыняр`, `чумыр` | Ask a Mari expert to check distinctions in чумыр класс, чумыр йылме, чумыр положений, пашам чумыр ышташ; record the final lexicon |
| Q3 | `уке` | Working ADJ Polarity=Neg in an existential predicate; the response ‘no’ in the particle inventory is a separate use. Comparison with AUX in Erzya/Moksha does not establish the Mari category; syntactic and morphological evidence is needed |
| Q4 | Years and decades | `1940-ан` requires confirmation of its analysis and consistent NumForm=Digit for digit notation |
| Q5 | `PartForm=FutNec` and `Aspect=Prosp` | The source proposes FutNec; the question for a Mari expert about deontic versus prospective meaning remains open. Do not silently replace it with Aspect=Prosp or assign both without a definition |
| Q6 | `iobj` | For Mari, there remains a question on the existence of dative core objects, therefore such objects in dative case are annotated as obl until further investigation. |
| Q7 | Indefinite and total forms | `чылажат`, `чылаже`, `чыланже` are provisionally ADV; the current dataset hasn't provided any samples of those as yet. Also confirm the distinction between pronominal and lexicalized derivatives, retaining PronType where justified |

<a id="ud-comparison"></a>
## 7. Related-language comparisons and UD requirements

### 7.1. What comparison contributes

The guide follows the organization of UD language pages: tokenization, morphology,
and syntax. Related-language decisions provide comparison points, but Mari
properties must be supported by Mari examples.

| Source | Useful model and limits on transfer |
|---|---|
| [Komi-Zyrian](https://universaldependencies.org/kpv/index.html) | Describes hyphenated tokens, case across UPOS categories, and Connegative. Modal verbs are not considered AUX there; this directly differs from several Mari proposals |
| [Erzya](https://universaldependencies.org/myv/index.html) | Illustrates possessive marking beyond NOUN and an inventory of syntactic subtypes. Negative AUX take aux:neg; the Mari dataset uses basic aux |
| [Moksha](https://universaldependencies.org/mdf/index.html) | Useful for negative copulas, possessor indexing, and nmod/obl distinctions. Its case and mood inventories and xcomp:ds are not automatically transferred |

### 7.2. Local extension definitions

Until official registration, these entries are project definitions.
Confirm the categories, values, and examples for each extension.

| Extension | Definition and project use |
|---|---|
| ConvType=Gen | Generic/instructive converb: кочкын; negative кочде with Polarity=Neg. Only with VerbForm=Conv |
| ConvType=Ant | The dependent verb’s action precedes the reference action: кочмек(е), ‘after eating’. VerbForm=Conv |
| ConvType=Post | The dependent verb’s action follows the reference action: кочмеш(ке), ‘before eating’. VerbForm=Conv |
| ConvType=Sim | Dependent and reference actions are simultaneous: кочшыла, ‘while eating’. VerbForm=Conv |
| InfForm=Nec | Necessitive infinitive: кочман, ‘one must eat’. VerbForm=Inf |
| PartForm=FutNec | Future-necessitive participle: кочшаш. VerbForm=Part |
| Variant=Short/Long | Short/full-form opposition within a specific ADJ or cardinal NUM paradigm; not a feature of every short word |
| PartType=Mod | Modal PART; the inventory of forms and contexts remains to be approved |
| aux:aspect | A grammaticalized aspectual AUX depending on the lexical verb, see table in AUX |
| advmod:emph | Phrase restriction/focus: гына depends on кож in кож гыч гына; the subtype is documented globally in UD |
| obl:tmod | A temporal nominal dependent: кечынже → шочын; temporal ADV retains advmod |
| flat:name / flat:foreign | Respectively, a name without an ordinary internal head and an opaque foreign-language expression; subsequent components attach to the first |
| compound:svc | A single event with multiple lexical verbs and a shared subject; distinct from aux:aspect and advcl |

Some feature names and subtypes occur across languages or have global documentation;
this does not mean that any new value is already permitted for `mhr`. Examples
without actual contexts cannot be described as corpus-attested.

### 7.3. Requirements for official documentation and release

UD requires a language overview page and separate pages for extensions not documented
globally. All used UPOS–feature–value combinations, relation subtypes, auxiliary/copula
lemmas, and exceptions for tokens with spaces require configuration in the language
lists for validation. This file does not perform that registration.
[Language-specific documentation requirements](https://universaldependencies.org/contributing/language_specific.html).

Adding a language/treebank requires a separate process. This document does not claim
that the process is complete or that UD has approved this guide.
[Adding a language or treebank](https://universaldependencies.org/contributing/adding_language_treebank.html).

<a id="quality"></a>
## 8. Annotation quality checks

Before handing a batch to a second annotator, check that:

- Original FORM, diacritics, word boundaries, spaces, and punctuation are preserved;
  `# text` can be reconstructed using MWT and SpaceAfter=No.
- Each sentence has a unique sent_id; ordinary rows have 10 columns; FEATS and MISC
  are kept separate; feature capitalization and ordering are normalized.
- The basic tree has exactly one root with HEAD=0; other words have valid HEAD
  values; there are no self-loops or cycles; every word connects to the root.
- A nominal postposition takes case to the nominal; cc attaches to the introduced
  conjunct; auxiliaries do not replace the lexical head; no zero copula is inserted.
- Case is not inferred solely from adverbial meaning; Number[psor] and Number are
  distinguished; infinitives, participles, converbs, Past II, and connegatives
  are not conflated.
- Cardinal/ordinal readings of digits, DET/ADJ/PRON, modal/lexical verb uses, and
  PART versus other categories have been reviewed in context.
- All unresolved Q-cases are logged separately; completed sentences have no
  temporary alternatives such as `ADJ/DET` in UPOS. Two annotators have adjudicated
  their disagreements.
- Following adjudication, comparable cases are reviewed consistently. Provenance
  and train/dev/test separation are preserved; evaluation sentences have not
  entered training or language-model adaptation data.

Run the current **official UD validator** from
[UniversalDependencies/tools](https://github.com/UniversalDependencies/tools)
on the final CoNLL-U with Mari language settings. Check the invocation and
validation levels against the
[validation documentation](https://universaldependencies.org/contributing/validation.html).
Format validation does not establish linguistic correctness. A release requires
successful official validation; local project checks do not replace it.

<a id="references"></a>
## 9. Official references

Checked 2026-10-31. These links point to current UD v2 pages; when freezing a corpus
version, also record the date/revision of the documentation used.

- [General UD guidelines](https://universaldependencies.org/guidelines.html).
- [Language-specific documentation: required sections and extensions](https://universaldependencies.org/contributing/language_specific.html).
- [CoNLL-U](https://universaldependencies.org/format.html) and
  [tokenization](https://universaldependencies.org/u/overview/tokenization.html).
- [Komi-Zyrian](https://universaldependencies.org/kpv/index.html),
  [Erzya](https://universaldependencies.org/myv/index.html),
  [Moksha](https://universaldependencies.org/mdf/index.html).
- [Validation](https://universaldependencies.org/contributing/validation.html) and
  [contributing to UD](https://universaldependencies.org/contributing/index.html).
- Riese et al. 2022: Riese, T., Bradley, J., & Yefremova, T. (2022). Mari (марий йылме): An essential grammar for international learners. Note: the guidelines contain many unspecified citations from this book.
