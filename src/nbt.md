# NBT
### What is NBT?
A NBT (Named Binary Tag) is a format used by minecraft used to record data, like tool and armour durability, saved structures, fireworks, and banners.

### Comparing Arbitrary NBT Values
How can be used to test if any given NBT values are equal?

The idea is to attempt to write both values to the same location, where the second operation will result in either a success or a failure depending on whether they are equal.
The explanation as to why this works is that if the values are equal, the second operation attempts to write a value that already exists in the destination, and therefore not change anything and give a negative success.

An example function that compares the NBT values at `storage kp:temp value.A` and `storage kp:temp value.B`, and stores the result in the `not_equal kp.temp` fake player:
```mcfunction
# Create 'kp.temp' objective
scoreboard objectives add kp.temp dummy
# Prepare storage values
data modify storage kp:temp value.A set value "string 1"
data modify storage kp:temp value.B set value "string 2"

# Copy both values to 'storage kp:temp compare', store success of second operation
data modify storage kp:temp compare set from storage kp:temp value.A
execute store success score not_equal kp.temp run data modify storage kp:temp compare set from storage kp:temp value.B

# Print result
execute if score not_equal kp.temp matches 0 run tellraw @a "The storage values are equal."
execute if score not_equal kp.temp matches 1 run tellraw @a "The storage values are different."
```
