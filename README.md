local Doors = {   
    ['MenuColor'] = Color3.fromRGB(255, 255, 255),  
    ['NotifyColor'] = Color3.fromRGB(255, 255, 255),  
    ['MainColor'] = Color3.fromRGB(90, 90, 90),  
    ['AccentColor'] = Color3.fromRGB(255, 255, 255),  
    ['ErrorColor'] = Color3.fromRGB(255, 0, 0),  
    ['Prefix'] = ";",  
    ['Autor'] = "Andrey",  
    ['Version'] = "1.0"  
}

function Notification(Text, Time)  
    local NotificationMain = Instance.new("ScreenGui")  
    local FrameMain = Instance.new("Frame")  
    local TextLabel = Instance.new("TextLabel")  
    local UICorner = Instance.new("UICorner")  

    NotificationMain.Name = "NotificationMain"  
    NotificationMain.Parent = game:GetService("CoreGui")  
    NotificationMain.ZIndexBehavior = Enum.ZIndexBehavior.Sibling  

    FrameMain.Name = "FrameMain"  
    FrameMain.Parent = NotificationMain  
    FrameMain.AnchorPoint = Vector2.new(0.5, 0.5)  
    FrameMain.BackgroundColor3 = Doors['NotifyColor']  
    FrameMain.BorderColor3 = Doors['MainColor']  
    FrameMain.BorderSizePixel = 3  
    FrameMain.Position = UDim2.new(0.5, 0, 0.93, 0)  
    FrameMain.Size = UDim2.new(0, 350, 0, 45)  

    TextLabel.Parent = FrameMain  
    TextLabel.AnchorPoint = Vector2.new(0.5, 0.5)  
    TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)  
    TextLabel.BackgroundTransparency = 1.000  
    TextLabel.BorderSizePixel = 0  
    TextLabel.Position = UDim2.new(0.5, 0, 0.5, 0)  
    TextLabel.Size = UDim2.new(1, 0, 1, 0)  
    TextLabel.Font = Enum.Font.SourceSansBold  
    TextLabel.Text = Text  
    TextLabel.TextColor3 = Doors['MainColor']  
    TextLabel.TextScaled = true  
    TextLabel.TextSize = 14.000  
    TextLabel.TextWrapped = true  

    UICorner.CornerRadius = UDim.new(0, 5)  
    UICorner.Parent = FrameMain  

    wait(Time)  
    NotificationMain:Destroy()  
end

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Camera = workspace.CurrentCamera

function GetCharacter()
    return LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
end

function GetHumanoid()
    return GetCharacter():WaitForChild("Humanoid")
end

function GetRootPart()
    return GetCharacter():WaitForChild("HumanoidRootPart")
end

function GetMouseHit()
    local unitRay = Camera:ScreenPointToRay(Mouse.X, Mouse.Y, 0)
    local part, position = workspace:FindPartOnRay(unitRay, LocalPlayer.Character, false, true)
    return part, position, unitRay
end
function ESP(v) 
    local EspPart = Instance.new("BoxHandleAdornment", v) 
    EspPart.AlwaysOnTop = true 
    EspPart.ZIndex = 1 
    EspPart.Adornee = v 
    EspPart.Size = v.Size + Vector3.new(0.1, 0.1, 0.1) 
    EspPart.Color3 = Doors['AccentColor'] 
    EspPart.Transparency = 0.6 
    EspPart.Visible = true 
end

function GetPlayerFromCharacter(character)
    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character == character then
            return player
        end
    end
end

function GetPlayerFromName(name)
    for _, player in ipairs(Players:GetPlayers()) do
        if player.Name:lower():find(name:lower()) then
            return player
        end
    end
end

function IsAlive(character)
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    return humanoid and humanoid.Health > 0
end

function GetAlivePlayers()
    local players = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character and IsAlive(player.Character) then
            table.insert(players, player)
        end
    end
    return players
end

function GetDeadPlayers()
    local players = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character and not IsAlive(player.Character) then
            table.insert(players, player)
        end
    end
    return players
end

function GetEnemies()
    local enemies = {}
    for _, player in ipairs(GetAlivePlayers()) do
        if player.Team ~= LocalPlayer.Team then
            table.insert(enemies, player)
        end
    end
    return enemies
end

function GetFriends()
    local friends = {}
    for _, player in ipairs(GetAlivePlayers()) do
        if player.Team == LocalPlayer.Team and player ~= LocalPlayer then
            table.insert(friends, player)
        end
    end
    return friends
end

function GetItems()
    local items = {}
    for _, item in ipairs(workspace:GetChildren()) do
        if item:IsA("Tool") or item:IsA("HopperBin") then
            table.insert(items, item)
        end
    end
    return items
end

function TeleportPlayer(character, position)
    local rootPart = character:WaitForChild("HumanoidRootPart")
    rootPart.CFrame = CFrame.new(position)
end

function NoClip()
    local noclip = false
    game:GetService("RunService").Stepped:Connect(function()
        if noclip then
            LocalPlayer.Character.Humanoid:ChangeState(11)
        end
    end)
    local function toggleNoclip()
        noclip = not noclip
        Notification("Noclip " .. (noclip and "Enabled" or "Disabled"), 2)
    end
    return toggleNoclip
end

function SetWalkSpeed(speed)
    GetHumanoid().WalkSpeed = speed
end

function SetJumpPower(power)
    GetHumanoid().JumpPower = power
end

function SetGravity(gravity)
    workspace.Gravity = gravity
end

function SetFov(fov)
    Camera.FieldOfView = fov
end

function GodMode(enable)
    local function enableGodMode()
        local character = GetCharacter()
        local humanoid = GetHumanoid()
        local oldState = humanoid:GetState()
        humanoid:ChangeState(Enum.HumanoidStateType.Physics)
       
    character:WaitForChild("HumanoidRootPart").Anchored = true
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Dead, false)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.PlatformStanding, false)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Swimming, false)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Freefall, false)
    Notification("Godmode Enabled", 2)
end

local function disableGodMode()
    local character = GetCharacter()
    character:WaitForChild("HumanoidRootPart").Anchored = false
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Dead, true)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.PlatformStanding, true)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Swimming, true)
    character:WaitForChild("Humanoid"):SetStateEnabled(Enum.HumanoidStateType.Freefall, true)
    Notification("Godmode Disabled", 2)
end

if enable then
    enableGodMode()
else
    disableGodMode()
end

return disableGodMode
end

function Fly()
local canFly = false
local speed = 2
local keysPressed = {}
local flying = false
local function start()
    local character = GetCharacter()
    if not character then return end
    local humanoid = GetHumanoid()
    if not humanoid then return end

    local mouse = LocalPlayer:GetMouse()

    local function updateFly()
        local forward, side = 0, 0
        if keysPressed['w'] then
            forward = forward + 1
        end
        if keysPressed['a'] then
            side = side - 1
        end
        if keysPressed['s'] then
            forward = forward - 1
        end
        if keysPressed['d'] then
            side = side + 1
        end
        local direction = (Camera.CFrame * CFrame.new(Vector3.new(side, 0, forward))).p - Camera.CFrame.p
        humanoid:MoveTo(character.PrimaryPart.Position + direction * speed)
    end

    game:GetService('RunService').RenderStepped:Connect(function()
        if flying then
            updateFly()
        end
    end)

    mouse.KeyDown:Connect(function(key)
        if key == 'f' then
            if not flying then
                flying = true
                Notification("Fly Enabled", 2)
            else
                flying = false
                Notification("Fly Disabled", 2)
            end
        end
        if flying and key == 'e' then
            speed = speed + 1
            Notification("Speed: " .. speed, 2)
        end
        if flying and key == 'q' then
            speed = math.max(speed - 1, 0)
           
            Notification("Speed: " .. speed, 2)
        end
        keysPressed[key] = true
    end)

    mouse.KeyUp:Connect(function(key)
        keysPressed[key] = nil
    end)
end

if enable then
    canFly = true
    start()
end

return function()
    canFly = false
    flying = false
end
end

function NoClip()
local enabled = false
local function start()
    local character = GetCharacter()
    if not character then return end

    local root = character:WaitForChild("HumanoidRootPart")
    local primaryPart = character.PrimaryPart

    root.Anchored = true

    for _, child in ipairs(character:GetChildren()) do
        if child:IsA("BasePart") and child ~= root then
            child.CanCollide = false
        end
    end

    local function update()
        if not enabled then return end

        local delta = primaryPart.Position - root.Position
        root.CFrame = root.CFrame + delta
    end

    game:GetService("RunService").RenderStepped:Connect(update)
end

if enable then
    enabled = true
    start()
end

return function()
    enabled = false

    local character = GetCharacter()
    if not character then return end

    local root = character:WaitForChild("HumanoidRootPart")

    root.Anchored = false

    for _, child in ipairs(character:GetChildren()) do
        if child:IsA("BasePart") and child ~= root then
            child.CanCollide = true
        end
    end
end
end

function Kill()
local character = GetCharacter()
if not character then return end
local humanoid = GetHumanoid()
if not humanoid then return end

humanoid.Health = 0

Notification("You have been killed", 2)
end

function Crash()
while true do
wait()
end
end

function RemoveLimbs()
local character = GetCharacter()
if not character then return end
for _, limb in ipairs(character:GetChildren()) do
    if limb:IsA("BasePart") and not limb:IsA("HumanoidRootPart") then
        limb:Destroy()
    end
end

Notification("Limbs Removed", 2)
end

function RemoveTorso()
local character = GetCharacter()
if not character then return end
character:WaitForChild("Head"):Destroy()

Notification("Head Removed", 2)
end

function RemoveArms()
local character = GetCharacter()
if not character then return end
for _, arm in ipairs({ "LeftArm", "RightArm" }) do
    character:WaitForChild(arm):Destroy()
end

Notification("Arms Removed", 2)
end

function RemoveLegs()
local character = GetCharacter()
if not character then return end
for _, leg in ipairs({ "LeftLeg", "RightLeg" }) do
    character:WaitForChild(leg):Destroy()
end

Notification("Legs Removed", 2)
end

function RemoveAll()
RemoveLimbs()
RemoveTorso()
RemoveHead()
RemoveArms()
RemoveLegs()
end

function DeleteMap()
for _, obj in ipairs(workspace:GetChildren()) do
if obj:IsA("Model") then
obj:Destroy()
end
end
Notification("Map Deleted", 2)
end

function SetWalkSpeed(value)
local humanoid = GetHumanoid()
if not humanoid then return end
humanoid.WalkSpeed = value

Notification("WalkSpeed set to " .. value, 2)
end

function SetJumpPower(value)
local humanoid = GetHumanoid()
if not humanoid then return end
humanoid.JumpPower = value

Notification("JumpPower set to " .. value, 2)
end

function SetGravity(value)
workspace.Gravity = value
Notification("Gravity set to " .. value, 2)
end

function SetFogEnd(value)
game:GetService("Lighting").FogEnd = value
Notification("FogEnd set to " .. value, 2)
end

function SetTimeOfDay(value)
game:GetService("Lighting").TimeOfDay = value
Notification("TimeOfDay set to " .. value, 2)
end

function RemoveDoors()
for _, obj in ipairs(workspace:GetChildren()) do
if obj:IsA("Model") and obj.Name == "Doors" then
obj:Destroy()
end
end
Notification("Doors Removed", 2)
end

function GodMode()
local character = GetCharacter()
if not character then return end
local humanoid = GetHumanoid()
if not humanoid then return end

local oldHealth = humanoid.Health

humanoid.Health = math.huge

while true do
    wait()

    if humanoid.Health ~= math.huge then
        humanoid.Health = math.huge
    end

    if humanoid.Health ~= oldHealth then
        humanoid.Health = oldHealth
    end
end
end

function SetPlayerSpeed(value)
speed = value
Notification("Speed set to " .. value, 2)
end

function ToggleWalkSpeed(enable)
local humanoid = GetHumanoid()
if not humanoid then return end
if enable then
    walkSpeed = humanoid.WalkSpeed
    SetWalkSpeed(speed)
else
    SetWalkSpeed(walkSpeed)
end
end

function ToggleJumpPower(enable)
local humanoid = GetHumanoid()
if not humanoid then return end
if enable then
    jumpPower = humanoid.JumpPower
    SetJumpPower(speed)
else
    SetJumpPower(jumpPower)
end
end

function ToggleGravity(enable)
if enable then
gravity = workspace.Gravity
SetGravity(0)
else
SetGravity(gravity)
end
end

function ToggleFogEnd(enable)
if enable then
fogEnd = game:GetService("Lighting").FogEnd
SetFogEnd(0)
else
SetFogEnd(fogEnd)
end
end
function ToggleTimeOfDay(enable)
if enable then
timeOfDay = game:GetService("Lighting").TimeOfDay
SetTimeOfDay("14:00:00")
else
SetTimeOfDay(timeOfDay)
end
end

function ToggleGodMode(enable)
if enable then
godMode = coroutine.wrap(GodMode)
godMode()
Notification("GodMode enabled", 2)
else
if godMode then
godMode()
godMode = nil
end
    Notification("GodMode disabled", 2)
end
end

function ToggleNoclip(enable)
if enable then
noclip = coroutine.wrap(NoClip)
noclip()
Notification("Noclip enabled", 2)
else
    if noclip then
        noclip()
        noclip = nil
    end

    Notification("Noclip disabled", 2)
end
end

function GetPlayers()
local players = {}
for _, player in ipairs(game:GetService("Players"):GetPlayers()) do
    if player.Character then
        table.insert(players, player)
    end
end

return players
end

function NotifyPlayer(player, message, time)
game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("SayMessageRequest"):FireServer(
message,
"All",
false,
player.Name
)
wait(time)

game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("SayMessageRequest"):FireServer(
    "",
    "All",
    false,
    player.Name
)
end

function NotifyPlayers(message, time)
for _, player in ipairs(GetPlayers()) do
NotifyPlayer(player, message, time)
end
end

function Kill(player)
if not player.Character then return end
local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
if not humanoid then return end

humanoid.Health = 0
end

function Kick(player, message)
game:GetService("Players"):KickPlayer(player, message)
end

function Ban(player, message)
game:GetService("Players"):Ban(player, message)
end

function ServerHop()
game:GetService("TeleportService"):Teleport(game.PlaceId)
end

-- Keybinds

game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
if gameProcessed then return end
if input.KeyCode == Enum.KeyCode.F then
    ToggleFly(not flyEnabled)
elseif input.KeyCode == Enum.KeyCode.X then
    ToggleNoclip(not noclipEnabled)
elseif input.KeyCode == Enum.KeyCode.Q then
    ToggleGodMode(not godModeEnabled)
elseif input.KeyCode == Enum.KeyCode.R then
    RemoveMap()
elseif input.KeyCode == Enum.KeyCode.V then
    RemoveDoors()
elseif input.KeyCode == Enum.KeyCode.J then
    ToggleJumpPower(not jumpPowerEnabled)
elseif input.KeyCode == Enum.KeyCode.W then
    ToggleWalkSpeed(not walkSpeedEnabled)
elseif input.KeyCode == Enum.KeyCode.G then
    ToggleGravity(not gravityEnabled)
elseif input.KeyCode == Enum.KeyCode.T then
    ToggleTimeOfDay(not timeOfDayEnabled)
elseif input.KeyCode == Enum.KeyCode.K then
    Kill(game.Players.LocalPlayer)
elseif input.KeyCode == Enum.KeyCode.B then
    Ban(game.Players.LocalPlayer, "Banned by NeRD")
elseif input.KeyCode == Enum.KeyCode.H then
    ServerHop()
elseif input.KeyCode == Enum.KeyCode.P then
    NotifyPlayers("Hello, I'm using NeRD's hack.", 3)
end
end)
AddCommand("fly", "Toggle fly mode", function(args)
ToggleFly(not flyEnabled)
end)

AddCommand("noclip", "Toggle noclip mode", function(args)
ToggleNoclip(not noclipEnabled)
end)

AddCommand("god", "Toggle god mode", function(args)
ToggleGodMode(not godModeEnabled)
end)

AddCommand("remove_map", "Remove the entire map", function(args)
RemoveMap()
end)

AddCommand("remove_doors", "Remove all doors", function(args)
RemoveDoors()
end)

AddCommand("jump_power", "Change jump power", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: jump_power <value>", 2)
return
end

local value = tonumber(args[1])
if not value then
    Notification("Invalid value.", 2)
    return
end

ToggleJumpPower(true)
JumpPower(value)
end)

AddCommand("walk_speed", "Change walk speed", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: walk_speed <value>", 2)
return
end
local value = tonumber(args[1])
if not value then
    Notification("Invalid value.", 2)
    return
end

ToggleWalkSpeed(true)
WalkSpeed(value)
end)

AddCommand("gravity", "Change gravity", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: gravity <value>", 2)
return
end
local value = tonumber(args[1])
if not value then
    Notification("Invalid value.", 2)
    return
end

ToggleGravity(true)
Gravity(value)
end)

AddCommand("time_of_day", "Change time of day", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: time_of_day <value>", 2)
return
end
local value = tonumber(args[1])
if not value then
    Notification("Invalid value.", 2)
    return
end

ToggleTimeOfDay(true)
TimeOfDay(value)
end)

AddCommand("kill", "Kill a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: kill <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

Kill(player)
end)

AddCommand("kick", "Kick a player", function(args)
if #args < 1 then
Notification("Invalid arguments. Syntax: kick <player> [reason]", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

table.remove(args, 1)
local reason = table.concat(args, " ")
if reason == "" then reason = nil end

Kick(player, reason)
end)

AddCommand("ban", "Ban a player", function(args)
if #args < 1 then
Notification("Invalid arguments. Syntax: ban <player> [reason]", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

table.remove(args, 1)
local reason = table.concat(args, " ")
if reason == "" then     reason = nil
end

Ban(player, reason)
end)

AddCommand("unban", "Unban a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: unban <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

Unban(player)
end)

AddCommand("tp", "Teleport to a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: tp <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

Teleport(player)
end)

AddCommand("tp_to_me", "Teleport a player to you", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: tp_to_me <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

TeleportToMe(player)
end)

AddCommand("tp_to", "Teleport a player to another player", function(args)
if #args ~= 2 then
Notification("Invalid arguments. Syntax: tp_to <player> <destination>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

local destination = GetPlayer(args[2])
if not destination then
    Notification("Destination player not found.", 2)
    return
end

TeleportTo(player, destination)
end)

AddCommand("tp_to_pos", "Teleport to a position", function(args)
if #args ~= 3 then
Notification("Invalid arguments. Syntax: tp_to_pos <x> <y> <z>", 2)
return
end
local x = tonumber(args[1])
local y = tonumber(args[2])
local z = tonumber(args[3])
if not x or not y or not z then
    Notification("Invalid position.", 2)
    return
end

TeleportToPos(x, y, z)
end)

AddCommand("bring", "Bring a player to you", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: bring <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

Bring(player)
end)

AddCommand("spawn_car", "Spawn a car", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: spawn_car <model>", 2)
return
end
local model = tonumber(args[1])
if not model then
    Notification("Invalid model.", 2)
    return
end

SpawnCar(model)
end)

AddCommand("spawn_ped", "Spawn a pedestrian", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: spawn_ped <model>", 2)
return
end
local model = tonumber(args[1])
if not model then
    Notification("Invalid model.", 2)
    return
end

SpawnPed(model)
end)

AddCommand("weapon", "Give a weapon to the player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: weapon <weapon>", 2)
return
end
local weapon = args[1]
if not IsValidWeapon(weapon) then
    Notification("Invalid weapon.", 2)
    return
end

GiveWeapon(weapon)
end)

AddCommand("ammo", "Give ammo to the player's current weapon", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: ammo <amount>", 2)
return
end
local amount = tonumber(args[1])
if not amount or amount < 0 then
    Notification("Invalid amount.", 2)
    return
end

GiveAmmo(amount)
end)

AddCommand("heal", "Heal the player", function(args)
Heal()
end)

AddCommand("armor", "Give armor to the player", function(args)
GiveArmor()
end)

AddCommand("god", "Enable or disable god mode for the player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: god <on/off>", 2)
return
end
local on = args[1] == "on"
local off = args[1] == "off"
if not on and not off then
    Notification("Invalid argument. Syntax: god <on/off>", 2)
    return
end

SetGodMode(on)
end)

AddCommand("invisible", "Make the player invisible", function(args)
SetInvisible(true)
end)

AddCommand("visible", "Make the player visible", function(args)
SetInvisible(false)
end)

AddCommand("spectate", "Spectate a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: spectate <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

Spectate(player)
end)

AddCommand("stop_spectating", "Stop spectating", function(args)
StopSpectating()
end)

AddCommand("freeze", "Freeze or unfreeze a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: freeze <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

FreezePlayer(player)
end)

AddCommand("unfreeze", "Unfreeze a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: unfreeze <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

UnfreezePlayer(player)
end)

AddCommand("kill", "Kill a player", function(args)
if #args ~= 1 then
Notification("Invalid arguments. Syntax: kill <player>", 2)
return
end
local player = GetPlayer(args[1])
if not player then
    Notification("Player not found.", 2)
    return
end

KillPlayer(player)
end)

AddCommand("revive", "Revive a player", function(args)
if #args ~= 1 then



local function HandleTPW()
    tpPart = tpPart + 1
    if tpPart == 1 then
        tp = CFrame.new(-425.362, 11.202, 19.236)
        camCframe = CFrame.new(-426.317, 11.202, 18.924)
        wait()
    elseif tpPart == 2 then
        tp = CFrame.new(-426.199, 11.202, 26.039)
        camCframe = CFrame.new(-426.199, 11.202, 27.039)
        wait()
    elseif tpPart == 3 then
        tp = CFrame.new(-426.416, 11.202, 33.033)
        camCframe = CFrame.new(-426.416, 11.202, 34.033)
        wait()
    elseif tpPart == 4 then
        tp = CFrame.new(-426.636, 11.202, 40.027)
        camCframe = CFrame.new(-426.636, 11.202, 41.027)
        wait()
    elseif tpPart == 5 then
        tp = CFrame.new(-426.854, 11.202, 47.021)
        camCframe = CFrame.new(-426.854, 11.202, 48.021)
        wait()
    elseif tpPart == 6 then
        tp = CFrame.new(-426.963, 11.202, 53.815)
        camCframe = CFrame.new(-426.963, 11.202, 54.815)
        wait()
    elseif tpPart == 7 then
        tp = CFrame.new(-426.969, 11.202, 60.809)
        camCframe = CFrame.new(-426.969, 11.202, 61.809)
        wait()
    elseif tpPart == 8 then
        tp = CFrame.new(-426.939, 11.202, 67.803)
        camCframe = CFrame.new(-426.939, 11.202, 68.803)
        wait()
    elseif tpPart == 9 then
        tp = CFrame.new(-426.91, 11.202, 74.797)
        camCframe = CFrame.new(-426.91, 11.202, 75.797)
        wait()
    elseif tpPart == 10 then
        tp = CFrame.new(-426.822, 11.202, 81.79)
        camCframe = CFrame.new(-426.822, 11.202, 82.79)
        wait()
    elseif tpPart == 11 then
        tp = CFrame.new(-427.12, 11.202, 88.785)
        camCframe = CFrame.new(-427.12, 11.202, 89.785)
        wait()
    elseif tpPart == 12 then
        tp = CFrame.new(-425.347, 11.202, 95.47)
        camCframe = CFrame.new(-424.377, 11.202, 95.749)
        wait()
elseif tpPart == 13 then
    tp = CFrame.new(-418.353, 11.202, 95.224)
    camCframe = CFrame.new(-417.365, 11.036, 94.887)
elseif tpPart == 14 then
    tp = CFrame.new(-411.618, 11.202, 81.484)
    camCframe = CFrame.new(-411.634, 11.036, 80.511)
elseif tpPart == 15 then
    tp = CFrame.new(-424.618, 11.202, 81.484)
    camCframe = CFrame.new(-424.634, 11.036, 80.511)
elseif tpPart == 16 then
    tp = CFrame.new(-397.213, 10.830, 66.746)
    camCframe = CFrame.new(-396.695, 10.521, 67.117)
elseif tpPart == 17 then
    tp = CFrame.new(-397.226, 11.202, 54.246)
    camCframe = CFrame.new(-396.656, 11.036, 54.122)
elseif tpPart == 18 then
    tp = CFrame.new(-397.226, 11.202, 41.746)
    camCframe = CFrame.new(-396.656, 11.036, 41.622)
    
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

local tpPart = nil
local tp = nil
local camCframe = nil

local function GetClosestTPPart()
    local closestDist = math.huge
    local closestPart = nil
    for _,part in ipairs(game:GetService("Workspace").Doors:GetChildren()) do
        if part.Name == "TeleportPart" then
            local dist = (part.Position - RootPart.Position).Magnitude
            if dist < closestDist then
                closestDist = dist
                closestPart = part
            end
        end
    end
    return closestPart
end

local function GetCamPos()
    local cam = workspace.CurrentCamera
    return cam.CFrame.p, cam.Focus.p
end

local function Teleport()
    if tpPart and tp and camCframe then
        RootPart.CFrame = tp
        workspace.CurrentCamera.CFrame = camCframe
    end
end

while wait(0.1) do
    tpPart = GetClosestTPPart()
    if tpPart then
        tp = tpPart.CFrame
        local camPos, camFocus = GetCamPos()
        camCframe = CFrame.lookAt(camPos, camFocus)
    end
end

Humanoid.Changed:Connect(function(property)
    if property == "WalkSpeed" then
        Humanoid.WalkSpeed = 16
    end
end)

Mouse.Button1Down:Connect(function()
    Teleport()
end)

Humanoid.WalkSpeed = 16

print("Doors Hack loaded!")
