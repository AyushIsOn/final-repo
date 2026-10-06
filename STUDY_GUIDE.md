# DSE 4150 NLP: Mid-Term Study Guide (Lectures 1–20)

Built from the 14 PDFs in this repo, following the lecture plan in the Course Handout.
Mid-term is **closed book, 30 marks**. Source PDF for each section is in *(italics)*.

**What the handout says is examinable up to Lecture 20**
| Lec | Topic |
|---|---|
| 2–5 | Intro: phases of NLP, knowledge in speech & language processing, ambiguity, models & algorithms |
| 6–10 | FSM: acceptor vs transducer, formal languages, DFA, NFA, DFA vs NFA, basic regex |
| 11–19 | Morphology: English morphology, inflectional/derivational, FS morphological parsing, FS lexicon, FSTs, orthographic rules, lexicon-free FSTs |
| 20 | Word & sentence tokenization |

---

## 1. Introduction to NLP *(NLP-UNIT-1-notes)*

**NLP** is the branch of AI that lets computers understand, interpret and generate human language (text or speech). It draws on computer science, linguistics and machine learning.

### Levels / phases of NLP (likely question)
| Level | Deals with | Example |
|---|---|---|
| Phonological | speech sounds | /b/ vs /p/ |
| Morphological | word structure (morphemes) | un+happy, play→playing |
| Lexical | individual word meaning, POS | *bank* (river/money) |
| Syntactic | grammar, sentence structure | "She is reading a book" ✓ vs "She reading is book" ✗ |
| Semantic | meaning | "I saw a man with a telescope" |
| Discourse | links across sentences | "Ravi went home. **He** was tired." |
| Pragmatic | intent and context | "Can you open the window?" is a request |

The short form taught as the 5 phases: **Lexical → Syntactic → Semantic → Discourse → Pragmatic.**

### Ambiguity (comes up in many questions)
- **Lexical:** one word has many meanings (bank). The words are *polysemous* or *homonymous*.
- **Structural/syntactic:** "I saw the man with a telescope" (who has the telescope?)
- **Morphological:** *unionised* = union-ise-ed or un-ion-ise-ed; *shorts* = shorts or short+s
- **Pragmatic:** the literal meaning differs from the intended one.
- How it's resolved: context, semantic analysis (word sense disambiguation, semantic role labelling), syntactic analysis, pragmatic analysis, statistical methods.

### History
- **1950s–70s, rule-based.** The Georgetown-IBM experiment (1954) translated 60 Russian sentences into English.
- **1980s–90s, statistical.** n-grams, HMMs, NER, statistical machine translation.
- **2000s onward, deep learning.** RNNs, Transformers, BERT, GPT, Word2Vec, GloVe.

### Challenges
1. Language diversity
2. Training data
3. Development time and resources
4. Phrasing ambiguity
5. Misspellings and grammar errors
6. Bias
7. Words with multiple meanings
8. Multilingualism
9. Uncertainty and false positives
10. Continuous conversation

### Applications
Machine translation, information retrieval, question answering, chatbots, information extraction, summarization, text classification, sentiment analysis, spell checking, speech recognition, NER.

### Types of grammar (short notes)
- **Transformational (Chomsky):** deep structure vs surface structure (active → passive).
- **LFG (Bresnan & Kaplan):** c-structure (phrase structure) + f-structure (subject, object, tense).
- **Government & Binding (Chomsky):** government = head–complement relation; binding = pronoun reference.
- **GPSG (Gazdar):** non-transformational phrase-structure rules plus features.
- **Dependency:** word-to-head relations, with the verb as root.
- **Paninian:** built for Sanskrit and Indian free word-order languages. Uses the **Karaka** roles Karta (doer), Karma (object), Karana (instrument), Adhikarana (location).
- **TAG (Aravind Joshi):** initial and auxiliary trees combined by substitution and adjunction.
- **CFG:** 4 parts: non-terminals, terminals, productions, start symbol S.
- **PCFG:** each rule has a probability, and the probabilities for one left-hand side sum to 1.

---

## 2. Theory of Computation basics *(Introduction to Theory of Computation)*

- **Symbol:** the smallest building block.
- **Alphabet Σ:** a finite, non-empty set of symbols, e.g. {a, b}.
- **String w:** a finite sequence of symbols. |w| is its length, and **ε** is the empty string (|ε| = 0).
- Over Σ = {a, b}, the number of strings of length n is **2ⁿ** (for an alphabet of size k it is kⁿ).
- **Kleene closure Σ\*** = all strings including ε. **Positive closure Σ⁺** = Σ\* − {ε}. So **L\* = {ε} ∪ L⁺**.
  - a\* = {ε, a, aa, …}; a⁺ = {a, aa, …}
- **Language L ⊆ Σ\*.** It can be finite (e.g. all strings of length 2: {aa, ab, ba, bb}) or infinite (e.g. all strings starting with b).

**Chomsky hierarchy (automaton → language class)**
| Automaton | Language class | Example |
|---|---|---|
| Finite automaton | Regular | aⁿ, aⁿbᵐ |
| Pushdown automaton | Context-free | **aⁿbⁿ** (no FSA can recognise this) |
| Linear-bounded automaton | Context-sensitive | |
| Turing machine | Recursive / recursively enumerable | |

**Acceptor vs transducer:** an *acceptor* (FSA) only says accept or reject. A *transducer* (FST) maps an input string to an output string.

---

## 3. Finite Automata: DFA and NFA *(ToC notes, Beyond_Determinism, DFA_Design_Blueprint, Length_Two_DFA_Design, NFA_Formal_Architecture, PPT-2)*

### Formal definition: a 5-tuple (Q, Σ, δ, q₀, F)
- Q: finite set of states
- Σ: input alphabet
- δ: transition function
- q₀: start state
- F ⊆ Q: final (accepting) states

| | DFA | NFA |
|---|---|---|
| δ | **Q × Σ → Q** | **Q × Σ → 2^Q** (power set); with ε-moves, Q × (Σ ∪ {ε}) → 2^Q |
| Next state | exactly one, unique ("absolute assurance") | zero, one or many |
| Missing transitions | **not allowed** (every state needs a move on every symbol) | allowed (go to ∅, the path dies) |
| ε-moves | no | yes (jump without consuming input) |
| Acceptance | ends in a final state | **any** path ends in a final state |
| Execution | single path | paths chosen at random or run in parallel |
| Design | rigid, possibly more states | easier and flexible |
| Power | regular languages | **the same**: every NFA can be converted to a DFA |

- **Why 2^Q?** With 2 states {A, B} the possible targets are [A], [B], [A,B], [∅], which is 4 = 2². With 3 states there are 8 = 2³.
- **Number of DFA states after subset construction:** 1 ≤ n ≤ 2ᵐ (m = number of NFA states).
- **Dead / trap state:** a non-final state with self-loops on every symbol. Once the machine enters it, it can never reach a final state.
- **Running time:** recognising a string with a DFA is linear in |w|.

### Standard DFA designs (practise drawing these, Σ = {0,1} or {a,b})

**(a) Strings starting with 0** *(DFA_Design_Blueprint)*
| State | 0 | 1 |
|---|---|---|
| → A | B | C |
| \* B | B | B |
| C (dead) | C | C |

Trace for 101: A→C→C→C, so REJECTED. Trace for 01: A→B→B, so ACCEPTED.

**(b) Strings of exactly length 2** *(Length_Two_DFA_Design)*

Each state counts characters read: A = length 0, B = length 1, C = length 2 (final), D = length 3+ (dead).
A —0,1→ B —0,1→ **C** —0,1→ D (loops on 0,1)
- 00 and 10 are accepted (they end in C).
- 1 is rejected (undershoot, ends in B).
- 001 is rejected (overshoot, ends in D).

**(c) Strings ending with 'a'**
- DFA: q0 —a→ q1, q0 —b→ q0, q1 —a→ q1, q1 —b→ q0. F = {q1}.
- NFA: q0 —a,b→ q0, q0 —a→ q1. F = {q1}. Same idea as the NFA slides, which use "ends with 0": A loops on 0,1 and A —0→ B.

**(d) Even number of a's** has the regular expression (b | ab\*ab\*)\* and 2 states.
- q0 (final) —a→ q1 —a→ q0; both states loop on b.
- For an **odd** number of a's, use the same machine with F = {q1}. The minimum is 2 states.

**(e) Contains substring "ab"** has the regular expression (a|b)\*ab(a|b)\*.
- q0 —b→ q0, q0 —a→ q1
- q1 —a→ q1, q1 —b→ q2
- q2 (final) loops on a,b

**(f) Number of a's divisible by 3** {a³ⁿ} needs 3 states in a cycle q0→q1→q2→q0, with q0 final. For 3n+1 make q1 the final state instead. In general, {aᵏⁿ} needs k states.

**(g) Binary numbers divisible by 3.** Each state is the remainder so far, and q0 is final.
| | 0 | 1 |
|---|---|---|
| →\*q0 | q0 | q1 |
| q1 | q2 | q0 |
| q2 | q1 | q2 |

Trace for 1001 (= 9): q0→q1→q2→q1→q0, so ACCEPTED.

**(h) Minimal DFA for (0+1)\*10 (strings ending in "10") has 3 states.**

### NFA → DFA (subset construction) *(ToC notes)*
1. Write the NFA transition table.
2. The DFA start state is {q₀} (its ε-closure if there are ε-moves).
3. For each new set S and each symbol x: δ'(S, x) = ⋃ δ(q, x) for q ∈ S.
4. Repeat while new sets keep appearing.
5. Any set containing an NFA final state is a DFA final state.

**Worked example (NFA for strings ending in "ab").** The NFA is δ(q0,a) = {q0,q1}, δ(q0,b) = {q0}, δ(q1,b) = {q2}, F = {q2}.
| DFA state | a | b |
|---|---|---|
| → {q0} | {q0,q1} | {q0} |
| {q0,q1} | {q0,q1} | {q0,q2} |
| \*{q0,q2} | {q0,q1} | {q0} |

### DFA minimisation (the "table" method in the notes)
1. Remove unreachable states.
2. Split the states into non-final and final groups.
3. Merge rows that have identical transitions (equivalent states).
4. Combine the groups into the minimised DFA.

### Equivalence of two FAs
Start from the pair of start states and follow each input together. At every pair, both states must be final or both non-final. Continue until no new pairs appear.

### FSAs in NLP *(PPT1, PPT-2)*
- **Day/month FSA** (e.g. 11/3, 3/12). It is non-deterministic: after reading '2' it is in two states. It also **overgenerates**, for example accepting 37/00.
- A **recursive FSA** handles comma-separated lists of dates.
- **Spoken dialogue systems** use FSAs, e.g. a date-collection dialogue with 4 states: nothing known, month known, day known, both known.
- Applications of FA: lexical analysis in compilers, spam filters, voice assistants, network protocols, games AI, digital circuits, text processing.

---

## 4. Regular Expressions *(2_NLP_RE_Token, Regular_Expressions_in_NLP, ToC notes)*

| Pattern | Meaning | Example |
|---|---|---|
| `[wW]oodchuck` | one char from a set | Woodchuck, woodchuck |
| `[A-Z]` `[a-z]` `[0-9]` | ranges | |
| `[^A-Z]` | **negation**, only when ^ is first inside [] | |
| `[e^]` | e or ^ (literal) | |
| `.` | any single char except newline | `beg.n` matches begin, begun, beg3n |
| `?` | 0 or 1 | `colou?r` matches color, colour; `woodchucks?` |
| `*` | 0 or more (**Kleene \***) | `to*` matches t, to, too |
| `+` | 1 or more (**Kleene +**) | `to+` matches to, too |
| `{n}` `{n,}` `{n,m}` | counts | `\d{2,4}` |
| `\|` | disjunction | `groundhog\|woodchuck`; `a\|b\|c` = `[abc]` |
| `^` `$` | start and end anchors | `^[A-Z]`, `\.$` |
| `\b` | word boundary | `\bcat\b` doesn't match "category" |
| `\d \D \w \W \s \S` | digit, non-digit, word char [A-Za-z0-9_], non-word, whitespace, non-whitespace | |
| `( )` | group and capture | |
| `\1` | refers back to capture group 1 | |
| `(?: )` | non-capturing group | |
| `(?= )` / `(?! )` | lookahead / negative lookahead (zero-width) | `^(?!Volcano)[A-Za-z]+` |

Special characters lose their meaning inside `[]`.

- **Refining a pattern to find "the":** `the` misses "The" → `[tT]he` also matches "other" and "Theology" → `\W[tT]he\W`.
- **False positives** (matching what you shouldn't, e.g. "there") are reduced by increasing **precision/accuracy**.
- **False negatives** (missing what you should match, e.g. "The") are reduced by increasing **recall/coverage**. The two goals pull against each other.
- **Substitution:** `s/colour/color/`. With a capture group, `s/([0-9]+)/<\1>/` turns "the 35 boxes" into "the <35> boxes".
- `/the (.*)er they (.*), the \1er we \2/` matches "the faster they ran, the faster we ran".
- **ELIZA** (Joseph Weizenbaum, 1966) imitated a Rogerian psychotherapist using regex substitution, e.g. `s/.* I'M (depressed|sad) .*/I AM SORRY TO HEAR YOU ARE \1/`.
- **Python:** use raw strings `r"\d+"`.
  - `re.match` checks only at the start of the string.
  - `re.search` finds the first match anywhere.
  - `re.findall` returns all matches.
  - `re.sub` replaces matches.
  - `re.split` splits on the pattern.
- **Useful patterns:**
  - Email: `[\w.-]+@[\w.-]+\.\w+`
  - URL: `https?://\S+`
  - Hashtag: `#\w+`
  - Date: `\d{1,2}/\d{1,2}/\d{4}`
  - Phone: `\d{3}-\d{3}-\d{4}`
  - Strip HTML tags: `re.sub(r"<.*?>", "", text)` (the `?` makes it non-greedy)
  - Tokenizer: `\w+|[^\w\s]` turns "isn't" into isn ' t
- **Regex ↔ FA:** every regular expression describes a regular language, which some FA accepts. The usual route is regex → NFA → DFA.

---

## 5. Morphology *(PPT1, PPT-2, NLP-UNIT-1-notes)*

### Vocabulary
- **Morpheme:** the smallest meaning-bearing (or grammatical) unit.
- **Stem:** the core meaning unit, usually a **free morpheme** (can stand alone).
- **Affix:** a **bound morpheme** (cannot stand alone).
- **Compound:** more than one stem (book+shop+s, ice cream cone).
- Note that *sl-* in slither/slide/slip is **not** a morpheme.
- **Affix types, by frequency across languages:**
  - suffix: dog+s, truth+ful
  - prefix: un+wise
  - infix: abso-bloody-lutely
  - circumfix: German ge+kauf+t (not found in English)
- **Allomorphs:** different surface forms (**morphs**) of one underlying morpheme, e.g. plural -s / -es / -ren.
- **Irregular forms** must be listed: go/went/gone, good/better/best, sing/sang/sung. The sound-change pattern is no longer productive (ping → pinged, not "pang").
- **Lemma** vs **surface form / wordform:** cat and cats are the same lemma but different wordforms.

### Inflectional vs derivational (very likely question)
| | Inflectional | Derivational |
|---|---|---|
| Effect | adds grammatical info (tense, number, person, gender, case) | creates a **new word** or meaning |
| POS change | never | often (help → help**er**, V → N) |
| Result | a form of the *same* word (its **paradigm**) | a different lemma |
| English examples | -s (plural), -s (3sg), -ed (past), -ed/-en (past participle), -ing | un-, re-, anti-, dis-, mis-, -ation, -er, -ness, -able, -al, -ism, -ize |
| Productivity | applies to almost all stems | can stack: anti-anti-dis-establish-ment-arian-ism |

**English inflection**
- Verbs: walk / walks / walked / walked / walking (and go / goes / went / gone / going).
- Nouns: singular / plural.
- Pronouns inflect for person, number, gender and case.

**English derivation**
- Nominalisation: computer**ization**, kill**er**, fuzzi**ness**
- Negation: **un**do, **mis**take
- Adjectivisation: do**able**, nation**al**
- **Zero derivation** (no affix): tango, waltz used as verbs.

**Other word-formation processes:**
- **Inflection** makes forms of the same word.
- **Derivation:** grace → disgrace → disgraceful → disgracefully.
- **Compounding:** cream → ice cream → ice cream cone.
- All of these are **productive**: Google → Googler, to google, ungoogle…

### Language typology
- **Isolating:** few morphemes per word (Yoruba).
- **Synthetic:** many morphemes per word. There are two kinds:
  - **Agglutinative:** each affix carries one piece of information (Turkish, Tamil, Telugu). Example: *uygar-laş-tır-ama-dık-lar-ımız-dan-mış-sınız-casına* means "as if you are among those whom we could not civilize".
  - **Inflected / fusional:** one affix carries several pieces of information (French).
- **English is analytic.** It has little inflection and relies on word order and helper words. It is not isolating, because it has derivational morphology.

### Morphological ambiguity
- Ambiguous pieces: *paint* (N or V), *+s* (plural or 3sg).
- Structural ambiguity: *shorts* vs *short+s*.
- Bracketing of **unionised**. *un-ion* isn't a word, and un- attaches to verbs (meaning "reverse", untie) or adjectives (meaning "not", unwise). So the bracketing is **(un-((ion-ise)-ed))**.
- FSTs **cannot** represent this internal bracketing.

### Uses of morphological processing
- Building a full-form lexicon
- Stemming for IR
- Lemmatisation as a step before parsing
- Generation

It is **bidirectional**: party+PLURAL ↔ parties, sleep+PAST ↔ slept.

### Finite-state morphological parsing
- **Parsing:** surface form → lexical form. cats → `cat +N +PL`; disgracefully → `dis(NEG) grace+N +ADJ +ADV`.
- **Generation** goes the other way. The goal is to accept grace, graceful, disgracefully, ungracefully… and reject \*gracelyful, \*disungracefully (\* = ungrammatical).

### Building a finite-state lexicon (FSA morphotactics)
- Chain states as **prefix → stem → suffix**: grace, dis-grace, grace-ful, dis-grace-ful.
- Merge the variants with a **union** using ε-transitions to skip the optional prefix or suffix.
- **Derivational FSA:** noun1 (fossil) + -ize/-ise + -ation; adj1 (equal) + -ize + -able; noun2 (nation) + -al; and so on.
- **What the lexicon needs:**
  - affixes with their information: ed → PAST_VERB / PSP_VERB, s → PLURAL_NOUN
  - irregular forms: began → PAST_VERB begin; begun → PSP_VERB begin
  - stems with their syntactic categories

### Finite-State Transducers (FST)
- **FST = 7-tuple ⟨Q, Σ, Δ, q₀, F, δ, σ⟩**: an FSA plus an **output alphabet Δ** and an **output function σ: Q × Σ → Δ\***. Each arc carries an input:output pair, e.g. `s:s`, `ε:^`, `^:e`.
- An FST defines a **relation between two regular languages**, e.g. {⟨cats, cat+N+pl⟩, ⟨foxes, fox+N+pl⟩…}.
- **Operations:**
  - **Inversion T⁻¹** swaps input and output, which turns a parser into a generator.
  - **Composition / cascade T ∘ T′** chains L1×L2 with L2×L3 to give L1×L3.
- **Two-level cascade with an intermediate representation:**

  Lexical `fox +N +PL` → (T_lex) → intermediate `fox^s#` → (T_e-insert) → surface `foxes`

  Here `^` is the morpheme boundary and `#` is the word boundary.
- **Ambiguity:** "book" can be +N +sg or +V. This needs a **non-deterministic FST** and a scoring function or context ("I read a book" vs "I book flights"). Not every NFST can be determinised. Generation is usually unambiguous; analysis often isn't.
- **Limitations:**
  - FSTs assume the text is already tokenised, and they work one character pair per transition.
  - They can't model internal (bracketing) structure or condition on earlier transitions.
  - Compounds have hierarchical structure, (((ice cream) cone) bakery), which needs CFGs.
- **Practical use:** run one FST per spelling rule, then either compile them into one big FST or run them in parallel.

### Orthographic (spelling) rules
- **E-insertion:** fox+s → foxes, box+s → boxes. Rule: **ε → e / {s, x, z} ^ \_\_ s**. The mapping is left of the slash and the context is on the right. The same rule covers plurals and 3sg verbs.
- **E-deletion:** make+ing → making.
- From the textbook (J&M) you should also know:
  - **Consonant doubling:** beg+ing → begging
  - **Y-replacement:** try+s → tries, fly → flies
  - **K-insertion:** panic+ed → panicked

**E-insertion FST trace for "boxes"** *(PPT1)*:
- Reading b o x gives b o x.
- At **e** there are two paths: `e:^` (gives box^) or `e:e`.
- Then **s**, giving the accepted outputs **box^s** and boxes (or boxe^s via ε:^).
- Similarly cakes ↔ cake^s.

### Lexicon-free FST: the Porter stemmer
The Porter stemmer is a **cascade of rewrite rules** with no lexicon, where each pass's output feeds the next. Sample rules:
- ATIONAL → ATE (relational → relate)
- ING → ε if the stem contains a vowel (motoring → motor)
- SSES → SS (grasses → grass)

It produces non-words: "Thi wa not the map… Billi Bone… accur copi complet".

### Stemming vs lemmatisation (likely question)
| Stemming | Lemmatisation |
|---|---|
| crude chopping of affixes with rules | maps to the dictionary form (lemma) |
| output may not be a real word (studies → studi, happiness → happi) | always a real word (studies → study, **better → good**, am/is/are → be) |
| no context or POS | uses POS tagging plus a dictionary and morphological analysis |
| fast, simple; good for IR indexing | slower, more accurate; good for MT, QA, sentiment |
| Porter, Snowball, Lancaster | WordNet lemmatiser, morphological parser |

---

## 6. Text Processing and Tokenization: Lecture 20 *(2_NLP_RE_Token, PPT-3, Lecture notes 20, NLP-UNIT-1-notes, Stop_Words)*

### Words and corpora
- **Type:** an element of the vocabulary (a distinct word). **Token:** one occurrence of a type in running text.
  - "they lay back on the San Francisco grass and looked at the stars and their" has **15 tokens and 13 types**. The answers 14 tokens / 12 or 11 types are also accepted, depending on whether San Francisco counts as one word and the/their are merged.
- N = number of tokens, |V| = vocabulary size.
- **Heaps' / Herdan's law:** |V| = kN^β with 0.67 < β < 0.75. Vocabulary grows faster than √N.
- **Zipf's law:** a word's frequency is inversely proportional to its frequency rank, so there is a long tail of rare words.
- Corpora vary by language (about 7097 languages), variety (e.g. AAE "iont"), code-switching (Hindi/English), genre and author demographics. **Datasheets** document a corpus's motivation, situation, collection process and annotation.
- Disfluencies in speech: fragments ("main- mainly") and filled pauses ("uh").

### Text normalisation has 3 steps
1. Tokenise (segment) words
2. Normalise word formats
3. Segment sentences

### Tokenization types
| Type | Example | Pros | Cons |
|---|---|---|---|
| Word | "NLP is fun, isn't it?" → NLP \| is \| fun \| , \| is \| n't \| it \| ? | meaningful units | huge vocabulary, **OOV problem** (falls back to UNK) |
| Sentence | splits on . ? ! plus rules | needed for parsing, MT, summarisation | abbreviations (Dr., U.S.A.), decimals (3.14), ellipsis, quotes |
| Character / byte | N \| L \| P | tiny vocabulary, **no OOV**, sees spelling | very long sequences, loses word meaning |
| Subword | un \| happi \| ness, play \| ing | handles OOV, smaller vocabulary, captures morphology; used by BERT and GPT | needs training, less interpretable |
| N-gram | "NLP is fun" → bigrams: NLP is \| is fun | captures local context | sparsity, large feature space |

Character vs byte: 😀 is 1 character but 4 bytes; 地 is 1 character but 3 bytes.

**Segmentation levels:** paragraph → sentence → word → subword → character.

**Simple UNIX tokeniser** (Ken Church's "UNIX for Poets"):
```
tr -sc 'A-Za-z' '\n' < shakes.txt | sort | uniq -c
```
Add `tr 'A-Z' 'a-z'` first to fold case, and `| sort -n -r` to rank by frequency. Using this, "the" is the top word in Shakespeare.

**Tokenization issues**
- Don't blindly remove punctuation: m.p.h., Ph.D., AT&T, cap'n, $45.55, 01/02/06, URLs, #nlproc, emails.
- **Clitics** can't stand alone: 're in we're, French j' in j'ai, l' in l'honneur.
- **Multiword expressions:** New York, rock 'n' roll.
- **Languages without spaces** (Chinese, Japanese, Thai):
  - Chinese words are made of characters (*hanzi*), averaging 2.4 per word. 姚明进入总决赛 can be split into 3 words, 5 words or 7 characters. Chinese NLP often just treats each character as a token.
  - Thai and Japanese need neural sequence models for segmentation.

### Byte Pair Encoding (BPE): expect a numerical question
There are 3 subword algorithms:
- **BPE** (Sennrich 2016) merges the most frequent adjacent pair.
- **WordPiece** (Schuster & Nakajima 2012 / Wu 2016) merges the pair that maximises the likelihood of the data.
- **Unigram LM** (Kudo 2018) starts with a big vocabulary and prunes it.

Each has two parts: a **token learner** (trained on the corpus, builds the vocabulary) and a **token segmenter** (tokenises new text).

**Learner algorithm**
1. Vocabulary = all individual characters.
2. Repeat k times:
   - Find the most frequent adjacent pair (A, B).
   - Add AB to the vocabulary.
   - Replace every A B in the corpus with AB.

Add an end-of-word marker `_` first.

**Worked example (J&M)**
- Corpus: low×5, lowest×2, newer×6, wider×3, new×2.
- Initial vocabulary: `_ d e i l n o r s t w`.
- Merges in order:
  1. e r → **er** (count 9: newer 6 + wider 3)
  2. er \_ → **er\_** (9)
  3. n e → **ne** (8: newer 6 + new 2)
  4. ne w → **new**
  5. l o → **lo**
  6. lo w → **low**
  7. new er\_ → **newer\_**
  8. low \_ → **low\_**

**Segmenter:** apply the learned merges **greedily, in the order they were learned**. Test-set frequencies don't matter.
- "n e w e r \_" becomes one token, **newer\_**.
- "l o w e r \_" becomes **low er\_** (2 tokens).

**Second walkthrough (PPT-3):** corpus "Peter Piper picked a peck of pickled peppers".
- Merges: \_+p → \_p (4), c+k → ck (3), e+r → er (3), \_p+e → \_pe, \_p+i → \_pi, \_pi+ck → \_pick, …
- Segmenting "\_pier": \_ p i e r → \_p i e r → \_p i er → \_pi er.

**BPE properties**
- Its tokens include frequent words and frequent subwords, which are often morphemes (-est, -er). For example, *unlikeliest* = un- + likely + -est (3 morphemes).
- It is used by GPT, Llama, Gemini and DeepSeek, with vocabularies of roughly 100k–262k tokens.
- It was popularised by GPT-2.
- It is simple and deterministic, and with byte-level BPE nothing is OOV.

### More on tokenization from PPT-3 (good for short-answer questions)
- A tokenizer **encodes** text into token IDs, which are looked up in the embedding matrix; it also **decodes** IDs back to text. Tokenization is a preprocessing step, not part of the language model itself.
- **SentencePiece** is a *library*, not an algorithm. It implements BPE and Unigram, treats whitespace as a symbol, and is used by T5, Llama 2 and XLM-R.
- **SuperBPE** learns superword tokens that span spaces.
- **Spelling problem:** "strawberry" becomes ["str", "aw", "berry"], so the model can't count the r's. Subword models are bad at spelling words out, reversing strings and handling typos.
- **Glitch tokens:** when the tokenizer's training data doesn't match the LM's, some tokens are rare in LM training and end up with poorly trained embeddings. Example: **SolidGoldMagikarp**.
- **Multilinguality:**
  - About 7000 languages exist, but 40–45% of Common Crawl is English.
  - **High-resource vs low-resource** languages.
  - **Cross-lingual transfer** (e.g. XLM-R): fine-tune in English, then run zero-shot in other languages.
  - **Curse of multilinguality:** with fixed capacity, adding languages eventually hurts all of them. On XNLI, accuracy drops from 71.8% with 7 languages to 67.7% with 100.
  - **Subword fertility** = average number of tokens per word. Higher fertility means worse performance and higher API cost. Lengths differ by up to 15× across languages.
  - Character/byte models (CANINE, ByT5, MrT5) can be fairer.

### Word normalisation
- Standardise forms: U.S.A./USA, uh-huh/uhhuh, am/is/are → be.
- **Case folding:** lowercase everything for IR. Keep case for sentiment, MT and information extraction, because US vs us matters.
- **Lemmatisation** is done by **morphological parsing**: cats → cat + s; Spanish amaren → amar + 3PL + future subjunctive.
- **Stemming:** see section 5.

### Sentence segmentation (sentence splitting / SBD)
- "!" and "?" are mostly unambiguous, but **"." is ambiguous**: it can end a sentence, mark an abbreviation (Inc., Dr.), or appear in a number (.02%, 4.3).
- Algorithm: tokenise first, then use rules or ML to classify each period as part of a word or a sentence boundary. An **abbreviation dictionary** helps.
- NLTK `sent_tokenize` (Punkt) gives "NLP is a part of AI." / "Dr. Rao teaches NLP at 10.30 a.m." / "It is useful!"

### Stop words *(Stop_Words_in_NLP)*
- Stop words are high-frequency, low-information words (the, is, at, of, and).
  - Example: "the cat sat on the mat" → [cat, sat, mat].
- **Why remove them:** less noise, smaller vocabulary (the 3 reviews go from 24 to 14 unique words, about 40% fewer), faster processing, better search ("capital of France" → capital, france).
- **When NOT to remove them:**
  - Sentiment analysis: "not good" → "good" flips the meaning.
  - Idioms: "to be or not to be".
  - Machine translation.
  - Question answering ("who", "what").
- There is no universal list; lists depend on language and domain. Add domain words like "said", "reuters" for news.
- **Pipeline:** tokenise → lowercase → filter.
- **Tools:** NLTK `stopwords.words('english')`, spaCy `token.is_stop`, sklearn `ENGLISH_STOP_WORDS`.
- **Pitfalls:** removing negations, case-sensitivity bugs, using the wrong language's list.

---

## 7. Spelling errors and Minimum Edit Distance *(Lecture notes 20, NLP-UNIT-1-notes)*
These are in the "Lecture notes 20" PDF even though the handout lists them as Lecture 21. Study them anyway.

- **Non-word errors** aren't in the dictionary (recieve, mesage). Detect them by **dictionary lookup** or character n-gram analysis.
- **Real-word errors** are valid words used wrongly in context ("meat you tomorrow", "two/too", "there house", "write hand"). Dictionary lookup can't catch them; you need an **n-gram LM** or context.
- **Causes:** typos, phonetic similarity, keyboard proximity, OCR, lack of proficiency.
- **Correction methods:**
  - dictionary-based
  - **edit distance**
  - phonetic (Soundex, Metaphone: nite → night)
  - rule-based ("i before e except after c": recieve → receive)
  - statistical n-gram LM (their house vs there house)
  - ML (Naive Bayes, SVM, CRF)
  - neural (RNN, LSTM, Transformer)
- **Modern pipeline:** generate candidates with edit distance, then rank them with a language model or context.

### Minimum Edit Distance (Levenshtein)
Operations: insertion, deletion and substitution, each costing 1. Damerau also allows transposition.

**DP recurrence** (D is an (m+1) × (n+1) table, rows = source, columns = target):
```
D[i][0] = i      D[0][j] = j
D[i][j] = min( D[i-1][j] + 1,                       # deletion
               D[i][j-1] + 1,                       # insertion
               D[i-1][j-1] + (0 if xᵢ = yⱼ else 1) )  # substitution
```
The answer is in the bottom-right cell, D[m][n].

**cat → cut** (answer = 1):
```
      ε  c  u  t
  ε   0  1  2  3
  c   1  0  1  2
  a   2  1  1  2
  t   3  2  2  1
```

**Answers to memorise** (sub cost 1 / sub cost 2):
| Pair | Sub cost 1 | Sub cost 2 |
|---|---|---|
| kitten → sitting | **3** | 5 |
| intention → execution | **5** | 8 |
| cat → cut | 1 | 2 |
| speling → spelling | 1 | 1 |
| recieve → receive | 2 | 2 |

recieve → receive is 1 if transposition is allowed.

Note: the lecture-notes table for **"smple"** lists simple = 1, sample = 2, smile = 2. Under standard Levenshtein, **sample = 1** (insert a) and **smile = 1** (substitute p → i) as well. The tie is broken by word frequency or context, which is why simple is chosen. Write this reasoning if asked.

Applications of MED: spelling correction, OCR correction, speech recognition (**Word Error Rate**), plagiarism detection, DNA alignment, IR query matching.

---

## 8. Beyond Lecture 20 (low priority)
The UNIT-1 notes also cover **n-gram language models**:
- Unigram: P(w) = C(w)/N
- Bigram: P(wᵢ|wᵢ₋₁) = C(wᵢ₋₁wᵢ)/C(wᵢ₋₁). For the corpus "NLP is fun NLP is useful": P(is|NLP) = 2/2 = 1, P(fun|is) = 1/2.
- Trigram: P(wᵢ|wᵢ₋₂wᵢ₋₁)
- The **Markov assumption** underlies these, and **zero probability for unseen n-grams** motivates smoothing.

The same notes cover **Indian language processing**: 22 scheduled languages, rich morphology, free word order, many scripts, agglutinative (Tamil, Telugu), code-mixing, and tools such as Indic NLP Library, iNLTK, AI4Bharat and IndicBERT.

The handout puts these after Lecture 20. Skim them only if you have time left.

---

## 9. Likely questions: practise these
1. List and explain the levels/phases of NLP with an example for each.
2. Types of ambiguity with examples. Why is NLP hard?
3. Define the DFA and NFA 5-tuples. Why does an NFA's δ map to 2^Q? Compare DFA and NFA (table).
4. Design DFAs for: starts with 0; exactly length 2; ends with "ab"; even number of a's; binary numbers divisible by 3; a³ⁿ. Give the transition table and trace a string.
5. Convert a given NFA to a DFA (subset construction).
6. Write regexes for: the word "the" (no false positives); email; phone; date; tokenizing a sentence. Explain false positives vs false negatives and precision vs recall.
7. Define morpheme, stem, affix (4 types), free vs bound morphemes, allomorph.
8. Compare inflectional and derivational morphology with English examples.
9. Explain isolating, agglutinative, inflected and analytic languages. What type is English?
10. Define an FST (7-tuple). Explain the e-insertion rule `ε → e / {s,x,z}^ __ s` and trace boxes/foxes. Describe the lexical → intermediate → surface cascade, and inversion and composition.
11. Lexicon-free FST: the Porter stemmer and its rules. Stemming vs lemmatisation.
12. Types vs tokens (count them in a sentence). Heaps' law and Zipf's law.
13. Word, sentence, character and subword tokenization: pros, cons and challenges. Tokenizing Chinese.
14. **Run BPE** on a small corpus for k merges, then segment a test word.
15. Sentence segmentation: why "." is ambiguous and how to fix it.
16. Stop words: why and when (not) to remove them.
17. Non-word vs real-word errors. **Compute MED with a DP table.**
