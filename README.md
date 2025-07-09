local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Cria GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ModMenuRP"

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 300, 0, 420)
main.Position = UDim2.new(0.5, -150, 0.5, -210)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Visible = true

local UICorner = Instance.new("UICorner", main)
UICorner.CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundTransparency = 1
title.Text = "🔥 MOD MENU RP 🔥"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16

-- Fechar botão
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -35, 0, 5)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 0, 0)
close.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
close.Font = Enum.Font.GothamBold
close.TextSize = 14
close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

-- Teleporte para Hospital
local teleBtn = Instance.new("TextButton", main)
teleBtn.Position = UDim2.new(0, 20, 0, 60)
teleBtn.Size = UDim2.new(0, 260, 0, 35)
teleBtn.Text = "📍 Teleportar para Hospital"
teleBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
teleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
teleBtn.Font = Enum.Font.Gotham
teleBtn.TextSize = 14
teleBtn.MouseButton1Click:Connect(function()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-200, 5, 300)
	end
end)

-- ESP toggle
local espOn = false
local espBtn = Instance.new("TextButton", main)
espBtn.Position = UDim2.new(0, 20, 0, 110)
espBtn.Size = UDim2.new(0, 260, 0, 35)
espBtn.Text = "👀 Ativar ESP (NomexCargo)"
espBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
espBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
espBtn.Font = Enum.Font.Gotham
espBtn.TextSize = 14

local espTags = {}

espBtn.MouseButton1Click:Connect(function()
	espOn = not espOn
	espBtn.Text = espOn and "❌ Desativar ESP" or "👀 Ativar ESP (NomexCargo)"
	if not espOn then
		for _, tag in pairs(espTags) do if tag and tag.Parent then tag:Destroy() end end
		espTags = {}
	end
end)

-- Velocidade
local speedBtn = Instance.new("TextButton", main)
speedBtn.Position = UDim2.new(0, 20, 0, 160)
speedBtn.Size = UDim2.new(0, 260, 0, 35)
speedBtn.Text = "🏃 Aumentar velocidade"
speedBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
speedBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
speedBtn.Font = Enum.Font.Gotham
speedBtn.TextSize = 14

speedBtn.MouseButton1Click:Connect(function()
	local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
	if hum then
		hum.WalkSpeed = 40
	end
end)

-- Noclip toggle
local noclipOn = false
local noclipBtn = Instance.new("TextButton", main)
noclipBtn.Position = UDim2.new(0, 20, 0, 210)
noclipBtn.Size = UDim2.new(0, 260, 0, 35)
noclipBtn.Text = "🚶 Ativar Noclip"
noclipBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
noclipBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
noclipBtn.Font = Enum.Font.Gotham
noclipBtn.TextSize = 14

noclipBtn.MouseButton1Click:Connect(function()
	noclipOn = not noclipOn
	noclipBtn.Text = noclipOn and "❌ Desativar Noclip" or "🚶 Ativar Noclip"
end)

RunService.Stepped:Connect(function()
	if noclipOn and LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetChildren()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	elseif LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetChildren()) do
			if part:IsA("BasePart") then
				part.CanCollide = true
			end
		end
	end
end)

-- Invisibilidade toggle
local invisOn = false
local invisBtn = Instance.new("TextButton", main)
invisBtn.Position = UDim2.new(0, 20, 0, 260)
invisBtn.Size = UDim2.new(0, 260, 0, 35)
invisBtn.Text = "👻 Ativar Invisibilidade"
invisBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
invisBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
invisBtn.Font = Enum.Font.Gotham
invisBtn.TextSize = 14

invisBtn.MouseButton1Click:Connect(function()
	invisOn = not invisOn
	invisBtn.Text = invisOn and "❌ Desativar Invisibilidade" or "👻 Ativar Invisibilidade"
	if LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetChildren()) do
			if part:IsA("BasePart") or part:IsA("Decal") then
				part.Transparency = invisOn and 1 or 0
			end
			if part:IsA("BasePart") and part.Name == "HumanoidRootPart" then
				part.Transparency = invisOn and 1 or 0
			end
		end
	end
end)

-- Pular alto toggle
local highJumpOn = false
local jumpBtn = Instance.new("TextButton", main)
jumpBtn.Position = UDim2.new(0, 20, 0, 310)
jumpBtn.Size = UDim2.new(0, 260, 0, 35)
jumpBtn.Text = "⬆️ Ativar Pulo Alto"
jumpBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
jumpBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpBtn.Font = Enum.Font.Gotham
jumpBtn.TextSize = 14

jumpBtn.MouseButton1Click:Connect(function()
	highJumpOn = not highJumpOn
	jumpBtn.Text = highJumpOn and "❌ Desativar Pulo Alto" or "⬆️ Ativar Pulo Alto"
	if LocalPlayer.Character then
		local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum.JumpPower = highJumpOn and 100 or 50
		end
	end
end)

-- Resetar personagem
local resetBtn = Instance.new("TextButton", main)
resetBtn.Position = UDim2.new(0, 20, 0, 360)
resetBtn.Size = UDim2.new(0, 260, 0, 35)
resetBtn.Text = "🔄 Resetar Personagem"
resetBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
resetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
resetBtn.Font = Enum.Font.Gotham
resetBtn.TextSize = 14

resetBtn.MouseButton1Click:Connect(function()
	if LocalPlayer.Character then
		LocalPlayer.Character:BreakJoints()
	end
end)

-- Loop ESP
RunService.RenderStepped:Connect(function()
	if espOn then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and p.Team then
				if not espTags[p] then
					local guiTag = Instance.new("BillboardGui", p.Character.Head)
					guiTag.Name = "TeamESP"
					guiTag.Size = UDim2.new(0, 100, 0, 30)
					guiTag.StudsOffset = Vector3.new(0, 2, 0)
					guiTag.AlwaysOnTop = true
					local label = Instance.new("TextLabel", guiTag)
					label.Size = UDim2.new(1, 0, 1, 0)
					label.BackgroundTransparency = 1
					label.TextColor3 = Color3.fromRGB(0, 255, 255)
					label.Font = Enum.Font.GothamBold
					label.TextSize = 14
					label.Text = p.Name .. " [" .. p.Team.Name .. "]"
					espTags[p] = guiTag
				end
			end
		end
	end
end)
