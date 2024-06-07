# Word Glossary - Baba Is You Mod
A utility Baba Is You mod that adds a glossary page for viewing descriptions of different texts.

This mod works by adding a new button in the object palette menu to access the Word Glossary. Glossary entries are added using lua code that interfaces with an [API provided by the mod](api.md). This allows different levelpacks to have a different set of glossary entries depending on the mods installed in the levelpack itself.

<img src="word_glossary_demo.gif" alt="drawing" width="800"/>

## Installation
### [Download the mod in the Releases Section!](https://github.com/PlasmaFlare/baba-word-glossary/releases)

### How to install

Place `!!word_glossary.lua` into either one of these locations:

- `<Baba game directory>\Data\Lua` if you want to enable the mod globally. Useful for switching between levelpacks that have custom words with their own glossary entries.
- `<Baba game directory>\Data\Worlds\<levelpack folder>\Lua` if you want the Word Glossary Mod to only activate when in a levelpack.

## Adding entries to the Word Glossary

If you are a Baba modder with your own custom words that you want to register, check out the API documentation below:

### [API Documentation Link](api.md)

## Changelog
- Version 1 (6/7/24)
  - Initial Release
