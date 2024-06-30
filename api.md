
# Adding custom words to the Word Glossary
The mod exposes a basic API for other mods to add their own entries with the Word Glossary. The functions and global variables in the API will only be present if the game detects the word glossary mod installed (a side effect of putting them in `keys`, which is a global table in base-game that gets re-initialized when reloading mods).

For a complete example of the API being used, view the [Plasma's Modpack implementation](https://github.com/PlasmaFlare/plasma-baba-mods/blob/master/Lua/glossary.lua).

## Global Variables

### `keys.IS_WORD_GLOSSARY_PRESENT`
`true` if the Word Glossary Mod is initialized. `nil` if not.

This can be useful if you want your mod to be compatible with the Word Glossary Mod without having to make a separate version of your mod. Just surround your code that calls functions in `keys.WORD_GLOSSARY_FUNCS` with:
```lua
if keys.IS_WORD_GLOSSARY_PRESENT then
    --- <code that calls functions in keys.WORD_GLOSSARY_FUNCS>
end
```

## `keys.WORD_GLOSSARY_VERSION`
Number for the word glossary version.

## Functions

### `keys.WORD_GLOSSARY_FUNCS.add_entries_to_word_glossary`

```lua
keys.WORD_GLOSSARY_FUNCS.add_entries_to_word_glossary(entries)
```
Adds a list of word entries to the word glossary in the order presented in `entries`. 

Each word entry is a table in this format:
```lua
{
    name: string
    description: string
    author: string (optional)
    group: string (optional)
    thumbnail_obj: string (optional)
    text_type: int (optional)
    display_name: string (optional) 
    display_sprites: list of strings (optional)
}
```

- `name` - string: the name of the word entry. This can be used as a default for most of the optional fields in the word entry. 
- `description` - string: description what the text or object does.
  - This supports:
    - Newlines
    - Color codes (Ex: `$3,2`).
    - Control Icons (Ex: `@gamepad_editor_rotate`)
    - Lang texts (Ex: `#main_custom`)
- `author` - string (optional): The author to credit for this text. (See [register_author](#keysword_glossary_funcsregister_author)).
  - If the author isn't registered via "register_author", it would just show the text itself
  - If left blank, the author will be shown as "N/A"
- `group` - string (optional): If this text is part of a modpack or some other group of texts, put the name of the group here.
  - If left blank, it won't show any group.
- `thumbnail_obj` - string (optional): the object to display as a thumbnail for the word entry. (Ex: `text_stable`)
  - If left blank, this would be set to "text_`name`". So if `name` = "cut" then `thumbnail_obj` = "text_cut".
- `text_type` - int (optional): the text type of this word entry.
  - If left empty, this would be set to the text_type of the `thumbnail_obj`
- `display_name` - string (optional): what to display as the title of this word entry.
  - If left empty, this would be set to `name`
  - If the text type isn't from the base game, it will show "Other".  (See [register_custom_text_type](#keysword_glossary_funcsregister_custom_text_type)).
- `display_sprites` - list of strings (optional): a table representing a list of objects to display on the left side when viewing the word entry in-game. You can show up to a maximum of 16 objects. Any extra objects will be ignored
  - If left empty, this would be set to `{thumbnail_obj}`, or simply the object shown in the thumbnail.

#### Examples:
```lua
keys.WORD_GLOSSARY_FUNCS.add_entries_to_word_glossary({

    -- Basic example. This adds text_guard to the word glossary. Thumbnails, text type, display sprites and display names are taken care of.
    {
        name = "guard",
        author = "PlasmaFlare",
        description = "Allows objects to sacrifice themselves in order to save another object from being destroyed.",
    },


    -- Another basic example but with a more detailed description.
    -- The [[]] is another way to define a string. But it also allows newlines.
    -- The weird indentation for the description is because [[]] counts every character within the brackets, including newlines and tabs/spaces, into its value.
    -- Notice how the description also uses color codes.
    {
        name = "cut",
        author = "PlasmaFlare",
        description = 
[[Gives an object the ability to split a text block into individual letters.

- The effect happens when the $5,3CUT$0,3 object walks into a text block.

- Letters cannot be cut.

- When a text block is cut, its letters are extracted out in the direction of the cut. Letter extraction can stop early at the first solid object encountered.

- If a text block gets cut from occupying the same space as a "CUT" object, letters are extracted in the direction the text block is facing.
]],
    },


    -- A more customized word entry for directional you. This has a custom thumbnail, a custom title (display_name), and 4 display sprites for showing the 4 different directions of directional you when viewing the word entry in-game.
    {
        name = "arrow_you",
        thumbnail_obj = "text_youright",
        display_name = "directional you",
        author = "PlasmaFlare",
        display_sprites = {"text_youup", "text_youright", "text_youleft", "text_youdown"}
        description = [[Variant of "YOU" that allows the player to move the object the direction of the arrow. Objects that are directional YOU can still trigger "WIN", and will be destroyed on "DEFEAT" object, like normal "YOU" objects.]],
    },
})
```


### `keys.WORD_GLOSSARY_FUNCS.register_author`

```lua
keys.WORD_GLOSSARY_FUNCS.register_author(author, color, custom_display)
```

----------------------------------------------------------------------------

Registers the word author and how you want to display their name in-game when viewing a word entry with the author.


- `author: string` - The author of the word to register. This will be the string to refer to the author when adding word entries.
- `color: table (optional)` - X and Y coordinates in the color palette in which to color the author name.
    - **Default:** `{0, 3}` (white)
- `custom_display: string (optional)` - If this is not nil, it overrides the default rendering of the author name with a custom format. You can take advantage of this to use color codes, or display a nickname or extra decorations for the author.
  - Ex: `$1,4plasma$3,4flare`
  - If `custom_display` is not nil, the `color` parameter of this function will be ignored

#### Example
```lua
keys.WORD_GLOSSARY_FUNCS.register_author("PlasmaFlare", {4,4} )
```

----------------------------------------------------------------------------

### `keys.WORD_GLOSSARY_FUNCS.register_custom_text_type`

```lua
keys.WORD_GLOSSARY_FUNCS.register_custom_text_type(text_type, display)
```

Registers a custom text type. Any word entry with its text type equal to `text_type` will show the string contained in `display`. **Note:** This can override the vanilla text types.

- `text_type: int` - The text type in number form.
- `display: string` - the string to display when selecting a word entry with the corresponding text type.

#### Example
```lua
-- Register texts of type 11 to show "Filler"
keys.WORD_GLOSSARY_FUNCS.register_custom_text_type(11, "Filler")
```