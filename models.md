# PURA Models

Legenda: in the following documentation, each data model is represented by a named object with any number of properties. Each property can either be a scalar ("simple") property, like a number, a text (string), a boolean (=yes/no) value, etc. Properties can freely nest.

The following conventions apply:

- required properties are marked by an asterisk.
- each property has a data type expressed in `()`.
- properties representing a list have their data type suffixed with `[]`.
- text (string) properties marked with `thesaurus` have their value picked from a predefined taxonomy.
- text (string) properties marked with MD represent a text with basic formatting (e.g. bold, italic, etc.), rather than a plain text with no formatting at all (which is the default).

## Items

The structuration level of this project has been lowered to a minimum to fit its requirements. So, essentially the highest levels of structure survive in the lemma, the manuscript descriptions, and in the categories and keywords scattered across the whole database. Categories belong to a well-defined and hierarchical taxonomy. This allows you to eventually go back from the more specific category to the more generic one, widening or narrowing the search by going up or down the "tree" that represents this hierarchy.

Also, lemma, categories and keywords are ready for [semantic projection](https://github.com/vedph/cadmus_doc/blob/master/core/core.graph.md), and this can be the basis for connecting to linguistic ontologies, but also to eventually provide an index ready to be queried by a presentation client.

The current [mappings](https://github.com/vedph/cadmus_pura/blob/master/Cadmus.Pura.Parts.Test/Assets/PresetMappings.json) to the semantic graph project each lemma with a number of properties, modeled as RDF triples, including their categories and keywords. In turn, the linguistic taxonomy can be projected in the graph as a set of hierarchically ordered classes, thus allowing inferencing.

In this project there are 3 types of items:

- **lemmata**: a sort of specialized dictionary. It mostly includes words, and occasionally syntactic constructs, either generic or involving a specific word. Each lemma has an ID equal to a normalized form of it (uppercase letters only, no diacritics: e.g. `ΗΣΥΧΙΟΣ`).
- **text with layers**: Greek text passages cited in the discussion. Each of these texts has as _group ID_ the lemma ID of the related lemma (e.g. `ΗΣΥΧΙΟΣ`). Its title is the same lemma ID, plus a suffix like `-A2`, `-B1`, etc. corresponding to conventional numberings used in the commentary to address each specific text (e.g. `ΗΣΥΧΙΟΣ-A1`).
- **manuscripts**: codicological descriptions.

Each of these items has a number of parts:

- **lemma**

  - `WordFormsPart` (PURA): form(s) and classification.
  - `CategoriesPart`: categories from a hierarchical set.
  - `IndexKeywordsPart`: free keywords.
  - `NotePart` (role=`d`): unstructured commentary with role=D.
  - `NotePart` (role=`e`): unstructured commentary with role=E.
  - `NotePart` (role=`biblio`): a non-structured bibliography as a text. This is preferred to a structured bibliography as the project does not requires it, and is going to directly import pre-existing data here.

- **text**

  - `TokenTextPart`: text with its citation.
  - `NotePart` (role `transl`): translation.
  - `NotePart` (role `apparatus`): preferred to a structured apparatus layer as these are just short notes eventually accompanying the text.
  - `CategoriesPart`: as above, but related to this specific text only.
  - `IndexKeywordsPart`: as above, but related to this specific text only.
  
- **manuscript**

  - `MsSignaturesPart` (TGR)
  - `MsPlacesPart` (TGR)
  - `HistoricalDatePart`
  - `MsContentsPart` (TGR)
  - `MsUnitsPart` (TGR)
  - `MsScriptsPart` (TGR)
  - `MsOrnamentsPart` (TGR)
  - `MsHistoryPart` (TGR)
  - `BibliographyPart`: [structured bibliography](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#bibliography) for manuscripts.
  - `NotePart`

Given that the project was born document-based and wants to preserve this structure, the picture below shows the essential mapping between the original documents and the target structure:

![mapping](./images/mapping.png)

At the left you can see the original layout of data in their Word documents. Each Word document there was a complete discussion of a topic connected to a lemma. As such it had a header, and three thematic sections A-B-C reporting all the texts being part of the discussion.

Then, the lemma and its texts are followed by a detailed commentary, articulated in other thematic sections D-E and a final, non-structured bibliography.

As for manuscripts instead, the project chose to directly import the models from another Cadmus-based project (TGR), thus following its higher structuration level. Another even more detailed modeling is available from another Cadmus-based project, but here TGR was preferred because it appeared simpler and less specialized.

In order to give this layout a minimal structure, apart from manuscripts these materials have been split into two big areas: lemma and texts. the following mapping has been implemented:

(1) the lemma header with all its details about it is a lemma item ("record"), including:

- `WordFormPart` with the details about the lemma's form(s).
- 3 `NotePart`'s, i.e. text parts dedicated to the different sections of the original document: D, E, and unstructured bibliography.

This represents the lemma and its full commentary.

(2) the texts items: these are all the texts connected to the lemma record. Each text is a record on its own, because this allows to add further data to it. Included parts are:

- the text content, which is a short text passage, stored in `TokenTextPart`. Currently the project stores only plain text, without metatextual layers.
- its translation, stored in a generic `NotePart`.
- notes about the text constitution, where relevant; this too is a generic text (`NotePart`) rather than an apparatus layer.

Finally, categories (`CategoriesPart`) and keywords (`IndexKeywordsPart`) can be included in both lemma and text items.

## Lemma Item

Each lemma item has a title starting with its first lemma LID (e.g. `ΗΣΥΧΙΟΣ`) followed by space and any other heading text.

- `WordFormsPart`\*:
  - `forms` (`WordForm[]`):
    - `lid` (`string`): lexicographic ID (LID for short). In the case of this project, it's just the uppercase lemma without diacritics.
    - `prelemma` (`string`)
    - `lemma`\* (`string`)
    - `postlemma` (`string`)
    - `homograph` (`integer`)
    - `pos`\* (`string`, thesaurus): part of speech.
    - `note` (`string`): optional short note.
    - `variants` (`VariantForm[]`): variants or other inflected forms related to this form:
      - `value`\* (`string`)
      - `pos`\* (`string`, thesaurus): part of speech.
      - `tag` (`string`)

Occasionally, the lemma can also consist in a syntactic pattern. In this case, `pos` will have a special value encoding the pattern, and lemma will be represented by a string where each tag is an uppercase abbreviation prefixed with `/`, and can be applied to specific words, or syntactic constituents, included between square brackets. For instance, we might have `[ἔχω/V /ADV]/VP` representing a verbal phrase (`/VP`) built from the verb (`/V`) `ἔχω` plus an unspecified adverb (`/ADV`) to mean a state.

- `CategoriesPart`: [categories](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#categories) assigned to the lemma. These will draw data from a hierarchical taxonomy.

- `IndexKeywordsPart`: [keywords](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#index-keywords) eventually assigned to the lemma.

- [NotePart](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#note). Roles: `d`, `e`, `biblio`.

In `NotePart` with role D/E the MD text also includes a minimalist tagging for:

- references to passages in the context of the same item: a capital letter + `.` + digit(s) like `B.1`.
- references to ancient authors (`{ra:...}`)
- references to modern authors (`{rm:...}`)
- references to text passages (`{rt:...}`)
- manuscripts (`{m:...}`)
- literals (`{l:...}`)

These minimal tags can be introduced in texts created with any word processor, as this is a content creation scenario to be included.

## Text Item

These are simple passages extracted from various works, and referenced in the discussions. Each item has its group ID equal to the work (e.g. `antiatt`), and its title equal to the LID of the main lemma (e.g. `ΗΣΥΧΙΟΣ` from `ἡσύχιος`), followed by a dash and the passage local ID (e.g. `-A1`).

- `TokenTextPart`\*: [text passage](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#token-text).

- [NotePart](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#note): for translation, with `role`=`transl`; for apparatus text, with `role`=`app`.

- `CategoriesPart`: [categories](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#categories) assigned to the text passage. These will draw data from a hierarchical taxonomy.

- `IndexKeywordsPart`: [keywords](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#index-keywords) eventually assigned to the lemma.

## Manuscript Item

- `BibliographyPart`: general [bibliography](https://github.com/vedph/cadmus_doc/blob/master/web/help/general-parts.md#bibliography).

See [TGR](https://github.com/vedph/cadmus_tgr_doc/blob/master/models.md).
