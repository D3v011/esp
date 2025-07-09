local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Criação da GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ScannerLeveGUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 450, 0, 400)
frame.Position = UDim2.new(0.5, -225, 0.5, -200)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "🕵️ Scanner Leve de Dados"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local output = Instance.new("ScrollingFrame", frame)
output.Size = UDim2.new(1, -20, 1, -50)
output.Position = UDim2.new(0, 10, 0, 40)
output.BackgroundColor3 = Color3.fromRGB(20,20,20)
output.BorderSizePixel = 0
output.CanvasSize = UDim2.new(0, 0, 0, 0)
output.ScrollBarThickness = 6

local layout = Instance.new("UIListLayout", output)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 4)

local function addText(text, color)
	local label = Instance.new("TextLabel", output)
	label.Size = UDim2.new(1, -10, 0, 20)
	label.BackgroundTransparency = 1
	label.TextColor3 = color or Color3.fromRGB(200, 200, 200)
	label.Font = Enum.Font.Gotham
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = text
	return label
end

output:ClearAllChildren()
addText("Iniciando scan leve do jogo...", Color3.fromRGB(100, 255, 100))

local function updateCanvas()
	local totalHeight = 0
	for _, child in pairs(output:GetChildren()) do
		if child:IsA("TextLabel") then
			totalHeight = totalHeight + child.Size.Y.Offset + layout.Padding.Offset
		end
	end
	output.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
end

-- Use coroutine para distribuir as operações e evitar travar
coroutine.wrap(function()
	addText("== Jogadores e Times ==")
	updateCanvas()
	wait(0.2)
	for _, player in pairs(Players:GetPlayers()) do
		addText("Jogador: " .. player.Name, Color3.fromRGB(255,255,0))
		updateCanvas()
		wait(0.05)
		if player.Team then
			addText("  Time: " .. player.Team.Name)
		else
			addText("  Sem time")
		end
		updateCanvas()
		wait(0.05)
	end
	
	wait(0.3)
	addText("\n== Ferramentas na mochila ==")
	updateCanvas()
	wait(0.2)
	for _, player in pairs(Players:GetPlayers()) do
		local backpack = player:FindFirstChild("Backpack")
		if backpack then
			local items = backpack:GetChildren()
			addText("Player: " .. player.Name .. " tem " .. tostring(#items) .. " ferramentas")
		else
			addText("Player: " .. player.Name .. " não tem mochila")
		end
		updateCanvas()
		wait(0.05)
	end
	
	wait(0.3)
	addText("\n== Objetos no Workspace ==")
	updateCanvas()
	wait(0.2)
	local children = Workspace:GetChildren()
	local limit = math.min(#children, 30) -- Limita a 30 objetos para evitar travar
	for i=1, limit do
		local obj = children[i]
		addText("Objeto: " .. obj.Name .. " | Tipo: " .. obj.ClassName)
		updateCanvas()
		wait(0.03)
	end
	
	wait(0.3)
	addText("\n== Distâncias dos jogadores ==")
	updateCanvas()
	wait(0.2)
	local lpPos
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		lpPos = LocalPlayer.Character.HumanoidRootPart.Position
	end
	for _, player in pairs(Players:GetPlayers()) do
		if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and lpPos then
			local dist = (player.Character.HumanoidRootPart.Position - lpPos).Magnitude
			addText(player.Name .. " está a " .. string.format("%.1f", dist) .. " metros")
			updateCanvas()
			wait(0.05)
		end
	end
end)()
