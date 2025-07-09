local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera
local RunService = game:GetService("RunService")

local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "PainelRP"

-- FRAME PRINCIPAL
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 320)
frame.Position = UDim2.new(0, 10, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0

-- TÍTULO
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "Monitor RP"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.BackgroundTransparency = 1

-- CHECKBOX PARA FILTRO DE CARGOS
local check = Instance.new("TextButton", frame)
check.Size = UDim2.new(1, -10, 0, 25)
check.Position = UDim2.new(0, 5, 0, 35)
check.BackgroundColor3 = Color3.fromRGB(35,35,35)
check.TextColor3 = Color3.fromRGB(255,255,255)
check.Font = Enum.Font.Gotham
check.TextSize = 13
check.Text = "Filtro: Só cargos (clique)"
local filtrarCargos = false

check.MouseButton1Click:Connect(function()
	filtrarCargos = not filtrarCargos
	check.Text = filtrarCargos and "Filtro: cargos ON" or "Filtro: cargos OFF"
end)

-- LISTA DE JOGADORES
local listFrame = Instance.new("ScrollingFrame", frame)
listFrame.Position = UDim2.new(0, 0, 0, 65)
listFrame.Size = UDim2.new(1, 0, 0, 150)
listFrame.CanvasSize = UDim2.new(0, 0, 4, 0)
listFrame.BackgroundTransparency = 1
listFrame.ScrollBarThickness = 6

-- LOCATIONS IMPORTANTES MANUAL (ajuste posição se quiser)
local locais = {
	["Delegacia"] = Vector3.new(100, 5, 100),
	["Hospital"] = Vector3.new(-200, 5, 300),
	["Loja de Armas"] = Vector3.new(150, 5, -120),
	["Praça"] = Vector3.new(0, 5, 0),
}

local localFrame = Instance.new("TextLabel", frame)
localFrame.Position = UDim2.new(0, 0, 0, 225)
localFrame.Size = UDim2.new(1, 0, 0, 95)
localFrame.TextColor3 = Color3.fromRGB(255,255,255)
localFrame.TextSize = 13
localFrame.BackgroundTransparency = 1
localFrame.Font = Enum.Font.Gotham
localFrame.TextXAlignment = Enum.TextXAlignment.Left
localFrame.TextYAlignment = Enum.TextYAlignment.Top
localFrame.Text = "Carregando locais..."

-- Atualiza a lista de jogadores
function atualizarJogadores()
	listFrame:ClearAllChildren()
	local y = 0

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local distancia = math.floor((player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)

			local cargo = ""
			if player.Character:FindFirstChild("Head") then
				for _, tag in pairs(player.Character.Head:GetChildren()) do
					if tag:IsA("BillboardGui") and tag:FindFirstChildWhichIsA("TextLabel") then
						cargo = tag:FindFirstChildWhichIsA("TextLabel").Text
						break
					end
				end
			end

			if not filtrarCargos or (cargo ~= "" and cargo:len() < 50) then
				local texto = player.Name .. " | " .. distancia .. "m"
				if cargo ~= "" then texto = texto .. " - [" .. cargo .. "]" end

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

-- Atualiza locais
function atualizarLocais()
	local texto = "📍 Locais próximos:\n"
	for nome, pos in pairs(locais) do
		local dist = math.floor((pos - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude)
		texto = texto .. "- " .. nome .. " (" .. dist .. "m)\n"
	end
	localFrame.Text = texto
end

-- Notificações de entrada/saída
Players.PlayerAdded:Connect(function(p)
	game.StarterGui:SetCore("SendNotification", {
		Title = "Entrou",
		Text = p.Name .. " conectou-se ao servidor.",
		Duration = 4
	})
end)

Players.PlayerRemoving:Connect(function(p)
	game.StarterGui:SetCore("SendNotification", {
		Title = "Saiu",
		Text = p.Name .. " saiu do servidor.",
		Duration = 4
	})
end)

-- Atualização contínua
RunService.RenderStepped:Connect(function()
	pcall(function()
		atualizarJogadores()
		atualizarLocais()
	end)
end)
