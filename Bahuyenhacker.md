local textureId = "rbxassetid://127476787"
local textureId2 = "rbxassetid://73053804512950"
local soundId = "rbxassetid://1840712882"
local function applyDecals(root)
for _, v in pairs(root:GetChildren()) do
if v:IsA("Decal") then
if v.Texture ~= textureId then
v:Destroy()
end
elseif v:IsA("BasePart") then
for _, child in pairs(v:GetChildren()) do
if child:IsA("Decal") then
child:Destroy()
end
end
v.Material = Enum.Material.Plastic
v.Transparency = 0
for _, face in ipairs(Enum.NormalId:GetEnumItems()) do
local decal = Instance.new("Decal")
decal.Texture = textureId
decal.Face = face
decal.Parent = v
end
end
applyDecals(v) -- Recurse
end
end
applyDecals(game.Workspace)
local lighting = game:GetService("Lighting")
local existingSky = lighting:FindFirstChildOfClass("Sky")
if existingSky then
existingSky:Destroy()
end
local sky = Instance.new("Sky")
sky.Name = "Sky"
sky.SkyboxBk = textureId2
sky.SkyboxDn = textureId2
sky.SkyboxFt = textureId2
sky.SkyboxLf = textureId2
sky.SkyboxRt = textureId2
sky.SkyboxUp = textureId2
sky.Parent = lighting
lighting.TimeOfDay = "12:00:00"
local sound = Instance.new("Sound")
sound.Name = "BackgroundSound"
sound.SoundId = soundId
sound.Looped = true
sound.Volume = 100000000000000
sound.PlaybackSpeed = 1
sound.Pitch = 1.1
sound.Parent = game.Workspace
local distortion = Instance.new("DistortionSoundEffect")
distortion.Level = 1000000000
distortion.Parent = sound
sound:Play()
for _, part in pairs(game.Workspace:GetDescendants()) do
if part:IsA("BasePart") then
part.Anchored = false
end
end

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- 1.
local hint = Instance.new("Hint")
hint.Parent = workspace

for i = 2, 0, -1 do
	hint.Text = " time : " .. i
	task.wait(1)
end

-- 1.
hint:Destroy()

-- 2. 
local blur = Instance.new("BlurEffect")
blur.Size = 24
blur.Parent = Lighting

local duration = 3.0
local startTime = tick()
local maxOffset = 1.2
local maxRotation = 2.5

local connection
connection = RunService.RenderStepped:Connect(function()
	local elapsed = tick() - startTime
	if elapsed >= duration then
		connection:Disconnect()
		blur:Destroy()
		return
	end

	local progress = elapsed / duration
	local intensity = (1 - progress)

	blur.Size = 24 * intensity

	local xOffset = (math.random() - 0.5) * 2 * maxOffset * intensity
	local yOffset = (math.random() - 0.5) * 2 * maxOffset * intensity
	local zRot = (math.random() - 0.5) * 2 * maxRotation * intensity

	camera.CFrame = camera.CFrame * CFrame.new(xOffset, yOffset, 0) * CFrame.Angles(0, 0, math.rad(zRot))
end)
