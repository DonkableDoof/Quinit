# Quinit

Quinit is an easy to use module loader for Roblox Luau. It allows you to cache and require modules on the server and client, as well as link up various helpful default functions to prevent calling multiple of the same connections.

### How To Use

**Setup** _(Server or Client script)_
```
const Quinit = require(ReplicatedStorage.Packages.Quinit)
local moduleFolder = path.to.modules

Quinit._Init(moduleFolder.Module1, 1) <- Set a run priority
Quinit._Init(moduleFolder.Module2, 2)
Quinit._Init(moduleFolder.ChildFolder:GetChildren())
Quinit._Start()
```

**Template Module**
```
local Service = {}

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

function Service._OnPreRender(deltaTime: number) -- Client only
end

return Service
```

When you send a module through Quinit, the following functions will run automatically when necessary:
- _OnPlayerAdded -> When a player joins the game
- _OnPlayerRemoving -> When a player leaves the game
- _OnCharacterAdded -> When a player character is loaded
- _OnHeartbeat -> Runs on every RunService Heartbeat
- _OnPreRender -> Runs before every rendered frame (Client only)
