-- Serviços
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Estados
local ESP_On = false
local HighlightItems = false
local PullItems = false

-- Tabela
local ESP_Boxes = {}
local ItemHighlights = {}

-- GUI
local GUI = Instance.new("ScreenGui", game.CoreGui)
GUI.Name = "RP_TestGUI"

local Frame = Instance.new("Frame", GUI)
Frame.Position = UDim2.new(0, 10, 0, 100)
Frame.Size = UDim2.new(0, 220, 0, 180)
Frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
Frame.BorderSizePixel = 0

local function criarBotao(nome, posY, callback)
	local btn = Instance.new("TextButton", Frame)
	btn.Size = UDim2.new(1, -20, 0, 30)
	btn.Position = UDim2.new(0, 10, 0, posY)
	btn.Text = nome
	btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
	btn.TextColor3 = Color3.fromRGB(255,255,255)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- ESP
function criarESP(player)
	if player == LocalPlayer then return end
	local esp = Drawing.new("Text")
	esp.Size = 14
	esp.Center = true
	esp.Outline = true
	esp.Visible = false
	esp.Color = Color3.fromRGB(255, 255, 255)
	ESP_Boxes[player] = esp
end

function removerESP(player)
	if ESP_Boxes[player] then
		ESP_Boxes[player]:Remove()
		ESP_Boxes[player] = nil
	end
end

-- Loop ESP
RunService.RenderStepped:Connect(function()
	if ESP_On then
		for _, player in pairs(Players:GetPlayers()) do
			local char = player.Character
			local esp = ESP_Boxes[player]
			if char and char:FindFirstChild("HumanoidRootPart") and esp then
				local pos, vis = Workspace.CurrentCamera:WorldToViewportPoint(char.HumanoidRootPart.Position)
				esp.Position = Vector2.new(pos.X, pos.Y)
				esp.Text = player.Name
				esp.Visible = vis
			elseif esp then
				esp.Visible = false
			end
		end
	else
		for _, esp in pairs(ESP_Boxes) do
			esp.Visible = false
		end
	end
end)

-- Highlight Itens
function atualizarItens()
	for _, h in pairs(ItemHighlights) do
		h:Destroy()
	end
	ItemHighlights = {}

	for _, obj in pairs(Workspace:GetDescendants()) do
		if obj:IsA("Tool") or obj.Name:lower():match("arma") or obj.Name:lower():match("item") then
			local hl = Instance.new("Highlight", obj)
			hl.FillColor = Color3.fromRGB(0,255,0)
			hl.FillTransparency = 0.5
			table.insert(ItemHighlights, hl)
		end
	end
end

-- Teleportar Itens
function puxarItens()
	for _, obj in pairs(Workspace:GetDescendants()) do
		if obj:IsA("Tool") or obj.Name:lower():match("arma") or obj.Name:lower():match("item") then
			if obj:FindFirstChild("Handle") then
				obj.Handle.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0,3,0)
			end
		end
	end
end

-- GUI Botões
local btn1 = criarBotao("ESP Jogadores", 10, function()
	ESP_On = not ESP_On
	btn1.Text = ESP_On and "Desativar ESP" or "ESP Jogadores"
end)

local btn2 = criarBotao("Destacar Itens", 50, function()
	HighlightItems = not HighlightItems
	btn2.Text = HighlightItems and "Remover Destaque" or "Destacar Itens"
	if HighlightItems then
		atualizarItens()
	else
		for _, h in pairs(ItemHighlights) do h:Destroy() end
	end
end)

local btn3 = criarBotao("Puxar Itens", 90, function()
	puxarItens()
end)

local btn4 = criarBotao("Fechar Painel", 130, function()
	GUI:Destroy()
	for _, box in pairs(ESP_Boxes) do box:Remove() end
	for _, h in pairs(ItemHighlights) do h:Destroy() end
end)

-- Inicializar ESP
for _, p in pairs(Players:GetPlayers()) do criarESP(p) end
Players.PlayerAdded:Connect(criarESP)
Players.PlayerRemoving:Connect(removerESP)

game.StarterGui:SetCore("SendNotification", {
	Title = "RP Script";
	Text = "Painel iniciado com sucesso!";
	Duration = 5;
})
