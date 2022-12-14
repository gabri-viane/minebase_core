# Minebase API (_WORK IN PROGRSS_)

## DOCUMENT NOT UP TO DATE: `minebase.colors.list` AND `minebase.screen` STATIC DATA IS NOW MOVED IN `minebase.statics`

This mod currently adds some HUD methods and other utility functions. It exports just one table: `minebase`.
This table has currently the following elements:
- `minebase.statics` : has static data
- `minebase.functions` : contains many utility functions
- `minebase.screen` : it's used as support for HUD and provide ready-to-use parameters for HUD functions.
- `minebase.colors` : provides a list of colors and other functions
- `minebase.commands` : provides api for command
- `minebase.HUD` : provides api for HUD
- `minebase.effects` : provides api for Effects (like speed boost, night vision... or custom ones) 
- `minebase.m` : shortcuts for methods and lists provided by other packages (listed up here)

## **IMPORTANT:**
**Some functions are defined as methods: they require the 'self' parameter!! So if something isn't working check again these docs to see if you are calling correctly the function/method** 

### How is structured
Everything is placed in a table where you would expect it: e.g. you are looking for a color: black. Every element that is provided by this mod and is not a function is under the package.`list` table:
`minebase.colors.list.black` 

An effect, for example jump_boost:
`minebase.effects.list.jump_boost`

If you are looking for an effect function it will be in: `minebase.effects.functions`, same for HUD: `minebase.HUD.functions`, and so on... 

### Types
Values can be: mandatory ( k = v )  or optional ( k ?= v )

 - `<string>` : string 
 - `<number>` : integer / float 
 - `<boolean>` : boolean
 - `<table>` : table 
 - `<function>` : function 
 - `<PlayerRef>` : Minetest API reference 
 - `<position>` : table `{x=<number>, y=<number>}` : `minebase.screen.*` or `minebase.screen.square.*`  
 - `<color>` : #RGBA string : `minebase.colors.list.*.*` 
 - `<hud:def>` : table of hud type def 
 - `<hud:container>` : container 
 - `<element_table>` : table `{name=<string>, element=<hud:*>}` 
 - `<text_instruction_table>` : table `{text=<string>, font_color?=<color>, bg_color?=<color>}` 
 - `<param_table>` : table `{name=<string>, type=<string>, optional=<boolean>}` 
 - `<drawable_table>` : table `{id?=<number>, name=<string>,type=<string>, drawable=<hud:*>, refresh?=table<<update_table>>}`
 - `<update_table>` : table `{*=*}`

## `minebase.statics`
Doesn't provide any useful data.

## `minebase.functions`
This package has all utilty functions used by the mod itself that can be used but carefully.

### Content:
1. ### _registerTx_:
    This function subscribe a _tick_ method on the _globalstep_ register: it's called every dtime (from minetest docs about 0,1s) and after `dt`-seconds it removed from the register. <br>
    It's defined as a method:
    ```lua
    function minebase.functions:registerTx (to_subscribe) 
    ```
    * `to_subscribe = {dt = <number>, finish = <function>, tick = <function(self)>}`<br>
        * `dt` : `<number>` :  is the number in seconds before the function `finish` is called, it's decremented by dtime every step
        * `finish` : `<function>` :  is a function that is called when `dt` is <= 0
        * `tick` : `<function>` :  is a method: is called every global step but not when `finish` is called. A self table is passed to this function every step.(`to_subscribe:tick()`)

    After the `finish` function is called `to_subscribe` is removed from globalstep register.
2. ### _stringToTokens_
    Spit a given string in tokens and return a table of tokens.<br>
    It's defined as a function:
    ```lua
    minebase.functions.stringToTokens = function(string)
    ```
    * `string` : `<string>` : the string to be splitted in tokens

    e.g. 
    ```lua
    tb = minebase.functions.stringToTokens("Hello how are you? I'm f1n3");

    --tb={"Hello","how","are","you","I","m","f1n3"}
    ```
    This function uses the regex:`([%a%d_-]+)`
3. ### _numberToTimer_
    Converts a number in seconds to a string of the format `minutes:seconds`.<br>
    It's defined as a function:
    ```lua
    minebase.functions.numberToTimer = function(seconds)
    ```
    * `seconds` : `<number>` : the number of seconds to be converted.
    
    e.g. 
    ```lua
    str = minebase.functions.numberToTimer(90);

    --str="1:30"
    ```
4. ### _sizeToString_
    Converts a _size table_ to a string. The returned string is formatted as `XxY` where `x` is the letter x.<br>
    It's defined as a function:
    ```lua
    minebase.functions.sizeToString = function (size)
    ```
    * `size` : `<position>` : it's a size table
    
    e.g. 
    ```lua
    str = minebase.functions.sizeToString({x=10,y=50});

    --str="10x50"
    ```
5. ### _warpString_
    Given a string it returns a text and the number of lines where every line is of max n-chars.<br>
    It's defined as a function:
    ```lua
   minebase.functions.warpString = function(string_to_warp, max_length)
    ```
    * `string_to_warp` : `<string>` : the string to warp;
    * `max_length` : `<number>` : the max number of characters on a line;
    
    e.g. 
    ```lua
    tb = minebase.functions.warpString("Hi how are you?",6);

    --[[
        tb={
            [0] = "Hi how\nare\nyou?"
            [1] = 3 --It's made out of 3 lines
        }
    ]]
    ```
6. ### _warpString_CN_
    A recursive function to support _warpString_
7. ### _splitString_
    Split a string using a separator and returns a table.<br>
    It's defined as a function:
    ```lua
    minebase.functions.splitString = function(inputstr, sep)
    ```
    * `inputstr` : `<string>` : the string to be splitted in tokens;
    * `sep` : `<string>` : the separator to use;

    e.g. 
    ```lua
    tb = minebase.functions.stringToTokens("Hello how are you? I'm f1n3"," ");

    --tb={"Hello","how","are","you?","I'm","f1n3"}
    ```
    This function uses the regex:`([^separator]+)`
8. ### _convertStringTo_
    Used by the mod, not meant to be used externally.

## `minebase.screen`
This pacakge is very useful used with the HUD's functions and methods.
It provides many useful and default variables to pass to this mod's methods and functions without having to declare them again.

### Content:
1. Many ready-to-use screen positions:
    ```lua
    top_left = { x = 0, y = 0 },
    top_center = { x = 0.5, y = 0 },
    top_right = { x = 1, y = 0 },
    center_left = { x = 0, y = 0.5 },
    center_center = { x = 0.5, y = 0.5 },
    center_right = { x = 1, y = 0 },
    bottom_left = { x = 0, y = 1 },
    bottom_center = { x = 0.5, y = 1 },
    bottom_right = { x = 1, y = 1 },
    ```
    You can acces them with `minebase.screen.position_name` where `position_name` is one of the listed above.
2. Screen dimensions (just square for now), in pixels:
    ```lua
     square = {
        small_s = { x = 4, y = 4 },
        small = { x = 8, y = 8 },
        small_l = { x = 16, y = 16 },
        medium_s = { x = 24, y = 24 },
        medium = { x = 32, y = 32 },
        medium_l = { x = 44, y = 44 },
        large_s = { x = 56, y = 56 },
        large = { x = 64, y = 64 },
        large_l = { x = 72, y = 72 },
    }
    ```
    You can acces them with `minebase.screen.square.dimension_name` where `dimension_name` is one of the listed above.
3. ### _enableScreen_
    Register a player to this mod screen utilities. (A player is automatically registered on join end removed on quit)<br>
    It's defined as a method:
    ```lua
    function minebase.screen:enableScreen(player)
    ```
    * `player` : `<PlayerRef>` : the PlayerRef to regiseter;
4. ### _disableScreen_
    Remove a player from using this mod screen services.<br>
    It's defined as a method:
    ```lua
    function minebase.screen:disableScreen(player)
    ```
    * `player` : `<PlayerRef>` : the PlayerRef to remove;
5. ### _get_
    Get a container (HUD element that will be explained later), previously registered to a player screen, by it's name.<br>
    It's defined as a method:
    ```lua
    function minebase.screen:get(player, container_name)
    ```
    * `player` : `<PlayerRef>` : the PlayerRef to get the screen;
    * `container_name` : `<string>` : the name of the previously registered container.

     e.g. 
    ```lua
    player=...;
    container = minebase.screen:get(player, "example");
    --if found: container = {name="example",...}
    --otherwise: container = nil
    ```

    If the player is not found registerd in the screen it fails.
6. ### _addToScreen_
   Adds a container to the screen of a player. The player is the owner of the container.<br>
   It's defined as a method:
    ```lua
    function minebase.screen:addToScreen(container)
    ```
    * `container` : `<hud:container>` : the container to register to the owner screen.

     e.g. 
    ```lua
    player=...;
    container = { --Later will be explained how to correctly create containers
        name="example",
        owner=player,
        ...
    };
    minebase.screen:addToScreen(container);
    --if everything is correct then calling:
    --  minebase.screen:get(player, "example");
    --Will return the container.
    ```
7. ### _removeFromScreen_
    Removes a container previously added to a screen.<br>
    It's defined as a method:
    ```lua
    function minebase.screen:removeFromScreen(container)
    ```
    * `container` : `<hud:container>` : the container to remove from the owner screen.

     e.g. 
    ```lua
    player=...;
    container = { --Later will be explained how to correctly create containers
        name="example",
        owner=player,
        ...
    };
    minebase.screen:removeFromScreen(container);
    --if everything is correct then calling:
    --  minebase.screen:get(player, "example");
    --Will return nil.
    ```
8. ### _assignToScreen_ and _refreshScreen_
    Not currently used.

## `minebase.colors`
Provides some methods to make easier to use colors in this mod.

### Content:
1. Some colors:
   - red
   - orange
   - yellow
   - green
   - sky_blue
   - blue
   - violet
   - purple
   - white
   - black
   - random

    Each color has two values: light and dark (not _random_).
    To use a color just type `minebase.colors.list.color_name.variant`.<br> e.g. (orange_light) : `minebase.colors.list.orange.light` => #FFA500FF

    random has an element: `rnd1` which is not a color value but a function that returns a random color.

    This list is expandable by other mods.

2. ### _getColorString_
    Returns a #RGBA string given a name and a type of color.<br>
    It's defined as a function:
    ```lua
    minebase.colors.functions.getColorString = function(color_name, type)
    ```
    * `color_name` : `<string>` : the name of the color from the list(if not found default is black).
    * `type` : `<string>` : the variant of the color from the list (light,dark, or custom)(if not found default is light).

    if the color.type is a function then the value returned from the function is returned. (As for `random.rnd1`).

3. ### _getColorHex_
    Same as _getColorString_ but instead of a string a hex number is returned.<br>
    It's defined as a function:
    ```lua
    minebase.colors.functions.getColorHex = function(color_name, type)
    ```
    * `color_name` : `<string>` : the name of the color from the list(if not found default is black).
    * `type` : `<string>` : the variant of the color from the list (light,dark, or custom)(if not found default is light).
4. ### _addColor_
    Allows to add a color to the list.<br>
    It's defined as a function:
    ```lua
    minebase.colors.functions.addColor = function(color_name, type, value)
    ```
    * `color_name` : `<string>` : the name of the color.
    * `type` : `<string>`  :the variant of the color.
    * `value` : `<string>` : the value of the color variant: can be a string or a function (or anything else, but not supported).
5. ### _setTextColor_
    Formats a table to a coloured text with escape sequences. It can colorize a text with different font and/or background color (if defined, otherwise no color is set).<br>
    It's defined as a function:
    ```lua
    minebase.colors.functions.setTextColor = function(text_instructions)
    ```
    * `text_instructions` : `<table<text_instruction_table>>` :  a table of _text_instruction_.

    A _text instruction_ is a table with three key-value pairs:
      1. _text_ : the text to format with provided colors. It's mandatory
      2. _font_color_ : the hex string of the color for the font. (Optional)
      3. _bg_color_ : the ehx string of the color for the background.  (Optional)

    e.g.
    ```lua
    text_instructions = {
        {
            text = "String to test",
            font_color = minebase.colors.list.green.light
        },
        {
            text = "here the text continues"
            --No need for font_color and bg_color if we want default color
        }
    }
    text = minebase.colors.functions.setTextColor(text_instructions)
    --The result is
    --"String to testhere the text continues"
    --where "String to test" is with the escape sequence for color green_light and the rest of the phrase (see there is no space added!!) it's the default engine color.
    ```

## `minebase.commands`
Currently only adds 1 function. It allows to register a new chat command with error and parse logic built-in.<br>
It's defined as a function:
```lua
minebase.commands.functions.addCommand = function(mod_name, command_alias, param_list, privileges, callback_function,description)
```
* `mod_name` : `<string>` : a string of the mod which is registering the command.
* `command_alias` : `<string>` : the alias for the chat command (string). e.g. for /jump, *jump* would be the alias.
* `param_list` : `<table<param_table>>` : an id-ordered table of prameters needed by the command:
  e.g.

  for the command: /jump \<who> \<height> <br>
  The parameters are _who_ and _height_, supposing that who is a string of a player name and height a number that is optional.
  ```lua
    jump_param_list={
        {
            name = "who",--name of the parameter
            type = "string",--type of the parameter
            optional = false--Optional or not
        },{
            name = "height",--name of the parameter
            type = "number",--it will be casted to number
            optional = false--Optional or not
        }
    }
    ```
* `privileges` : `<table>` :  a list of privileges required.
* `description` : `<string>` : the description of the command.
* `callback_function` : `<function>` :  the function called when a player uses the command, this is how it needs to be defined:
    ```lua
    function(name,params)
        --name is the name of the player
        --params is a list of parameters
        --params={name=value,name=value,...}

        --using the "/jump <who> <height>" example
        local who = params.who;--returns the <who> part of the command
        local height = params.height; --you need to check this because it's optional
        --logic
        return "";--Return string to send to player's chat
    end
    ```

e.g. of usage:
```lua
minebase.commands.functions.addCommand(
    "minebase",
    "jump",
    {
        {
            name = "who",-
            type = "string",
            optional = false
        },{
            name = "height",
            type = "number",
            optional = false
        }
    },
    {},--no privileges needed
    function(name,params)
        local msg = "Requested from "..name.." to make player "..params.who.." to jump";
        if params.height then
            return msg.." "..params.height.."meters";
        else
            return msg;
        end
    end,
    "This command does basically nothin");
```

**ATTENTION**: 
The order of the param_list is extremly important:<br>
/jump \<who> \<height><br>
* \<who> is set to the first param, and \<height> to the second param in the param list passed.
* If who is optional and is not passed than all the next parameters are ignored: if an optional parameter is not found all the others are ignored, even if not optional

## `minebase.HUD`
This package has many methods to allow a better HUD building and defines a new concept: the ***container***.

But before explaining what a container is here is a list of functions that allows basic HUD components (also identified **type="def"** in the mod):

1.  ### newText: 
    It's a function that returns a table of a **hud_elem_type="text"**
    ```lua
    minebase.HUD.functions.newText = function(text, scale, offset, color, direction, alignment, size, style, z_index)
    ```
    All the parameters are already defined in Minetest API (just _color_ is _number_ in Minetest API). Also: default values are:
    * `direction = 0`
    * `size = { x = 1, y = 0}`
    * `style = 0`
    * `number = (black) #000000`
    * `z_index = 0`

2. ### newImage
    It's a function that returns a table of a **hud_elem_type="image"**
    ```lua
    minebase.HUD.functions.newImage = function(image, scale, offset, direction, alignment, z_index)
    ```
    All the parameters are already defined in Minetest API (just _color_ is _number_ in Minetest API). Also: default values are:
    * `direction = 0`
    * `alignment = { x = 0, y = 0}`
    * `z_index = 0`

## Container
There are some functions related to the handling of the container but I will not cover them as they are designed to not be modified and not meant to be "modder-friendly", but if you are intrested in modifing the behaviors or even optimize them, you can find them in
the package `minebase/scripts/HUD/HUD_container.lua`.

Here is their name:
* `minebase.HUD.functions.drawContainer(container)` : Handles first draw of items
* `minebase.HUD.functions.removeContainer(container)` : Handles remove from screen
* `minebase.HUD.functions.reloadContainerPosition(container)` : Refresh absolute position on screen
* `minebase.HUD.functions.reloadContainerOffsets(container)` : Refresh relative position on screen
* `minebase.HUD.functions.refreshContainer(container)` : Handle edits of sub-elements and refresh the components that requires (they are refreshed on globalstep)

**Container quick explanation** : A container is just a table that provides methods and attributes to handle a list of sub-elements. It's automatically refreshed when an element changes (added, removed, update), and it allows to avoid manual storage of id, calling player:_hud_add_/_hud_remove_/_hud_change_. 

It has a position and an offset, it's not an object drawn on screen but it helps to avoid annoying math when moving around object: when you update a position or offset of a container all his sub-elements are moved with it.

Elements added to a container don't require a position (even if provided it will be overwritten), they are just meant to work with offsets. 

The elements are drawn realtive to a container, e.g.:<br>
1. If the container is at position {x=0.5,y=0.5} (in the middle of the screen) then an element with ANY POSITION added to the container will be placed in the middle of the screen. To position the element just use the offset.
2. If the container is at position {x=0.5,y=0.5} with an offset of {x=0,y=10} then his sub-element with an offset of {x=10,y=5} will result at position {x=0.5,y=0.5} with an effective offset of {x=10,y=15}.
3. If the container is at position {x=0.5,y=0.5} and then updated to {x=0.6,y=0.9}, all his sub-components are moved to the same position, but THEY KEEP THEIR OFFSETS.

You may try yourself all the possibilities.

## 1. Attributes
   * `name` : `<string>` : name of the container, it's used to get an element back from its list of sub-elements
   * `owner` : `<PlayerRef>` : the player that owns this container, this will determine the behavior of `minebase.screen:addToScreen(container)`
   * `position` : `<position>` : only the main container uses this attribute, all the sub-elements (type = "def" or "container"), inherit the position of their main container.
   * `type` : `<string>` : it can be `"def"`or `"container"` (no need to handle it)
   * `offset` : `<position>` : the offset of this container reletive to the main container position (all of sub-elements offsets depend on it).
   * `elements` : `<table<drawable_tabe>>` : a list of _drawable_table_ 
   * `to_draw` : `<boolean>` : internal parameter
   * `to_refresh` : `<boolean>` : internal parameter
   * `refresh_callback` : `<function>` : a function called on every update request from the container (optional)

## 2. Methods
**(ATTENTION: USE container:function and not container.function)**
1.  ### **getElement**
    ```lua
    function container:getElement(nm)
    ```
    * `nm` : `<string>` : the name of the element previously added to this container
    * returns the drawable of a _drawable_table_: can be a _def_ or a _container_

2. ### **get**
    ```lua
    function container:get(nm)
    ```
    * `nm` : `<string>` : the name of the element previously added to this container
    * returns the _drawable_table_: 
        ```lua
        {
            id = nil, --or hud_add return value if type="def"
            name = "...",
            type="container",--or "def"
            drawable={...}--the element to draw
        }
        ```

3. ### **getID**
    ```lua
    function container:getID(nm)
    ```
    * `nm` : `<string>` : the name of the element previously added to this container
    * returns the id of the _drawable_table_ in `elements` matching the same name
  
4. ### **getLast**
    ```lua
    function container:getLast()
    ```
    * returns the last  _drawable_table_ in `elements` 

5. ### **move**
    ```lua
    function container:move(to_position)
    ```
    * `to_position` : `<position>` : position to move the container to (only for main container, not container as sub-elements of other container) 

6. ### **addOffset**
    ```lua
    function container:addOffset(x, y)
    ```
    * `x` : `<number>` : amount of pixels to offset on x axis
    * `y` : `<number>` : amount of pixels to offset on y axis

    As the name says: it adds to current offset, it doesn't replace the values.

7. ### **updateElement**
    ```lua
    function container:updateElement(nm, updates)
    ```
    * `nm` : `<string>` : the name of the component to update
    * `updates` : `<table<update_table>>` : a table of updates. Updates only applies on _def_ elements. For container this parameter is usless. This parameter needs to be a list of _update_table_ :
        ```lua
        {
            {--This is an update_table
                type="text",--or offset or alignment, or anything else from Minetest API HUD
                value="new_text" -- or any value requested by Minetest API
            },
            {
                type="number",--or offset or alignment, or anything else from Minetest API HUD
                value=0x00000 -- or any value requested by Minetest API
            },
        }
        ```

    After bein called this method calls, if present the `container.refresh_callback()`

8. ### **onRefreshRequest**
    ```lua
    function container:onRefreshRequest(RFunction)
    ```
    * `RFunction` : `<function>` : the function that will be called when a component update is requested (so a container refresh request)

9. ### **addElement**
    ```lua
    function container:addElement(nm, element)
    ```
    * `nm` : `<string>` : the name of the component to add, then the component can be searched by its name with `component:getElement(nm)`
    * `element` : `<hud:def>` or `<hud:container>` : the element to add: a container or an element created from `minebase.HUD.function.newText` or `minebase.HUD.function.newImage`

10. ### **addElements**
    ```lua
    function container:addElements(els)
    ```
    * `els` : `<table<element_table>>` :a list of element_table:
        ```lua
        {
            {
                name = "example1",--Name of the component
                element = {...}--The component
            },
            {
                name = "example2",
                element = {...}
            }
        }
        ```
    This method is the same as `container:addElement(nm,element)` but prevents multiple updates and it draws just once.

    Prefer this method when adding multiple elements at same time.

11. ### **delete**
    ```lua
    function container:delete()
    ```
    Delete all the elements inside this container and the container itself.

12. ### **removeElement**
    ```lua
    function container:removeElement(nm)
    ```
    * `nm` : `<string>` : the name of the component to remove

13. ### **registerToScreen**
    ```lua
    function container:registerToScreen()
    ```
    This method calls ` minebase.screen:addToScreen` and subscribe the container to globalstep for refreshing automatically the components. Otherwise it never updates.

    Sub-container don't need to use this method as the main container handles all of its components.

## 3. Create method
There are two functions to allow creation of (basic) containers:
1. ### **createContainer**
   ```lua
    minebase.HUD.functions.createContainer = function(player, name, position, offset)
    ```
    * `player` : `<PlayerRef>` : the owner of this container; this container, if registered to screen, will be owned by this player.
    * `name` : `<string>` : the name of the container. If registered to screen this is the name to get the container back.
    * `position` : `<position>` : absolute position of the container. All sub-elements are relative to this position. (For best practice use positions from `minebase.screen.[position_name]`)
    * `offset`  : `<position>` : relative position of the container to its absolute position. (Optional)
    * returns a **container**

2. ### **createLightContainer**
    ```lua
    minebase.HUD.functions.createLightContainer = function(player, name, position, offset, component)
    ```
    * `player` : `<PlayerRef>` : same as `createContainer`
    * `name` : `<string>` :  same as `createContainer`
    * `position` : `<position>` :  same as `createContainer`
    * `offset` : `<number>` :  same as `createContainer`
    * `component` : `<hud:def>` or `<hud:container>` : component to set for this container
    * returns a **container**

    This container can only contain 1 element (the paramete `component`). Add,Remove methods do not work. 
    Every get method returns the first sub-element.

    Use this container when you need just one element to box.

    
### Already-built Containers
Thera are some components already prepared for being used in mods, these components are just containers with elements already in them. You can create components by calling specific methods.<br>
**Methods that ends with T have all the images and icons boundled in one element to optimize the HUD**, otherwise default methods create components with an element for each image.<br>
These are allo methods, call them with ":".
1. ### _newIconBox_
    This method create a frame containing an image. 
    ```lua
    function minebase.HUD.complex:newIconBox(player, name, image, position)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `image` : `<string>` : the image to show in the frame; optimal texture size: 32x32 with 3px around of transparent border.
    * `position` : `<position>` : the position on the screen.
    * returns the container. 

    Container has these default elements:
    * `background` : `<hud:def>` : the background image
    * `square` : `<hud:def>` : the border frame around the image
    * `image` : `<hud:def>` : the image shown

2. ### _newIconBoxT_
    This method create a frame containing an image.
    ```lua
    function minebase.HUD.complex:newIconBoxT(player, name, image, position, size)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `image` : `<string>` : the image to show in the frame.
    * `position` : `<position>` : the position on the screen.
    * `size` : `<number>` : the size in px of the iconBox. Default is `minebase.screen.square.medium_l` . Sizes can be found in: `minebase.screen.square.[size]`
    * returns the container.

    Container has these default elements:
    * `icon` : `<hud:def>` : the background image + the border frame around the image + the image shown

    ![IconBox](https://i.imgur.com/ecKRL7M.png)

3. ### _newBlockBox_
    This method create a frame containing a representation of a block (top,left and right \\|/ ).
    ```lua
    function minebase.HUD.complex:newBlockBox(player, name, image, position)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `image` : `<string>`  or `<table>` : the image to show in the frame that will be builded as a 3D block icon. (if table:{top = _string_, left = _string_, right = _string_}) 
    * `position` : `<position>` : the position on the screen.
    * returns the container. 

    Container has these default elements:
    * `background` : `<hud:def>` : the background image
    * `square` : `<hud:def>` : the border frame around the image
    * `image` : `<hud:def>` : the image of the 3d block shown

4. ### _newBlockBoxT_
    This method create a frame containing a representation of a block (top,left and right \\|/ ).
    ```lua
    function minebase.HUD.complex:newBlockBoxT(player, name, image, position, size)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `image` : `<string>`  or `<table>` : the image to show in the frame that will be builded as a 3D block icon. (if table:{top = _string_, left = _string_, right = _string_}) 
    * `position` : `<position>` : the position on the screen.
    * `size` : `<number>` : the size in px of the iconBox. Default is `minebase.screen.square.medium_l` . Sizes can be found in: `minebase.screen.square.[size]`
    * returns the container. 

    Container has these default elements:
    * `icon` : `<hud:def>` : the background image + the border frame around the image + the image of the 3d block shown

    ![BlockBox](https://i.imgur.com/4ncsIhW.png)

5. ### _newEffectBar_
    This method create an effect bar.
    ```lua
    function minebase.HUD.complex:newEffectBar(player, name, effect_applied, position)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `effect_applied` : `<effect>` : the effect to show in this bar. 
    * `position` : `<position>` : the position on the screen.
    * returns the container. 

    Container has these default elements:
    * `bar` : `<hud:def>` :the background image
    * `square` : `<hud:def>` :the border frame around the image
    * `image` : `<hud:def>` :the image of the effect
    * `text` : `<hud:def>` :the name of the effect
    * `timer` : `<hud:def>` :the countdown timer of the effect

6. ### _newEffectBarT_
    This method create an effect bar.
    ```lua
    function minebase.HUD.complex:newEffectBarT(player, name, effect_applied, position)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `effect_applied` : `<effect>` : the effect to show in this bar. 
    * `position` : `<position>` : the position on the screen.
    * returns the container. 

    Container has these default elements:
    * `base` : `<hud:def>` :the background image + the border frame around the image + the image of the effect
    * `text` : `<hud:def>` : the name of the effect
    * `timer` : `<hud:def>` : the countdown timer of the effect

7. ### _newEffectList_
    Creates the automatic list to show current effects. Needs to be changed.

8. ### _newBoxBorderT_
    This method creates just a border.
    ```lua
    function minebase.HUD.complex:newBoxBorderT(player, name, position, width, height)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `position` : `<position>` : the position on the screen.
    * `width` : `<number>` :the width of the box.
    * `height` : `<number>` :the height of the box.
    * returns the container. 

    Container has these default elements:
    * `border` : `<hud:def>` : a border image

9. ### _newBoxT_
    This method creates an empty box with border.
    ```lua
    function minebase.HUD.complex:newBoxT(player, name, position, width, height)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `position` : `<position>` : the position on the screen.
    * `width` : `<number>` : the width of the box.
    * `height` : `<number>` : the height of the box.
    * returns the container. 

    Container has these default elements:
    * `box` : `<hud:def>` : transparent background + border image

10. ### _newTextBoxT_
    This method creates an box with a border frame, a title and text (text automatically warps).
    ```lua
    function minebase.HUD.complex:newTextBoxT(player, name, position, width, height, title, title_color, text, text_color)
    ```
    * `player` : same as `createContainer`
    * `name` : same as `createContainer`
    * `position` : `<position>` : the position on the screen.
    * `width` : `<number>` :  the width of the box.
    * `height` : `<number>` : the height of the box.
    * `title` : `<string>` :  the text for the title of the box.
    * `title_color` : `<color>` :  font color for the title of the box.
    * `text` : `<string>` : the text for the box.
    * `text_color` : `<color>` : font color for the text of the box.
    * returns the container. 

    Container has these default elements:
    * `box` : `<hud:def>` :  transparent background + border image
    * `title` : `<hud:def>` : title of the box
    * `text` : `<hud:def>` : text of the box

### Complex containers
These are container with new behaviors:

## 1. List
A list is a container that renders elements with spacing between them. It allows to choose a direction so items are added along the same, also, if set, when reached a specific number element the list can warp in another direction.

Example list: append direction bottom, expand direction to left, expand to left after 5 elements appended. 

![List image](https://i.imgur.com/aHJvVxW.png)

- Yellow arrow : direction which this list appends the items
- Green arrow : direction which this list exends when it reaches 5 elements

```lua
function minebase.HUD.complex:newList(player, name, position, spacing, rules)
```
* `player` : same as `createContainer`
* `name` : same as `createContainer`
* `position` : `<position>` : the position on the screen.
* `spacing` : `<number>` : the spacing between elements. (_default 10px_)
* `rules` : `<table>` : additional rules can be set passing this table. Additional rules:
    * `direction` : `<position>` : the direction which the list appends: (_default {x=-1, y=0}_)
        * y=  [-1 : up]  [1 : down]
        * x=  [-1 : left] : [1 : right] 
    * `expandable` : `<boolean>` : if _true_ checks **expand_limit** and warps the list (_default false_)
    * `expand_limit` : `<number>` : max number of elements before the list warps (_default 5_)
    * `expand_direction` : `<position>` : the direction which the list warps: (_default {x=-1, y=0}_)
        * y=  [-1 : up]  [1 : down]
        * x=  [-1 : left] : [1 : right] 
    * `v_spacing` : `<number>` : spacing when the list warps (_default 10_)
* returns the container. 

The example list in the image has these for rules:
```lua
rules = {
    direction = { x = 0, y = 1 },
    expandable = true,
    expand_limit = 5,
    expand_direction = { x = -1, y = 0 },
    spacing = 48,
    v_spacing = 48
}
```

The List element also provides new methods:
1. ### **listAdd**
    Use this method instead of default _addElement_.
    ```lua
    function list:listAdd(el)
    ```
    * `el` : `<element_table>` : the element to append to the list:
        ```lua
        {
            name = "example1",--Name of the component
            element = {...}--The component
        }
        ```

2. ### **listRemove**
    Use this method instead of default _removeElement_.
    ```lua
    function list:listRemove(nm)
    ```
    * `nm` : `<string>` : the name of the element to remove from the list

## `minebase.effects`
//TODO

## Easy access 
Some functions and some lists are easly provided in: `minebase.m`. 
This table is just to have everything in the same place, for example:
```lua
minebase.m.effects.jump_boost
--same as
minebase.effects.list.jump_boost
```
