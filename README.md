local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ModMenuRP"
gui.ResetOnSpawn = false

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 320, 0, 400)
main.Position = UDim2.new(0.5, -160, 0.5, -200)
main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
main.BorderSizePixel = 0
main.Visible = true
main.Active = true
main.Draggable = true

local UICorner = Instance.new("UICorner", main)
UICorner.CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "🔥 MOD MENU RP 🔥"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 20

-- Fechar botão
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -40, 0, 5)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 0, 0)
close.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
close.Font = Enum.Font.GothamBold
close.TextSize = 18
close.AutoButtonColor = true
close.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

-- Função pra criar botões
local function criarBotao(texto, posY)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(0, 280, 0, 35)
	btn.Position = UDim2.new(0, 20, 0, posY)
	btn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 16
	btn.Text = texto
	btn.AutoButtonColor = true
	btn.ZIndex = 2
	btn.ClipsDescendants = true

	btn.MouseEnter:Connect(function()
		btn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
	end)
	btn.MouseLeave:Connect(function()
		btn.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
	end)

	return btn
end

local y = 60

-- Teleportes
local pontosTeleporte = {
	Hospital = CFrame.new(-200, 5, 300),
	Prefeitura = CFrame.new(100, 10, -150),
	Policia = CFrame.new(50, 5, 50),
	-- Adicione mais locais aqui
}

for nome, cframe in pairs(pontosTeleporte) do
	local btn = criarBotao("📍 Teleportar para " .. nome, y)
	btn.MouseButton1Click:Connect(function()
		if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
			LocalPlayer.Character.HumanoidRootPart.CFrame = cframe
		end
	end)
	y = y + 45
end

-- Noclip toggle
local noclipAtivo = false
local noclipBtn = criarBotao("🚶‍♂️ Noclip: OFF", y)
y = y + 45

local function noclipLoop()
	while noclipAtivo do
		RunService.Stepped:Wait()
		local char = LocalPlayer.Character
		if char then
			for _, part in pairs(char:GetChildren()) do
				if part:IsA("BasePart") then
					part.CanCollide = false
				end
			end
		end
	end
	-- Reativar colisão ao desligar
	local char = LocalPlayer.Character
	if char then
		for _, part in pairs(char:GetChildren()) do
			if part:IsA("BasePart") then
				part.CanCollide = true
			end
		end
	end
end

noclipBtn.MouseButton1Click:Connect(function()
	noclipAtivo = not noclipAtivo
	noclipBtn.Text = "🚶‍♂️ Noclip: " .. (noclipAtivo and "ON" or "OFF")
	if noclipAtivo then
		coroutine.wrap(noclipLoop)()
	end
end)

-- Equipar primeiro item da mochila
local equiparBtn = criarBotao("🎒 Equipar 1º item da mochila", y)
y = y + 45

equiparBtn.MouseButton1Click:Connect(function()
	local backpack = LocalPlayer:FindFirstChild("Backpack")
	if backpack then
		for _, item in pairs(backpack:GetChildren()) do
			if item:IsA("Tool") then
				item.Parent = LocalPlayer.Character
				break
			end
		end
	end
end)

-- ESP toggle
local espOn = false
local espBtn = criarBotao("👀 ESP: OFF", y)
y = y + 45

local espTags = {}

espBtn.MouseButton1Click:Connect(function()
	espOn = not espOn
	espBtn.Text = "👀 ESP: " .. (espOn and "ON" or "OFF")
	if not espOn then
		for _, tag in pairs(espTags) do
			if tag and tag.Parent then
				tag:Destroy()
			end
		end
		espTags = {}
	else
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and p.Team then
				if not espTags[p] then
					local billboard = Instance.new("BillboardGui", p.Character.Head)
					billboard.Name = "ESPTag"
					billboard.Size = UDim2.new(0, 100, 0, 30)
					billboard.StudsOffset = Vector3.new(0, 2, 0)
					billboard.AlwaysOnTop = true

					local label = Instance.new("TextLabel", billboard)
					label.Size = UDim2.new(1, 0, 1, 0)
					label.BackgroundTransparency = 1
					label.TextColor3 = Color3.fromRGB(0, 255, 255)
					label.Font = Enum.Font.GothamBold
					label.TextSize = 14
					label.Text = p.Name .. " [" .. p.Team.Name .. "]"

					espTags[p] = billboard
				end
			end
		end
	end
end)

-- Atualiza ESP ao entrar e sair de jogadores
Players.PlayerAdded:Connect(function(p)
	if espOn then
		p.CharacterAdded:Connect(function()
			if p.Character and p.Character:FindFirstChild("Head") and p.Team then
				if not espTags[p] then
					local billboard = Instance.new("BillboardGui", p.Character.Head)
					billboard.Name = "ESPTag"
					billboard.Size = UDim2.new(0, 100, 0, 30)
					billboard.StudsOffset = Vector3.new(0, 2, 0)
					billboard.AlwaysOnTop = true

					local label = Instance.new("TextLabel", billboard)
					label.Size = UDim2.new(1, 0, 1, 0)
					label.BackgroundTransparency = 1
					label.TextColor3 = Color3.fromRGB(0, 255, 255)
					label.Font = Enum.Font.GothamBold
					label.TextSize = 14
					label.Text = p.Name .. " [" .. p.Team.Name .. "]"

					espTags[p] = billboard
				end
			end
		end)
	end
end)
Players.PlayerRemoving:Connect(function(p)
	if espTags[p] then
		espTags[p]:Destroy()
		espTags[p] = nil
	end
end)
