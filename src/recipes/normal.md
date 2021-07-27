# Custom Recipes Via JSON

How to add custom recipes using datapacks:
There are two types of crafting when it comes to custom recipes, shaped, and shapeless, shaped requires a specific pattern of items to be put into the crafting slots, and shapeless only requires the items
Heres an example for shaped crafting, creating a powered rail;
```json
{
"type": "minecraft_crafting_shaped",
"pattern": [
"x x"
"x#x"
"x!x"
],
"key":{
"x":{
"item": "minecraft:gold"
},
"#":{
"item": "minecraft:stick"
},
"!":{
"item": "minecraft_redstone"
}
},
"result": {
"count": 6 
"item": "minecraft:powered_rail"
} 
}
```

Type: type of recipe, smelting, crafting:shapeless, crafting:special, etc
Pattern:  the pattern for the recipe in a crafting table
Key: explains what characters mean for each item (ie # = golden carrot)
Item: an item specified
Result: the resulting item from a recipe
Count: how many items you'll get from a said recipe, default is one
However, if you wanted the recipe to be shapeless, you could write it like this
```json
{
"type": "minecraft:shapeless" 
"ingredients": {
"item": "minecraft:gold",
"item": "minecraft:stick",
"item": "minecraft:redstone"
}
"result": {
"count": 6 
"item": "minecraft:powered_rail" 
}
}
```

There's also another category called special crafting, which deals with things like banners, leather armour dye, and fireworks, mainly things with nbt tags (check #nbt for more info). 

For smelting, smoking, and blasting, the format is the same, you just go to the type tag  and switch out the words;
`{"type": "minecraft:smelting"}`
 
or
`{"type": "minecraft:smoking"}`

Let's make an example. This recipe will make it so if you use a blast furnace to smelt a gold ingot, it'll turn into 9 nuggets
```json
{
"type": "minecraft:blasting"
"ingredients":
{
"item": "minecraft:coal"
"item": "minecraft:gold_ingot"
}
"result"{
"item":"minecraft:gold_nugget"
"count": 9
}
}
```

 Ingredients: a list of items needed in a recipe

For smithing, you can create items using the smithing table, such as a diamond block and one netherite ingot would turn into a netherite block, lets give it a try.
```json
{
"type": "minecraft:smithing"
"base": {
"item":"minecraft:diamond_block"
}
"addition": {
"item":"minecraft:netherite_ingot"
}
"result": {
"item":"minecraft:netherite_block"
}
}
```

Base: the first item in the smithing table
Addition: the second item in a smithing table
