-- [LocalScript] coloque em StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

----------------------------------------------------------------
-- ANTI RAGDOLL (JUNTADO DO MODULESCRIPT)
----------------------------------------------------------------
local AntiRagdoll = {}
local antiEnabled = false

function AntiRagdoll.Enable(char)
	if not char then return end
	antiEnabled = true
	for _, v in pairs(char:GetDescendants()) do
		if v:IsA("BallSocketConstraint") or v:IsA("HingeConstraint") then
			v.Enabled = false
		end
	end
end

function AntiRagdoll.Disable(char)
	if not char then return end
	antiEnabled = false
	for _, v in pairs(char:GetDescendants()) do
		if v:IsA("BallSocketConstraint") or v:IsA("HingeConstraint") then
			v.Enabled = true
		end
	end
end

function AntiRagdoll.Toggle(char)
	if antiEnabled then
		AntiRagdoll.Disable(char)
	else
		AntiRagdoll.Enable(char)
	end
end

----------------------------------------------------------------
-- GUI
----------------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AntiRagdollHub"
ScreenGui.Parent = player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 230, 0, 80)
Frame.Position = UDim2.new(0.5, -115, 0.1, 0)
Frame.BackgroundColor3 = Color3.new(0,0,0)
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui

local UiCorner = Instance.new("UICorner")
UiCorner.CornerRadius = UDim.new(0,14)
UiCorner.Parent = Frame

local UiStroke = Instance.new("UIStroke")
UiStroke.Thickness = 3
UiStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
UiStroke.Parent = Frame

-- RGB Border
local function rgbRainbow()
	local t = tick()
	return Color3.new(
		math.abs(math.sin(t)),
		math.abs(math.sin(t + 2)),
		math.abs(math.sin(t + 4))
	)
end

RunService.RenderStepped:Connect(function()
	UiStroke.Color = rgbRainbow()
end)

local title = Instance.new("TextLabel")
title.Text = "AntiRagdoll Hub"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1
title.Size = UDim2.new(1, 0, 0, 28)
title.Parent = Frame

local toggle = Instance.new("TextButton")
toggle.Font = Enum.Font.GothamSemibold
toggle.TextSize = 18
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.BackgroundColor3 = Color3.fromRGB(30,30,30)
toggle.Size = UDim2.new(0.8, 0, 0, 32)
toggle.Position = UDim2.new(0.1, 0, 0.5, 0)
toggle.Parent = Frame

local UiCornerBtn = Instance.new("UICorner")
UiCornerBtn.Parent = toggle

----------------------------------------------------------------
-- BOTÃO
----------------------------------------------------------------
local function updateBtn()
	if antiEnabled then
		toggle.Text = "Desativar AntiRagdoll"
		toggle.BackgroundColor3 = Color3.fromRGB(18,150,43)
	else
		toggle.Text = "Ativar AntiRagdoll"
		toggle.BackgroundColor3 = Color3.fromRGB(30,30,30)
	end
end

toggle.MouseButton1Click:Connect(function()
	local char = player.Character or player.CharacterAdded:Wait()
	AntiRagdoll.Toggle(char)
	updateBtn()
end)

----------------------------------------------------------------
-- RESPAWN
----------------------------------------------------------------
player.CharacterAdded:Connect(function(char)
	task.wait(0.2)
	if antiEnabled then
		AntiRagdoll.Enable(char)
	end
end)

updateBtn()
