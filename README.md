local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ESPPanel"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Panel = Instance.new("Frame")
Panel.Size = UDim2.new(0, 250, 0, 80)
Panel.Position = UDim2.new(0, 50, 0, 50)
Panel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Panel.BorderSizePixel = 0
Panel.Active = true
Panel.Draggable = true
Panel.Name = "MainPanel"
Panel.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -30, 0, 30)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "ESP Panel"
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 20
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Panel

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 0)
CloseBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
CloseBtn.Text = "X"
CloseBtn.Font = Enum.Font.SourceSansBold
CloseBtn.TextSize = 18
CloseBtn.TextColor3 = Color3.fromRGB(255,255,255)
CloseBtn.Parent = Panel

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(1, -20, 0, 35)
ToggleBtn.Position = UDim2.new(0, 10, 0, 35)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 90, 180)
ToggleBtn.Text = "Ativar ESP"
ToggleBtn.Font = Enum.Font.SourceSansBold
ToggleBtn.TextSize = 18
ToggleBtn.TextColor3 = Color3.fromRGB(255,255,255)
ToggleBtn.Parent = Panel

-- Painel Draggable (compatível com todos dispositivos)
local UIS = game:GetService("UserInputService")
local dragging, dragInput, dragStart, startPos

Panel.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = Panel.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

Panel.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UIS.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		Panel.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-- Botão Fechar
CloseBtn.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)

-- ESP Functions
local ESPEnabled = false

local function getTeamInfo(player)
	local team = player.Team
	return team and team.Name or "Sem time", team and team.TeamColor.Color or Color3.new(1,1,1)
end

local function createESP(player)
	if player.Character and player.Character:FindFirstChild("Head") then
		if player.Character.Head:FindFirstChild("ESPBillboard") then return end

		local billboard = Instance.new("BillboardGui")
		billboard.Name = "ESPBillboard"
		billboard.Adornee = player.Character.Head
		billboard.Size = UDim2.new(0, 200, 0, 40)
		billboard.StudsOffset = Vector3.new(0, 2.5, 0)
		billboard.AlwaysOnTop = true

		local label = Instance.new("TextLabel")
		label.Name = "ESPLabel"
		label.Size = UDim2.new(1, 0, 1, 0)
		label.BackgroundTransparency = 1
		label.TextStrokeTransparency = 0.3
		label.Font = Enum.Font.SourceSansBold
		label.TextSize = 18
		label.TextYAlignment = Enum.TextYAlignment.Top

		local teamName, teamColor = getTeamInfo(player)
		label.Text = player.DisplayName .. " [" .. teamName .. "]"
		label.TextColor3 = teamColor

		label.Parent = billboard
		billboard.Parent = player.Character.Head
	end
end

local function updateESP(player)
	if player.Character and player.Character:FindFirstChild("Head") then
		local gui = player.Character.Head:FindFirstChild("ESPBillboard")
		if gui and gui:FindFirstChild("ESPLabel") then
			local label = gui.ESPLabel
			local teamName, teamColor = getTeamInfo(player)
			label.Text = player.DisplayName .. " [" .. teamName .. "]"
			label.TextColor3 = teamColor
		end
	end
end

local function removeESP(player)
	if player.Character and player.Character:FindFirstChild("Head") then
		local gui = player.Character.Head:FindFirstChild("ESPBillboard")
		if gui then gui:Destroy() end
	end
end

local function addAllESPs()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			createESP(player)
		end
	end
end

local function removeAllESPs()
	for _, player in ipairs(Players:GetPlayers()) do
		if player.Character and player.Character:FindFirstChild("Head") then
			local gui = player.Character.Head:FindFirstChild("ESPBillboard")
			if gui then gui:Destroy() end
		end
	end
end

-- Toggle ESP
ToggleBtn.MouseButton1Click:Connect(function()
	ESPEnabled = not ESPEnabled
	ToggleBtn.Text = ESPEnabled and "Desativar ESP" or "Ativar ESP"
	if not ESPEnabled then
		removeAllESPs()
	end
end)

-- Eventos ESP
Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function()
		wait(1)
		if ESPEnabled and player ~= LocalPlayer then
			createESP(player)
		end
	end)
	player:GetPropertyChangedSignal("Team"):Connect(function()
		updateESP(player)
	end)
	player:GetPropertyChangedSignal("DisplayName"):Connect(function()
		updateESP(player)
	end)
end)

Players.PlayerRemoving:Connect(function(player)
	removeESP(player)
end)

-- Atualização contínua
RunService.RenderStepped:Connect(function()
	if ESPEnabled then
		addAllESPs()
		for _, player in ipairs(Players:GetPlayers()) do
			updateESP(player)
		end
	end
end)
