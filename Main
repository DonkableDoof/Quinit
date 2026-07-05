--\\ VARIABLES //--
const RunService = game:GetService("RunService")
const Players = game:GetService("Players")
const ReplicatedStorage = game:GetService("ReplicatedStorage")
const Data = require(ReplicatedStorage.Data)

local CachedModules = {}
local ModulePriorities = {}

local connections = {} :: {[Player]: RBXScriptConnection}




--\\ FUNCTIONS //--
local function getOrderedModules(): { { name: string, module: any } }
	local ordered = {}
	for name, module in CachedModules do
		table.insert(ordered, { name = name, module = module })
	end
	table.sort(ordered, function(a, b)
		return (ModulePriorities[a.name] or 0) < (ModulePriorities[b.name] or 0)
	end)
	return ordered
end




--\\ QUINIT //--
local Quinit = {}


function Quinit.Get(name: string)
	return CachedModules[name]
end


function Quinit._Init(input: ModuleScript | {ModuleScript}, priority: number?)
	local scripts = typeof(input) == 'table' and input or {input} :: {ModuleScript}
	priority = priority and priority or 50

	for _, module in scripts do
		if not module:IsA('ModuleScript') then continue end
		if CachedModules[module.Name] then continue end
		local mod = require(module)
		CachedModules[module.Name] = mod
		ModulePriorities[module.Name] = priority
	end
end


function Quinit._Start()
	for _, entry in getOrderedModules() do
		if entry.module._Start then
			task.spawn(entry.module._Start)
		end
	end
	
	if RunService:IsServer() then
		for _, player in ipairs(Players:GetPlayers()) do
			task.spawn(Quinit._OnPlayerAdded, player)
			if player.Character then
				task.spawn(Quinit._OnCharacterAdded, player.Character, player)
			end
		end
		Players.PlayerAdded:Connect(function(player)
			Data.Server.Service:waitForData(player)
			Quinit._OnPlayerAdded(player)

			connections[player] = player.CharacterAdded:Connect(function(character: Model)
				Quinit._OnCharacterAdded(character, player)
			end)
		end)
		Players.PlayerRemoving:Connect(function(player: Player)
			task.spawn(Quinit._OnPlayerRemoving, player)

			if connections[player] then
				connections[player]:Disconnect()
				connections[player] = nil
			end
		end)
	else
		Players.PlayerAdded:Connect(function(player: Player)	
			task.spawn(Quinit._OnPlayerAdded, player)

			connections[player] = player.CharacterAdded:Connect(function(character: Model)
				task.spawn(Quinit._OnCharacterAdded, character, player)
			end)
		end)
		Players.PlayerRemoving:Connect(function(player: Player)
			task.spawn(Quinit._OnPlayerRemoving, player)

			if connections[player] then
				connections[player]:Disconnect()
				connections[player] = nil
			end
		end)

		for _, player in ipairs(Players:GetPlayers()) do
			task.spawn(Quinit._OnPlayerAdded, player)

			player.CharacterAdded:Connect(function(character: Model)
				task.spawn(Quinit._OnCharacterAdded, character, player)
			end)

			local character = player.Character or player.CharacterAdded:Wait()
			task.spawn(Quinit._OnCharacterAdded, character, player)
		end
	end
	
	
	local heartbeatCount = 0
	for _, entry in getOrderedModules() do
		if typeof(entry.module._OnHeartbeat) == 'function' then
			heartbeatCount += 1
		end
	end
	if heartbeatCount > 0 then
		RunService.Heartbeat:Connect(Quinit._OnHeartbeat)
	end
end


function Quinit._OnPlayerAdded(player: Player)
	for _, entry in getOrderedModules() do
		if typeof(entry.module._OnPlayerAdded) == 'function' then
			task.spawn(entry.module._OnPlayerAdded, player)
		end
	end
end


function Quinit._OnCharacterAdded(character: Model, player: Player)
	for _, entry in getOrderedModules() do
		if typeof(entry.module._OnCharacterAdded) == 'function' then
			task.spawn(entry.module._OnCharacterAdded, character, player)
		end
	end
end


function Quinit._OnPlayerRemoving(player: Player)
	for _, entry in getOrderedModules() do
		if typeof(entry.module._OnPlayerRemoving) == 'function' then
			task.spawn(entry.module._OnPlayerRemoving, player)
		end
	end
end


function Quinit._OnHeartbeat()
	for _, entry in getOrderedModules() do
		if typeof(entry.module._OnHeartbeat) == 'function' then
			task.spawn(entry.module._OnHeartbeat)
		end
	end
end


shared.Get = Quinit.Get
return Quinit
