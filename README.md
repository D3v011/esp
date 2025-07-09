-- Painel Mod Menu RP (Menu Flutuante com vantagens discretas) por ChatGPT

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Cria GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ModMenuRP"

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 300, 0, 350)
main.Position = UDim2.new(0.5, -150, 0.5, -175)
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

-- Teleporte
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
