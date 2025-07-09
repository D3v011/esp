local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Criação da GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ScannerCompletoGUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 600, 0, 500)
frame.Position = UDim2.new(0.5, -300, 0.5, -250)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundTransparency = 1
title.Text = "🕵️ Scanner Completo do Jogo"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.TextStrokeTransparency = 0.75

local output = Instance.new("ScrollingFrame", frame)
output.Size = UDim2.new(1, -20, 1, -60)
output.Position = UDim2.new(0, 10, 0, 45)
output.BackgroundColor3 = Color3.fromRGB(15,15,15)
output.BorderSizePixel = 0
output.CanvasSize = UDim2.new(0, 0, 4, 0)
output.ScrollBarThickness = 8

local layout = Instance.new("UIListLayout", output)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 6)

local function addText(text, color)
	local label = Instance.new("TextLabel", output)
	label.Size = UDim2.new(1, -20, 0, 22)
	label.BackgroundTransparency = 1
	label.TextColor3 = color or Color3.fromRGB(200,200,200)
	label.Font = Enum.Font.Gotham
	label.TextSize = 15
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = text
end

local function dumpChildren(obj, indent)
	indent = indent or ""
	for _, child in pairs(obj:GetChildren()) do
		addText(indent .. "- " .. child.Name .. " (" .. child.ClassName .. ")", Color3.fromRGB(180,180,180))
		dumpChildren(child, indent .. "  ")
	end
end

-- Limpa e inicia scan
output:ClearAllChildren()
addText("🚀 Iniciando scan completo do jogo...\n", Color3.fromRGB(100, 255, 100))

-- Jogadores e times
addText("== Jogadores e Times ==")
for _, player in pairs(Players:GetPlayers()) do
	addText("Jogador: " .. player.Name, Color3.fromRGB(255,255,0))
	if player.Team then
		addText("  Time: " .. player.Team.Name)
	else
		addText("  Sem time")
	end
	-- Health se possível
	if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
		local hp = player.Character:FindFirstChildOfClass("Humanoid").Health
		local maxHp = player.Character:FindFirstChildOfClass("Humanoid").MaxHealth
		addText(string.format("  Health: %.0f / %.0f", hp, maxHp))
	end
end

-- Ferramentas na mochila e character
addText("\n== Ferramentas (Backpack + Character) ==")
for _, player in pairs(Players:GetPlayers()) do
	addText("Player: " .. player.Name, Color3.fromRGB(255, 200, 0))
	-- Backpack
	if player:FindFirstChild("Backpack") then
		local bp = player.Backpack:GetChildren()
		if #bp == 0 then
			addText("  Mochila vazia")
		else
			addText("  Mochila:")
			for _, tool in pairs(bp) do
				addText("    - " .. tool.Name .. " (" .. tool.ClassName .. ")")
			end
		end
	else
		addText("  Sem mochila")
	end
	-- Character
	if player.Character then
		local toolsInChar = {}
		for _, c in pairs(player.Character:GetChildren()) do
			if c:IsA("Tool") then
				table.insert(toolsInChar, c.Name)
			end
		end
		if #toolsInChar > 0 then
			addText("  Equipado:")
			for _, tname in pairs(toolsInChar) do
				addText("    - " .. tname)
			end
		else
			addText("  Nada equipado")
		end
	else
		addText("  Sem character")
	end
end

-- Objetos no Workspace (com hierarquia)
addText("\n== Objetos no Workspace ==")
for _, obj in pairs(Workspace:GetChildren()) do
	addText(obj.Name .. " (" .. obj.ClassName .. ")", Color3.fromRGB(150, 200, 255))
	dumpChildren(obj, "  ")
end

-- Distâncias dos jogadores até o local player
addText("\n== Distância dos Jogadores ==")
local lpPos = nil
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
	lpPos = LocalPlayer.Character.HumanoidRootPart.Position
end
for _, player in pairs(Players:GetPlayers()) do
	if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and lpPos then
		local dist = (player.Character.HumanoidRootPart.Position - lpPos).Magnitude
		addText(string.format("%s está a %.2f metros", player.Name, dist), Color3.fromRGB(200, 255, 200))
	end
end

-- Itens colecionáveis - procura modelos com nome "Pickup", "Item", "Coin" (exemplo comum)
addText("\n== Itens Colecionáveis no Workspace ==")
local foundPickup = false
for _, obj in pairs(Workspace:GetChildren()) do
	if obj:IsA("Model") and (string.find(obj.Name:lower(), "pickup") or string.find(obj.Name:lower(), "item") or string.find(obj.Name:lower(), "coin")) then
		foundPickup = true
		addText(obj.Name .. " (" .. obj.ClassName .. ")", Color3.fromRGB(255, 150, 150))
	end
end
if not foundPickup then
	addText("Nenhum item colecionável encontrado", Color3.fromRGB(150,150,150))
end
