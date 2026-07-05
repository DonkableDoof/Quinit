# Quinit

Quinit is an easy to use module loader for Roblox Luau. It allows you to cache and require modules on the server and client, as well as link up various helpful default functions to prevent calling multiple of the same connections.

### How To Use

**Setup** _(Server or Client script)_
```
const Quinit = require(ReplicatedStorage.Packages.Quinit)
local moduleFolder = path.to.modules

Quinit._Init(moduleFolder:GetChildren())
Quinit._Start()
```

**Template Module**
```
local Service = {}

function Service.Example(self: Service)
end

function Service._Start()
end

function Service._OnPlayerAdded(player: Player)
end

function Service._OnCharacterAdded(character: Model, player: Player)
end

function Service._OnPlayerRemoving(player: Player)
end

function Service._OnHeartbeat()
end

return Service
```
