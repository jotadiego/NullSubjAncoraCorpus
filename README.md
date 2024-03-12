# NullSubjAncoraCorpus
An adaptation of the AnCora annotated Spanish corpus tagged for null subject recognition.

## About the Corpus
This corpus is composed of 16623 sentences based on the Spanish version of the [AnCora](https://clic.ub.edu/corpus/es/ancora) (see Taulé et al., LREC 2008), an annotated multi-level corpus. The _NullSubjAncoraCorpus_ is intended as a specialized version of that corpus for null subject and impersonal verb recognition tasks.

The original AnCora corpus includes syntactic information which makes it possible to classify 94% of finite verbs into the following three scenarios:
- verbs featuring an **explicit subject**, as in "_**Los niños** comen pan._" (The children eat bread).
- **null subject** verbs as a result of pronoun dropping (which might be rewritten with an explicit subject), as in "_Comen pan_" (\[they\] eat bread).
- null subject verbs due to the verb being semantically **impersonal** (where explicit subject marking is not allowed in Spanish), as in "_Está lloviendo._" (\[it\] is raining).

The `NullSubjAncoraCorpus` extracts this information from the AnCora corpus and dissambiguates the remaining 6% of finite verbs (1323 verbs which were manually tagged). The modified dataset excludes sentences which lacked finite verbs.

The _NullSubjAncoraCorpus_ corpus was developed as part of a research on the usage of machine translation (MT) techniques applied to null subject and impersonal verb detection, as detailed in the paper _Null Subjects in Spanish as a Machine Translation Problem_ (Luis Chiruzzo and José Diego Suárez, 2023).

## Target audience
This corpus is intended for the training and evaluation of Machine Learning (ML) tasks focused on null subject and impersonal verbs.

## Dataset partition
The corpus is structured into three plaintext files: `train.txt` (training partition, 13489 sentences), `dev.txt` (development partition, 1567 sentences) and `test.txt` (testint partition, 1623). This follows the recommended dataset partition for the AnCora corpus proposed in the  CoNLL 2017 Shared Task (Zeman et al, 2017), which have since become a _de facto_ standard partition for the corpus. Usage of this partition for further ML projects is recommended for ease of comparison.

Statistics for the partition:

| Subset | File | Sentences | Tokens | Finite verbs | Explicit subject | Null subject (excluding impersonal) | Impersonal |
|--------|------|-----------|--------|--------------|------------------|-------------------------------------|------------|
| Training    | `train.txt` | 13489 | 414334 | 34925 | 25047 |  8835 | 1043 |
| Development | `dev.txt`   |  1567 |  48768 |  4018 |  2850 |  1062 |  106 |
| Testing     | `test.txt`  |  1623 |  49018 |  4162 |  2979 |  1062 |  121 |

## Dataset format
Each partition file is contains a number of sentences delimited by newline characters `\n` (please take notice that, depending on the OS, some text-processing utilities might convert newline sequences to `\r\n` instead).

Each sentence within _NullSubjAncoraCorpus_ contains a series of tokens (corresponding to lexical items or punctuation marks) including tags that indicate their part-of-speech (POS) category and, in the case of verbs, an indication of whether it is an non-finite form (**`i`**), a personal finite verb with an explicit subject (**`v`**), a personal finite verb with null subject (**`w`**) or a finite impersonal verb (**`y`**).

Each token is composed of a plaintext string (a word, a punctuation mark or a multi-word sequence annotated as a single lexical item in AnCora), the vertical bar character **`|`** and a single character indicating the POS or verb type class as per the following table:

| Tag     | Part of Speech | Verb type                   | Example                       |
|---------|----------------|-----------------------------|-------------------------------|
| **`a`** | Adjective      | N/A                         | `bueno\|a` (good)             |
| **`c`** | Conjunction    | N/A                         | `y\|c` (and)                  |
| **`d`** | Determiner     | N/A                         | `el\|d` (the)                 |
| **`f`** | Punctuation    | N/A                         | `,\|f` (a comma)              |
| **`i`** | **Verb**       | Non-finite                  | `cantar\|i` (to sing)         |
| **`j`** | Interjection   | N/A                         | `ay\|j` (ouch!)               |
| **`k`** | Date           | N/A                         | `mayo_del_2002\|k` (May 2002) |
| **`n`** | Noun           | N/A                         | `hombre\|n` (man)             |
| **`p`** | Pronoun        | N/A                         | `nos\|p` (us)                 |
| **`r`** | Adverb         | N/A                         | `bien\|r` (well)              |
| **`s`** | Preposition    | N/A                         | `en\|s` (in)                  |
| **`v`** | **Verb**       | **Explicit subject**        | `canta\|v` (sings, preceded or followed by its subject) |
| **`w`** | **Verb**       | **Null subject** (personal) | `canta\|w` (sings, null subject) |
| **`y`** | **Verb**       | **Impersonal**              | `llueve\|y` (it rains)        |
| **`z`** | Numeral        | N/A                         | `100\|z` (100)                |

### Multi-word tokens
The AnCora corpus annotates certain multi-word sequences as a single token, including multi-word personal names (_Juan Pérez_ as `juan_pérez`), dates (_7 de marzo_ as `7_de_marzo`) and expressions which are regarded as a single lexical item (_a pesar de que_ as `a_pesar_de_que`, _echaré de menos_ as `echaré_de_menos`, etc). In such cases, the corresponding token is formed by replacing any whitespace characters with the underscore character **`_`**.

Words resulting from lexical contractions such as _del_ (of the, mandatory contraction of  _de_, 'of', and _el_, 'the') or formed by the incorporation of clitics as in _cuéntame_ (tell me, from _cuenta_, 'tell', and the clitic suffix _-me_, 'to me') are also annotated as a single lexical item.

These behaviors are inherited in `NullSubjAncoraCorpus`.

### Modal and auxilliary verb constructions

In Spanish a verb might be preceded by auxiliary verbs which determine its tense and aspect as well as modal verbs such as _poder_ (to be able), _intentar_ (to try) or _querer_ (to want). In those cases, only the first verb within the sequence might appear in a finite form, with all other verbs appearing in non-finite forms (infinitives, participles or gerunds).

It should be noted that the syntactical notion of 'subject' only applies to finite verbforms in Spanish.

For instance, in the sentence _Ella podría estar trabajando_ (She could be working) there is a sequence of three verbs _podría_ (a modal verb), _estar_ (an auxiliary) and _trabajando_ (the main verb) yet only the first verb (_podría_) appears in a finite form. As such, the categorization of the subject type for the entire sequence is determined by the single initial finite verb, even though its semantics be given by main verb at the end of the sequence.

As a result, such a sentence would have been tagged as `ella|p podría|v estar|i trabajando|i`, with a **`v`** mark indicating an explicit subject (the pronoun _ella_) for the finite verb _podría_ while the non-finite forms _estar_ and _trabajando_ are tagged as non-finite verbs **`i`**.

### Subjects of conjunctions of verbs

As in English, a Spanish sentence might include a single subject followed by a conjunction involving two or more verbs as in _el niño lee y escribe_ (the boy reads and writes). There are two ways such a sentence might be analyzed:

- It could be posited that the explicit subject _el niño_ (the boy) applies to both verbs (_lee_, 'reads' and _escribe_, 'writes'). Under such an analysis, both verbs would be classified as bearing a explicit subject.
- It could be considered that the explicit subject only applies to the first verb within the conjunction (_el niño lee_, 'the boy reads') while the other verb features a null subject (_y () escribe_, 'and \[he\] writes').

The AnCora corpus consistently applies the second criterion, which the _NullSubjAncoraCorpus_ inherits. As such, _El niño lee y escribe_ would be transcribed as `el|d niño|n lee|v y|c escribe|w`, tagging _escribe_ as an instance of null subject.

### A note on impersonal verbs

This work employs a fairly broad  definition of impersonal verbs following the terminology set by the main Spanish language regulation body (Real Academia Española, RAE) in works such as _Nueva gramática básica de la lengua española_ (RAE, 2011). This covers both intrinsically impersonal verbs (such as meteorological verbs such as _llover', 'to rain') which occur almost exclusively in an impersonal usage as well as a range of other phenomena involving verbs which could also appear in personal usages, making this a highly contextual distinction.

This includes existential clauses (“hay”, “hubo”, corresponding to English “there is”, “there were”, etc.), clauses specifying a temporal frame (“era sábado”, “hace tres años”, corrseponding to English “it was Saturday”, “(it was) three years ago”), and sentences referring to a generic, unspecified actor such as “se trabaja mejor en equipo” (“it’s better to work on teams”) or “llaman a la puerta” (“(someone) is calling at the door”), among others.

## Annotation procedure

This corpus was constructed by extracting information from the existing [AnCora corpus](https://clic.ub.edu/corpus/es/ancora) that was relevant to the task at hand and then manually supplying subject categorization tags in cases where such information was insufficient for the proposed classification problem.

The Spanish-language AnCora corpus is composed of nearly 17 000 sentences taken from journalistic texts totalling around 500 000 words. Each sentence is annoated for multiple syntactic and semantic features, including a phrase structure grammatical analysis, lemma and part of speech (POS) identification, annotations for certain syntactic and semantic roles and inflectional data for nouns and verbs among others.

The first step in the constrction of the _NullSubjAncoraCorpus_ consisted in the extraction of lexemes along with POS tags. Verb inflection metadata allowed us to identify finite verbs (relevant for the task at hand) which would then be further classified as having an explicit subject (`v` tag), a null subject resulting from subject ellipsis (`w` tag) or presenting no subject due to appearing in an impersonal construction (`y` tag).

It was identified that certain features found within the AnCora corpus reliably showed the category for a majority of finite verbs in the corpus. Such features include:

- Typically, a noun phrase working as subject within a verb phrase will include the attribute `func='subj'` marking it as such. In those cases, the corresponding verb was automatically tagged as having a explicit subject (`v`).
- Most null-subject non-impersonal verb phrases contain a special 'elliptic subject noun phrase' node. When such a node was identified, the corresponding verb was automatically tagged as null-subject (`w`).
- Impersonal verb phrases conforming to a certain construction (_oraciones impersonales reflejas_) are annotated with the attribute `func='impers'`. In that such cases, the corresponding verb was automatically tagged as impersonal (`y`). This annotation in AnCora is not found for verbs appearing in other kinds of impersonal constructions.

This procedure sufficed to classify a substantial majority of the finite verbs within the corpus, amounting to 94%. It was found, however, that the remaining 6% (comprising 1324 verbs within all corpus partitions) lacked any markings within AnCora that could reliably indicate their category. Furthermore, we observed that these 'unclassified' verbs could in fact correspond to any of the proposed categories. As a result, we found it necessary to annotate the category of these verbs manually, based on their context within the sentence.

## Acknowledgments
This work is based on the existing [AnCora corpus](https://clic.ub.edu/corpus/es/ancora) (see Taulé et al., LREC 2008), all credit for the compilation and annotation of the original corpus goes to the corresponding authors.
