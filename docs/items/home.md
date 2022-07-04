
# Modding Items
---
Items are a core part of Necesse, and are one of the most commonly modded aspects in video games. Modding items allows you to add, remove, or modify existing items within the game to 
create a unique experience.

This guide will go over the basics of how to mod items within the game. Subdirectories are used to provide item-specific information, or more advanced details that may be unneccesary for the average user.


### Creating items
---

There are two primary ways to create a new item in `Necesse`. 

<!-- panels:start -->
<!-- div:left-panel -->
The **first** is to create a new class derived from an existing `Item` class where you can add custom functionality by **overriding** existing methods. 

Different `Item` classes may provide you with access to override different methods, this is listed in the **item specific sections**.

For more examples you can check the [**official java example repo**](https://github.com/DrFair/ExampleMod) or [**unofficial kotlin mod example**](https://github.com/DraSouls/Necesse-KtExampleMod)
<!-- div:right-panel -->
<!-- tabs:start -->
#### **Java**

```java
// Base item implementation
public class MyItem extends Item {
    public MyItem(Item.Rarity itemRarity) {
        int stackSize = 12;
        super(stackSize);
        rarity = itemRarity;
    }
}

// Base sword implementation
public class MySwordItem extends CustomSwordToolItem {
    public MySwordItem(int dmg) {
        super(Rarity.UNCOMMON,
            300 /* animSpeed */, dmg, 120 /* range */, 100 /* knockback */, 400 /* enchant cost */)
    }
}
```

#### **Kotlin**

```kt
// Base item impplementation
class MyItem(Item.Rarity itemRarity) : Item(12 /* stack size */) {
    init { rarity = itemRarity }
} 

// Base sword implementation
class MySwordItem(int dmg) : 
    CustomSwordToolItem(Rarity.UNCOMMON, 300 /* animSpeed */, dmg, 120 /* range */, 100 /* knockback */, 400 /* enchant cost */)
```
<!-- tabs:end -->
<!-- panels:end -->
The **second** is to **register** an item using an existing `Item` class, and supplying custom data through the constructor. This is far less extensible, but allows you to create simple items very quickly. For most people, this option may be sufficient. 

For more information on registering items, see [**Registering Items**](hme?id=registering-items)

#### Types of items
---

#### Properties of an item

Below is a table of available **properties** that can be **modified** on any `Item` object. These properties exist in **all** `Item` objects and inherited classes, however their defaults are different depending on the implementation. 

For this reason, defaults have been left out of the table. **Check side bar for item specific properties.**
<!-- panels:start -->
<!-- div:left-panel -->

| Name      | Description           | Can Set |  Type               |    Options                                                    |
| :---      | :---:                  | :---:   |      :---:               |:---:                                                              |
| rarity    |  Sets an items rarity value, which also changes its name color |    Yes     | Item.Rarity (enum)  | NORMAL, COMMON, UNCOMMON, RARE, EPIC, LEGENDARY, QUEST, UNIQUE |
| hungerUsage    |  Player hunger cost on use. Only used when attacking.  |    Yes     | float  | Float denoting how much hunger to remove from the player on use |
| worldDrawSize    |  Size of the item when dropped in the world (default 24) |    Yes     | int  | Object size when placed in pixels |
| attackXOffset    |  Offsets attack animation on the X axis |    Yes     | int  | Pixel count - negative values move item to the right relative to where the item is facing |
| attackYOffset    |  Offsets attack animation on the Y axis |    Yes     | int | Pixel count - negative values move item forward relative to where the entity is facing |
| animSpeed    |  Speed at which animations are played, seems to play a part in cooldown time. |    Yes     | int  | Time in miliseconds |
| cooldown    |  Downtime between item uses (Attack speed, for example)  |    Yes     | long  | Time in miliseconds |
| animAttacks    |  TODO |    Yes     | TODO  | TODO |
| changeDir    |  Unknown TODO |    Yes     | boolean | True - False |
| keyWords    |  Item keyword categorization for searching and recipes. Override `Item.matchesSearch` to change search functionality  |    Yes     | Array<String> | Any string to categorize your item. `stone`, `armor`, etc for example |
| stackSize    |  Maximum inventory stack size of the item |    Yes     | Int  | Integer value denoting the maximum number of items in a stack |
| dropAsMatDeathPenalty    |  If the item should be treated as a material on death and dropped  |    Yes     | boolean  | True - False |
| dropDecayTimeMillis    |  Time in ms before dropped items are removed (Lifespan) |    Yes     | long  | Long value in miliseconds |
| isPotion    |  TODO |    Yes     | TODO  | TODO |


<!-- div:right-panel -->
<!-- tabs:start -->
#### **Java**

```java
public MyConstructor() {
    rarity = Item.Rarity.NORMAL;
    worldDrawSize = 24;
    attackXOffset = 8;
    attackYOffset = 16;
    hungerUsage = 0.0F;
    animSpeed = 200;
    cooldown = 0;
    animAttacks = 1;
    changeDir = false;
    keyWords = new ArrayList();
    stackSize = stackSize;
    dropAsMatDeathPenalty = true;
    dropDecayTimeMillis = 50;
    isPotion = false;
}
```

#### **Kotlin**

```kt
init {
    rarity = Item.Rarity.NORMAL
    worldDrawSize = 24
    attackXOffset = 8
    attackYOffset = 16
    hungerUsage = 0.0F
    animSpeed = 200
    cooldown = 0
    animAttacks = 1
    changeDir = false
    keyWords = new ArrayList()
    stackSize = stackSize
    dropAsMatDeathPenalty = true
    dropDecayTimeMillis = 50
    isPotion = false
}
```
<!-- tabs:end -->
<!-- panels:end -->

### Textures and icons
---


### Registering items
---

Registering items is required for them to be loaded into the game on startup. Registering an item always required creating a new instance of the item and providing a `string` id for it. For this reason, it's possible to create simple basic items
without creating a new class beforehand. 

<!-- panels:start -->
<!-- div:left-panel -->

It's recommended to create a `ItemRegistry` class in your mod, similar to the one `Necesse` uses internally. This allows you to have a [**single source of truth**](https://www.mulesoft.com/resources/esb/what-is-single-source-of-truth-ssot#:~:text=A%20single%20source%20of%20truth%20(SSOT)%20is%20the%20practice%20of,via%20a%20single%20reference%20point.) for all your item definitions and registrations, which is important to maintain clean code. 

This `ItemRegistry` class can then be called in your `ModEntry` class on `init` (for objects) or `postInit`

<!-- div:right-panel -->
<!-- tabs:start -->
#### **Java**

```java
// Could be done either statically, or with a singleton. Necesse uses a singleton.
public ModItemRegistry() {
    public static void registerAll() { /* Item registrations go here */ }
}
```

#### **Kotlin**

```kt
// Singleton example, can be done functionally (without the class) if prefered.
object ModItemRegistry {
    fun registerAll() { /* Item registrations go here */ }
}
```
<!-- tabs:end -->

<!-- div:left-panel -->
Once we have our `ItemRegistry` setup we can now add our items to it so they get added. As mentioned before, we can register simple items here to avoid creating a new class for our items. 

Even if you're using an existing class, you have the possibility of overloading existing methods to provide **some** custom functionality for your object! This is great when you only want to modify one thing without creating an entirely new class for it. For example, wanting to add [**modifiers**](/buffs/modifiers.md) to armor or weapons. 

<!-- div:right-panel -->
<!-- tabs:start -->
#### **Java**

```java
//..
public static void registerAll() { 
     // Register our custom item class
    ItemRegistry.registerItem(
        "mycustomsword",    // Item ID. This will be used to refer to the weapon elsewhere, such as recipes. Feel free to make a static variable out of it.
        new MySwordItem(50),    // Our item that will be registered
        30f,                // broker value
        true);              // If the object is obtainable

    // Register an existing basic class
    ItemRegistry.registerItem(
        "mybasicsword",
        new CustomSwordToolItem(Item.Rarity.UNCOMMON, 200, 50, 120, 100, 30),
        30f,
        true);

    // Register existing basic class with overloaded method
    ItemRegistry.registerItem(
        "myhelmet", 
        new SetHelmetArmorItem(2, 350, 2f, "myhelmet", "mychestplate", "myboots", "setbonus")  {
            public ArmorModifiers getArmorModifiers(InventoryItem item, Mob mob) { /* overloads armor modifiers */
                return new ArmorModifiers(new ModifierValue(BuffModifiers.EMITS_LIGHT, true));
            }
        },
        150, 
        true);
 }
```

#### **Kotlin**

```kt
//...
fun registerAll() {
    // Register our custom item class
    ItemRegistry.registerItem(
        "mycustomsword",    // Item ID. This will be used to refer to the weapon elsewhere, such as recipes. Feel free to make a static variable out of it.
        MySwordItem(50),    // Our item that will be registered
        30f,                // broker value
        true)               // If the object is obtainable

    // Register an existing basic class
    ItemRegistry.registerItem(
        "mybasicsword",
        CustomSwordToolItem(Item.Rarity.UNCOMMON, 200, 50, 120, 100, 30),
        30f,
        true)
    )

    // Register set helmet with overloaded method
    ItemRegistry.registerItem(
        "myhelmet",
        object : SetHelmetArmorItem(1, 150, Rarity.RARE, "myhelmet", "mychestplate", "myboots", "setbonus") {
            override fun getArmorModifiers(item: InventoryItem, mob: Mob): ArmorModifiers { return super.getArmorModifiers(item, mob) }
        },
        15f,
        true)
}
```
<!-- tabs:end -->

<!-- div:left-panel -->
To ensure your items are registered on mod start, you must now add your `ItemRegistry` to your entrypoint `init` call. It's important to ensure that different types of registrations happen in the correct order provided by the developer. 
Items are not the only thing that can be registered, Mobs, Objects, Buffs, and more all have their own `Registry` classes you'll have to create. Below you will find the order recommended by the developer for registering mod objects.

More information can be found [**on the official modding page**](https://necessewiki.com/Modding#public_void_init.28.29)

- **Tiles**
- **Objects**
- **Biomes**
- **Buffs**
- **Global ingredients**
- **Recipe techs**
- **Items**
- **Enchantments**
- **Mobs**
- **Pickup entities and projectiles**
- **Level, world events, level data and settlers**
- **Containers**
- **World generators**
- **Packets**
- **Quests**
- **Other**

<!-- div:right-panel -->
<!-- tabs:start -->
#### **Java**

```java
@ModEntry
public class ModEntryPoint {
    public void init() {
        ModItemRegistry.registerAll();
    }
}
```

#### **Kotlin**

```kt
@ModEntry
class ExampleMod {
    fun init() {
        ModItemRegistry.registerAll()
    }
```
<!-- tabs:end -->


<!-- panels:end -->
### Adding recipes
---


