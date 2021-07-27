# Custom Recipes with NBT

### TODO -- Note; There’s also an advanced method to prevent give all recipe packs from breaking your recipes

The knowledge book method is a technique used to craft items with custom nbt tags. 
NOTE: this method still doesn’t allow for nbt checks in the recipe items, so you 
can only get items with custom nbt as a crafting output, not input. That said, 
let’s get started. In this case I'll show you how to craft an emerald sword as an 
example.

Go to https://crafting.thedestruc7i0n.ca/ and create your recipe. It’s important 
that you place the knowledge book item in the result slot. In our case, since we’re 
making an emerald sword the output will be something like this, and it's gonna be 
called emerald_sword:
```json
{
    "type": "minecraft:crafting_shaped",
    "pattern": [
        "#",
        "#",
        "/"
    ],
    "key": {
        "#": {
            "item": "minecraft:emerald"
        },
        "/": {
            "item": "minecraft:stick"
        }
    },
    "result": {
        "item": "minecraft:knowledge_book",
        "count": 1
    }
}
```

you can download the .json file for this and put in in your datapack’s recipe folder.
Then, you need to create a loot table that gives the player the item WITH NBT that 
you want. Both can be generated here: https://misode.github.io/

name this json file `emerald_sword.json`
```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "minecraft:item",
          "name": "minecraft:diamond_sword",
          "functions": [
            {
              "function": "minecraft:set_name",
              "entity": "this",
              "name": {
                "text": "100% Real Emerald sword",
                "italic": false
              }
            }
          ]
        }
      ]
    }
  ]
}
```
This loot table will be rewarded to the player when they unlock the advancement 
that triggers when the recipe is unlocked and also rewards the function that 
clears the knowledge book and revokes the advancement itself. So, we need to 
create the following advancements:

This first advancement will be called root, it's function is to prevent 
"unlock all recipe packs" from giving you the items randomly
```
{
  "criteria": {
    "impossible": {
      "trigger": "minecraft:impossible"
    }
  },
  "requirements": [
    [
      "impossible"
    ]
  ]
}

and emerald_sword
{
    "parent": "kp:recipes/nbt/root",
  "rewards": {
    "function": "kp:nbt_recipe",
    "loot": ["kp:items/emerald_sword"]
  },
  "criteria": {
    "has_the_recipe": {
      "trigger": "minecraft:recipe_unlocked",
      "conditions": {
        "recipe": "kp:nbt/emerald_sword"
      }
    }
  }
}
```

Lastly, we need to create the function that resets everything and clears the 
knowledge book from the player
`nbt_recipe.mcfunction`:
```mcfunction
advancement revoke @s through kp:recipes/nbt/root
clear @s knowledge_book
stopsound @s * minecraft:entity.item.pickup
recipe take @s kp:nbt/emerald_sword
```

NOTE: you don't have to create multiple of these functions for every recipe you
add, you just need to add the command that takes the recipe recipe take @s kp:nbt/...

### TODO --  add download for pack that asdru linked
