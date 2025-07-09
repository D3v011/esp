local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ScannerGUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 450, 0, 400)
frame.Position = UDim2.new(0.5, -225, 0.5, -200)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "🕵️ Scanner Automático de Dados"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local output = Instance.new("ScrollingFrame", frame)
output.Size = UDim2.new(1, -20, 1, -50)
output.Position = UDim2.new(0, 10, 0, 40)
output.BackgroundColor3 = Color3.fromRGB(20,20,20)
output.BorderSizePixel = 0
output.CanvasSize = UDim2.new(0, 0, 3, 0)
output.ScrollBarThickness = 6

local layout = Instance.new("UIListLayout", output)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 4)

-- Função para adicionar texto no output
local function addText(text)
	local label = Instance.new("TextLabel", output)
	label.Size = UDim2.new(1, -10, 0, 20)
	label.BackgroundTransparency = 1
	label.TextColor3 = Color3.fromRGB(200, 200, 200)
	label.Font = Enum.Font.Gotham
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Text = text
end

-- Limpar output e começar scan
output:ClearAllChildren()
addText("Iniciando scan do jogo...")

-- Scan jogadores e times
addText("== Jogadores e Times ==")
for _, player in pairs(Players:GetPlayers()) do
	addText("Jogador: " .. player.Name)
	if player.Team then
		addText("  Time: " .. player.Team.Name)
	else
		addText("  Sem time")
	end
end

-- Scan ferramentas dos jogadores
addText("\n== Ferramentas na mochila ==")
for _, player in pairs(Players:GetPlayers()) do
	if player:FindFirstChild("Backpack") then
		local tools = player.Backpack:GetChildren()
		if #tools == 0 then
			addText("Player: " .. player.Name .. " não tem ferramentas.")
		else
			addText("Player: " .. player.Name)
			for _, tool in pairs(tools) do
				addText("  Item: " .. tool.Name .. " | Tipo: " .. tool.ClassName)
			end
		end
	end
end

-- Scan objetos no workspace
addText("\n== Objetos no Workspace ==")
for _, obj in pairs(Workspace:GetChildren()) do
	addText("Objeto: " .. obj.Name .. " | Tipo: " .. obj.ClassName)
end

-- Scan posições dos jogadores (distância local)
addText("\n== Posições dos Jogadores ==")
local lpPos = nil
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
	lpPos = LocalPlayer.Character.HumanoidRootPart.Position
end

for _, player in pairs(Players:GetPlayers()) do
	if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and lpPos then
		local dist = (player.Character.HumanoidRootPart.Position - lpPos).Magnitude
		addText(player.Name .. " está a " .. math.floor(dist) .. " metros")
	end
end

-- Ajustar canvas
RunService.RenderStepped:Connect(function()
	local totalHeight = 0
	for _, child in pairs(output:GetChildren()) do
		if child:IsA("TextLabel") then
			totalHeight = totalHeight + child.Size.Y.Offset + layout.Padding.Offset
		end
	end
	output.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
end)
