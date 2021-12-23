# Cadmus PURA

## Conceptual Documentation

- [overview](overview.md)
- [models](models.md)

## Pura Graph

If you enable the graph for PURA, you should first ensure that preset nodes, mappings, and thesauri are seeded into the index database, e.g.: 

```bat
@echo off

xcopy \Projects\Cadmus\Pura\CadmusPura\Cadmus.Pura.Parts.Test\Assets\*.* c:\users\dfusi\desktop

.\cadmus-tool graph-add c:\users\dfusi\desktop\PresetNodes.json cadmus-pura .\plugins\Cadmus.Cli.Plugin.Pura\seed-profile.json repository-factory-provider.pura

.\cadmus-tool graph-add c:\users\dfusi\desktop\PresetMappings.json cadmus-pura .\plugins\Cadmus.Cli.Plugin.Pura\seed-profile.json repository-factory-provider.pura -t m

.\cadmus-tool graph-add c:\users\dfusi\desktop\PresetThesauri.json cadmus-pura .\plugins\Cadmus.Cli.Plugin.Pura\seed-profile.json repository-factory-provider.pura -t t

.\cadmus-tool graph-many cadmus-pura .\plugins\Cadmus.Cli.Plugin.Pura\seed-profile.json repository-factory-provider.pura
```

This uses the Cadmus CLI tool to seed data from [JSON files](https://github.com/vedph/cadmus-pura/tree/master/Cadmus.Pura.Parts.Test/Assets). Preset nodes are properties and classes; the preset thesaurus is the linguistic taxonomy, where each entry becomes a class node; preset mappings are used to:

- create a lemma node for each lemma item (`x:lemmata/TITLE`). This is the subject for a number of triples:

- `rdfs:comment DESCRIPTION`.
- `rdf:type x:lemma`.
- `rdf:type x:classes/CATEGORY`.
- `x:hasKeyword KEYWORD`.
- `x:hasIxKeyword INDEXKEYWORD`.

- create a form node for each lemma's form (`x:forms/EID`). This is the subject of:

- `kad:isInGroup ITEM`.
- `x:hasForm FORM`.
- `x:hasIxForm INDEXFORM`.
- `x:hasPOS POS`.
- `x:hasVariantForm VARIANT`.
- `x:hasIxVariantForm INDEXVARIANT`.

