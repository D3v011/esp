local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera
local RunService = game:GetService("RunService")

-- GUI BASE
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "PainelRPv3"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 270, 0, 370)
frame.Position = UDim2.new(0, 10, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "Painel RP 🔍"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.BackgroundTransparency = 1

-- FILTRO DE CARGO
local check = Instance.new("TextButton", frame)
check.Size = UDim2.new(1, -10, 0, 25)
check.Position = UDim2.new(0, 5, 0, 35)
check.BackgroundColor3 = Color3.fromRGB(35,35,35)
check.TextColor3 = Color3.fromRGB(255,255,255)
check.Font = Enum.Font.Gotham
check.TextSize = 13
check.Text = "Filtro: cargos OFF"
local filtrarCargos = false

check.MouseButton1Click:Connect(function()
	filtrarCargos = not filtrarCargos
	check.Text = filtrarCargos and "Filtro: cargos ON" or "Filtro: cargos OFF"
end)

-- LISTA DE JOGADORES
local listFrame = Instance.new("ScrollingFrame", frame)
listFrame.Position = UDim2.new(0, 0, 0, 65)
listFrame.Size = UDim2.new(1, 0, 0, 140)
listFrame.CanvasSize = UDim2.new(0, 0, 4, 0)
listFrame.BackgroundTransparency = 1
listFrame.ScrollBarThickness = 6

-- LOCAIS IMPORTANTES (você pode editar os vetores conforme seu mapa)
local locais = {
	["Delegacia"] = Vector3.new(100, 5, 100),
	["Hospital"] = Vector3.new(-200, 5, 300),
	["Loja de Armas"] = Vector3.new(150, 5, -120),
	["Praça Central"] = Vector3.new(0, 5, 0),
}

-- FRAME DE LOCAIS
local locaisFrame = Instance.new("Frame", frame)
locaisFrame.Position = UDim2.new(0, 0, 0, 210)
locaisFrame.Size = UDim2.new(1, 0, 0, 150)
locaisFrame.BackgroundTransparency = 1

local locaisLabel = Instance.new("TextLabel", locaisFrame)
locaisLabel.Size = UDim2.new(1, 0, 0, 20)
locaisLabel.Text = "📍 Locais Importantes"
locaisLabel.Font = Enum.Font.GothamBold
locaisLabel.TextColor3 = Color3.fromRGB(255,255,255)
locaisLabel.TextSize = 14
locaisLabel.BackgroundTransparency = 1

local y = 25
for nome, pos in pairs(locais) do
	local btn = Instance.new("TextButton", locaisFrame)
	btn.Size = UDim2.new(1, -10, 0, 25)
	btn.Position = UDim2.new(0, 5, 0, y)
	btn.Text = nome
	btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
	btn.TextColor3 = Color3.fromRGB(255,255,255)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 13

	btn.MouseButton1Click:Connect(function()
		if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
			LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
		end
	end)

	y = y + 30
end

locaisFrame.CanvasSize = UDim2.new(0, 0, 0, y)

-- ATUALIZA JOGADORES
function atualizarJogadores()
	listFrame:ClearAllChildren()
	local y = 0

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local distancia = math.floor((player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
			local cargo = player.Team and player.Team.Name or "Sem time"

			-- Adiciona tag flutuante em cima da cabeça
			if player.Character:FindFirstChild("Head") and not player.Character.Head:FindFirstChild("TimeTag") then
				local guiTag = Instance.new("BillboardGui", player.Character.Head)
				guiTag.Name = "TimeTag"
				guiTag.Size = UDim2.new(0, 100, 0, 30)
				guiTag.StudsOffset = Vector3.new(0, 2, 0)
				guiTag.AlwaysOnTop = true

				local texto = Instance.new("TextLabel", guiTag)
				texto.Size = UDim2.new(1, 0, 1, 0)
				texto.BackgroundTransparency = 1
				texto.TextColor3 = Color3.fromRGB(0, 255, 255)
				texto.Font = Enum.Font.GothamBold
				texto.TextSize = 14
				texto.Text = cargo
			end

			if not filtrarCargos or (cargo ~= "Sem time") then
				local texto = player.Name .. " | " .. distancia .. "m - [" .. cargo .. "]"

				local label = Instance.new("TextLabel", listFrame)
				label.Position = UDim2.new(0, 0, 0, y)
				label.Size = UDim2.new(1, -10, 0, 25)
				label.BackgroundTransparency = 1
				label.TextColor3 = Color3.fromRGB(200, 200, 200)
				label.Font = Enum.Font.Gotham
				label.TextSize = 14
				label.Text = texto
				y = y + 26
			end
		end
	end

	listFrame.CanvasSize = UDim2.new(0, 0, 0, y)
end

-- NOTIFICAÇÕES DE ENTRADA/SAÍDA
Players.PlayerAdded:Connect(function(p)
	game.StarterGui:SetCore("SendNotification", {
		Title = "Entrou",
		Text = p.Name .. " conectou-se.",
		Duration = 4
	})
end)

Players.PlayerRemoving:Connect(function(p)
	game.StarterGui:SetCore("SendNotification", {
		Title = "Saiu",
		Text = p.Name .. " saiu.",
		Duration = 4
	})
end)

-- LOOP
RunService.RenderStepped:Connect(function()
	pcall(function()
		atualizarJogadores()
	end)
end)
