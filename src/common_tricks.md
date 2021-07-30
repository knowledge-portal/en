# Good Datapack Practices and Common Tricks
Written by Ellivers
## Structuring and Consistency

An organized structure to your data pack's folders and file names always helps you understand your code better and leads to less bugs.
Place files that have something in common in the same folder, and name them based on what their purpose is.

Consistency in naming stuff like tags, files, and scores can help keep you sane. Always have a clear structure in place, such as prefixing tag and objective names with your namespace (`namespace.name`) and giving tags different categories (`namespace.entity.name`, `namespace.block.name`).

If you are unsure of how a good structure could look, you could take a look at some vanilla assets, such as the lang file or the built-in data pack, as well as get ideas from looking at data packs that other people have made.

## Commenting

Comments are very important and a huge help when working with functions, as well as any code in general. Ideally, every function should have at least one line of comments in it. A comment is any line that starts with a `#`.

Here is how I like to structure my comments:

```mcfunction
# Called by namespace:path/to/function
# Short description of what this function does
# More describing if needed

# Description of what these commands do
command 1
command 2

# Description of what these commands do
command 3
command 4
```

## Performance

Functions in data packs can easily lead to bad performance. Here is a list of some things to be aware of:

* **Multiple Entity Selectors**

  **@e** is an essential selector as it is able to select all entities, but it is also a huge reason for command lag.
  Therefore, it should be avoided in large quantities as much as possible. If you execute a function as an entity, you can then use the `@s` selector to refer to that entity. `@s` is the selector you should be using the most.
  
  Example of splitting an `@e` selector into multiple `@s`:
  
  function1:
  ```mcfunction
  # Executes as all pigs that have the "namespace.mytag" tag
  execute as @e[type=minecraft:pig,tag=namespace.mytag] run function namespace:function2
  ```
  
  function2:
  ```mcfunction
  # Called by namespace:function1
  # Runs commands as all pigs that have the "namespace.mytag" tag
  
  say My name is @s
  kill @s
  say ouch
  ```
  
  **@a** is another selector that selects multiple entities.
  It only selects players, and there are usually not nearly as many players as general entities in a world, so it does not contribute to lag as much as `@e`. However, if you have a lot of `@a` selectors you should still be splitting them into `@s`, the same way you do with `@e`.
  
* **NBT Checks**
  
  Checking for NBT data hurts performance quite a lot, and is something that you should avoid as much as possible.
  
  Example of a bad NBT check:
  ```mcfunction
  # Check if the executing player is holding a feather
  execute if entity @s[nbt={SelectedItem:{id:"minecraft:feather"}}]
  ```
  
  **Predicates**
  
  Predicates are JSON files that can be used in combination with commands to check for all kinds of things.
  
  Often, NBT checks can be replaced with predicates to improve performance. This is the case for the command shown above.
  
  Here is what a predicate replacing the NBT check above would look like:
  
  ```json
  {
    "condition": "minecraft:entity_properties",
    "entity": "this",
    "predicate": {
        "equipment": {
            "mainhand": {
                "item": "minecraft:feather"
            }
        }
    }
  }
  ```
  
  It could replace the check in the command like this:
  ```mcfunction
  # Check if the executing player is holding a feather
  execute if predicate namespace:path/to/predicate
  ```
  
  However, predicates will not help if you only use them to check using the `nbt` key.
  This is an example of something that does *not* help performance:
  ```
  {
    "condition": "minecraft:entity_properties",
    "entity": "this",
    "predicate": {
        "nbt": "{OnGround:1b}"
    }
  }
  ```
  
  Notice how the predicate above only checks for the property `OnGround:1b`, and does not use any keys other than `nbt`.
  In this case, it is better to simply check the NBT in the command directly:
  ```mcfunction
  # Check if the executing entity is on the ground
  execute if entity @s[nbt={OnGround:1b}]
  ```
  
  **Storage**
  
  Storage is global NBT that can contain anything you want and that can be accessed from anywhere. It is the most performance-friendly option for storing NBT, but is not specific to any entity or block.
  
  If you need to either modify NBT several times, or check it multiple times while predicates are not applicable, using storage can be a lot more efficient.
  
  Here is an example:
  ```mcfunction
  # Set the X, Y, and Z coordinates of the executing entity's position
  
  data modify storage namespace:name Key set from entity @s Pos
  data modify storage namespace:name Key[0] set value 3.0d
  data modify storage namespace:name Key[1] set value 7.0d
  data modify storage namespace:name Key[2] set value 85.0d
  data modify entity @s Pos set from storage namespace:name Key
  ```
  
## Advancements

When you need to detect something a player does, like placing a block, first see if that is something an advancement can detect, instead of creating a scoreboard objective for it or perhaps running a check for it every tick. Advancements are very useful and powerful, as they do not cause any lag (with the exception of the `tick` advancement trigger) and can be used to run a function when something happens.

## Fakeplayers

"Fakeplayers" are an extremely useful trick used in order to avoid creating big amounts of scoreboard objectives.
Simply give a score to a nonexistant player, and you are able to make way more variables using only one objective and no entities.

Here is an example of what you can do using fakeplayers:
```mcfunction
# Do some math

scoreboard players set #five objective 5

scoreboard players operation #variable1 objective *= #five objective
scoreboard players operation #variable2 objective += #variable1 objective
```

Notice how the fakeplayer names are prefixed with `#`. This is not needed, but it makes sure that the fakeplayer's name can't happen to be the same as a real player's name, since real players can't have `#` in their name.
Fakeplayers are global and not applicable if you need to tie a score to an entity.
