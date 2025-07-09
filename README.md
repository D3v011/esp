local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Variáveis de controle
local espOn, noclipOn, invisOn, highJumpOn, speedOn = false, false, false, false, false

-- Função para pegar o humanoide
local function getHumanoid()
	local char = LocalPlayer.Character
	return char and char:FindFirstChildOfClass("Humanoid")
end

-- Transparência do personagem
local function setTransparencyForCharacter(char, transparency)
	for _, part in pairs(char:GetChildren()) do
		if part:IsA("BasePart") then
			part.Transparency = transparency
		elseif part:IsA("Decal") then
			part.Transparency = transparency
		elseif part:IsA("Accessory") and part:FindFirstChild("Handle") then
			part.Handle.Transparency = transparency
		end
	end
end

-- Aplicar configurações após respawn
local function applySettingsToCharacter(char)
	local hum = char:WaitForChild("Humanoid")
	hum.JumpPower = highJumpOn and 100 or 50
	hum.WalkSpeed = speedOn and 40 or 16
	for _, part in pairs(char:GetChildren()) do
		if part:IsA("BasePart") then
			part.CanCollide = not noclipOn
		end
	end
	setTransparencyForCharacter(char, invisOn and 1 or 0)
end

LocalPlayer.CharacterAdded:Connect(function(char)
	wait(0.1)
	applySettingsToCharacter(char)
end)

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "ModMenuRP"
gui.Parent = pcall(function() return game:GetService("CoreGui") end) and game.CoreGui or LocalPlayer.PlayerGui

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 300, 0, 420)
main.Position = UDim2.new(0.5, -150, 0.5, -210)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Visible = true

Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)

Instance.new("TextLabel", main) {
	Size = UDim2.new(1, 0, 0, 35),
	BackgroundTransparency = 1,
	Text = "🔥 MOD MENU RP 🔥",
	TextColor3 = Color3.fromRGB(255, 255, 255),
	Font = Enum.Font.GothamBold,
	TextSize = 16,
}

-- Fechar e resetar tudo
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -35, 0, 5)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 0, 0)
close.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
close.Font = Enum.Font.GothamBold
close.TextSize = 14
close.MouseButton1Click:Connect(function()
	espOn, noclipOn, invisOn, highJumpOn, speedOn = false, false, false, false, false
	if LocalPlayer.Character then
		applySettingsToCharacter(LocalPlayer.Character)
	end
	gui:Destroy()
end)

-- Botões (exemplo: teleporte hospital)
local function makeButton(y, text, callback)
	local btn = Instance.new("TextButton", main)
	btn.Position = UDim2.new(0, 20, 0, y)
	btn.Size = UDim2.new(0, 260, 0, 35)
	btn.Text = text
	btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.MouseButton1Click:Connect(callback)
	return btn
end

makeButton(60, "📍 Teleportar para Hospital", function()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-200, 5, 300)
	end
end)

-- ESP
local espBtn = makeButton(110, "👀 Ativar ESP (NomexCargo)", function()
	espOn = not espOn
	espBtn.Text = espOn and "❌ Desativar ESP" or "👀 Ativar ESP (NomexCargo)"
end)

-- Velocidade
local speedBtn = makeButton(160, "🏃 Aumentar velocidade", function()
	speedOn = not speedOn
	speedBtn.Text = speedOn and "❌ Resetar velocidade" or "🏃 Aumentar velocidade"
	local hum = getHumanoid()
	if hum then hum.WalkSpeed = speedOn and 40 or 16 end
end)

-- Noclip
local noclipBtn = makeButton(210, "🚶 Ativar Noclip", function()
	noclipOn = not noclipOn
	noclipBtn.Text = noclipOn and "❌ Desativar Noclip" or "🚶 Ativar Noclip"
end)

-- Invisibilidade
local invisBtn = makeButton(260, "👻 Ativar Invisibilidade", function()
	invisOn = not invisOn
	invisBtn.Text = invisOn and "❌ Desativar Invisibilidade" or "👻 Ativar Invisibilidade"
	if LocalPlayer.Character then setTransparencyForCharacter(LocalPlayer.Character, invisOn and 1 or 0) end
end)

-- Pulo alto
local jumpBtn = makeButton(310, "⬆️ Ativar Pulo Alto", function()
	highJumpOn = not highJumpOn
	jumpBtn.Text = highJumpOn and "❌ Desativar Pulo Alto" or "⬆️ Ativar Pulo Alto"
	local hum = getHumanoid()
	if hum then hum.JumpPower = highJumpOn and 100 or 50 end
end)

makeButton(360, "🔄 Resetar Personagem", function()
	if LocalPlayer.Character then LocalPlayer.Character:BreakJoints() end
end)

-- Noclip loop contínuo
RunService.Stepped:Connect(function()
	if noclipOn and LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetChildren()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end
end)

-- ESP otimizado
local espTags = {}
RunService.RenderStepped:Connect(function()
	if not espOn then
		for p, tag in pairs(espTags) do
			if tag and tag.Parent then tag:Destroy() end
			espTags[p] = nil
		end
	else
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
			elseif espTags[p] then
				if espTags[p].Parent then espTags[p]:Destroy() end
				espTags[p] = nil
			end
		end
	end
end)
