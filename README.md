# FiveM Coding Standards @ Versa
Lua doesn't really have a widely adopted central coding standard, especially when it comes to FiveM development. A lot of the coding standard around FiveM can be drastically improved, and we hope this will help people! This repository outlines the coding standards we follow at Versa to ensure all contributed Lua code remains clean, consistent, and maintainable across all of our projects.

If you have suggestions or improvements, feel free to open a Pull Request — we’d love to see your ideas!

# 📚 Table of Contents

- [1.0 - Lua Syntax](#10---lua-syntax)
  - [1.1 - Constants](#11---constants)
  - [1.2 - Variables & Functions](#12---variables--functions)
  - [1.3 - Spacing](#13---spacing)
  - [1.4 - Loops](#14---loops)
  - [1.5 - Semicolons](#15---semicolons)
- [2.0 - FiveM Development Practices](#20---fivem-development-practices)
  - [2.1 - Events](#21---events)
  - [2.2 - Citizen Events](#22---citizen-events)
  - [2.3 - Exports](#23---exports)
  - [2.4 - When to use an event vs export](#24---when-to-use-an-event-vs-export)
  - [2.5 - Client->Server / Server->Client Communication](#25---client-server--server-client-communication)
  - [2.6 - Server Validation (Protecting against cheaters)](#26---server-validation-protecting-against-cheaters)
- [3.0 - Documentation & Commenting](#30---documentation--commenting)
  - [3.1 - Documenting Functions](#31---documenting-functions)
  - [3.2 - Commenting](#32---commenting)
- [4.0 - File Structure](#40---file-structure)
- [5.0 - Error Handling](#50---error-handling)



## 1.0 - Lua Syntax
### 1.1 - Constants:
* When defining a constant, use `UPPER_SNAKE_CASE`. We also make use of the new Lua 5.4 [<const>](https://www.lua.org/manual/5.4/manual.html#3.3.7) keyword.
  * Must [define Lua 5.4](https://docs.fivem.net/docs/scripting-reference/resource-manifest/resource-manifest/#lua54) runtime in the `fxmanifest.lua` in order to use it
```lua
local SERVER_MAX_PLAYERS <const> = 100
```
* However, local variables assigned from runtime data (e.g., property on an object) that do not change **inside a function** should use `lowerCamelCase` and can be marked with the <const> keyword if it will not be reassigned. 
  * Example: 5.0 Error Handling -> `hasMoney()`
```lua
local spendLimit <const> = Account.spendLimit
```
### 1.2 - Variables & Functions:
* While Lua’s standard library and most popular community codebases use `snake_case`, we prefer to use `lowerCamelCase`/`UpperCamelCase` as we believe there is better consistency and readability. . 
* Do **not** use [Hungarian Notation](https://en.wikipedia.org/wiki/Hungarian_notation#Examples) when defining variables
* Keep functions short (some say even 20-25 lines). Separate longer functions into smaller [helper functions](https://web.cs.wpi.edu/~cs1101/a05/Docs/creating-helpers.html#:~:text=A%20helper%20function%20is%20a,giving%20descriptive%20names%20to%20computations.).
* Use `lowerCamelCase` for all **local variables** and **local functions**.
```lua
local someLocalVariable = {}

local function someLocalFunction()
  -- ... code ...
end
```
* Use `UpperCamelCase`  for all **global variables** and **global functions**.
```lua
SomeGlobalVariable = {}

function SomeGlobalVariable()
  -- ... code ...
end
```
### 1.3 - Spacing
* Use spaces around operators for better readability. 
  * Do not put any spaces inside parentheses for function calls or grouped expressions...
```lua
-- Don't do this:
print(firstName..' '..lastName)
local sum=a+b
local result = ( a + b ) * c
local numbers = {1,2,3}

-- Do this:
print(firstName .. ' ' .. lastName)
local sum = a + b
local result = (a + b) * c
local numbers = {1, 2, 3}
```
### 1.4 - Loops
* Use `ipairs` when you want to iterate **until the first nil** and the data is structured as a clean array.
* Use `for i = 1, #table` when performance is critical or when you know the array may contain `nil` values but `#table` still gives you a safe bound.
* Always avoid modifying the table you're looping over during iteration.

### 1.5 - Semicolons
* **Nope.** Separate code onto multiple lines.

## 2.0 - FiveM Development Practices
### 2.1 - Events
* When Registering an event, define the handler function directly in `RegisterNetEvent`
* Naming events should always follow the same pattern - `resource:server/client:eventKey`
```lua
-- Don't do this
RegisterNetEvent('panel:server:sendLog')
AddEventHandler('panel:server:sendLog', function(message)
  print('Log Message: ' .. message)
end)

-- Do this
RegisterNetEvent('panel:server:sendLog', function(message)
  print('Log Message: ' .. message)
end)
```
### 2.2 - Citizen Events
* Inspired from Project Error's [FiveM Lua Style](https://github.com/project-error/fivem-lua-style), we agree with what they say: "Where possible, avoid using the Citizen prefix for the Citizen table of functions"
* The following Citizen functions are globally aliased and can be called without the prefix: `SetTimeout`, `Wait`, `CreateThread`, `RconPrint` (alias for Citizen.Trace). For all other Citizen table functions, continue to use the full `Citizen.` prefix as they are not aliased globally.
```lua
-- Don't do this
Citizen.CreateThread(function()
  Citizen.Wait(1000) -- 1 second wait with ugly Citizen prefix
  -- ... code ...
end)

-- Do this
CreateThread(function()
  Wait(1000) -- 1 second wait
  -- ... code ...
end)
```
### 2.3 - Exports

### 2.4 - When to use an event vs export

### 2.5 - Client->Server / Server->Client Communication

### 2.6 - Server Validation (Protecting against cheaters)

## 3.0 - Documentation & Commenting
### 3.1 - Documenting Functions
* When documenting a function, you always use 3 dashes (`---`) to describe what the function does, and then for each `@param` you make a new line, and finally the `@return` to describe what the function returns.
* Note: You don't *need* to document every single local function — especially small, well-named [helper functions](https://web.cs.wpi.edu/~cs1101/a05/Docs/creating-helpers.html#:~:text=A%20helper%20function%20is%20a,giving%20descriptive%20names%20to%20computations.) that are only used within a single file. 
  * Use your judgment: if the function has non-obvious logic, multiple parameters, or will be used in multiple places, it's worth documenting.
```lua
--- Add money to a player's bank account
-- @param player int        The online player ID
-- @param account int       The account ID to add money to
-- @param amount int        The amount of money to add
-- @param message string    The transaction message (reason for adding money)
-- @return table or (nil, string)  
--         Returns a table of the account object on success, or nil and an error message on failure
function AddMoneyToAccount(player, account, amount, message)
  -- ... code ...
end
```
### 3.2 - Commenting
* Use comments to explain **why** something is done, not what the code is doing—good code will be self-explanatory. Essentially, make sure they are concise and relevant
* Use single-line comments (`--`) for short explanations and multi-line comments (`--[[ ... ]]`) for longer descriptions or disabling code blocks temporarily.
* Use `TODO:` and `FIXME:` tags to indicate missing features/problems in existing code.
```lua
-- TODO: implement method
local function something()
   -- FIXME: check conditions
end
```

## 4.0 - File Structure
* Resources should be lowercase and split by underscores. All client files must have a `cl_` prefix in the filename & all server files must have a `sv_` prefix in the file name. All filenames must be fully lowercase. 
* A config folder is used to separate by environment (client, server, shared).
  * Obviously, you do not want to store sensitive data in the client or shared config as the client can access it.
* We have a [resource template](https://github.com/versa-development/fivem-resource-boilerplate) on our GitHub (fxmanifest data included)
```your_resource
 ├── fxmanifest.lua
 ├── client
 │    └── cl_main.lua
 ├── server
 │    └── sv_main.lua
 ├── shared
 │    └── sh_utils.lua
 └── config
      ├── sh_config.lua     -- shared config
      ├── sv_config.lua     -- server-only config
      └── cl_config.lua     -- client-only config (if needed)
```

## 5.0 - Error Handling
* Functions that can fail for expected reasons should return `nil` and a (string) error message
* One of the main reasons we (Versa) use this is because we use a lot of APIs. Also, returning errors from [helper functions](https://web.cs.wpi.edu/~cs1101/a05/Docs/creating-helpers.html#:~:text=A%20helper%20function%20is%20a,giving%20descriptive%20names%20to%20computations.) keeps main code cleaner as you won't need to plaster hard-coded error messages all over it (main codebase).
```lua
-- a really simple way to show how to handle an error using a helper function
-- does all validation in helper function so the main payForFood() is clean
local function hasMoney(amountNeeded)
  local bankAccount <const> = Account.money
  local spendLimit <const> = Account.spendLimit
  local spentToday <const> = Account.spentToday

  if amountNeeded > bankAccount then
    return nil, 'Missing $' .. (amountNeeded - bankAccount)
  end

  if spentToday >= spendLimit then
    return nil, 'You are over your spend limit'
  end

  return true
end

-- Calling code example (would be in main code)
local function payForFood(amount)
  local result, err = hasMoney(amount) -- trigger our helper function
  if not result then
    ShowNotification(err) -- The helper function handles all error messages, so this stays clean
    return -- dont run any mmore of the code
  end
  
  getMyFood()
end
```
* In other cases (e.g. not using an API) you could also just show the notification in the helper function then just return inside `payForFood()`
```lua
local result = hasMoney(amount) -- trigger our helper function
if not result then return end -- dont run any more code
```
