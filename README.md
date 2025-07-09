local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Create GUI
local gui = Instance.new("ScreenGui")
gui.Name = "ScannerGUI"
gui.Parent = game:GetService("CoreGui") -- For exploit use; replace with LocalPlayer.PlayerGui for Studio/official games

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 450, 0, 400)
frame.Position = UDim2.new(0.5, -225, 0.5, -200)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 0
frame.Parent = gui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "🕵️ Scanner Automático de Dados"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = frame

local output = Instance.new("ScrollingFrame")
output.Size = UDim2.new(1, -20, 1, -50)
output.Position = UDim2.new(0, 10, 0, 40)
output.BackgroundColor3 = Color3.fromRGB(20,20,20)
output.BorderSizePixel = 0
output.CanvasSize = UDim2.new(0, 0, 3, 0)
output.ScrollBarThickness = 6
output.Parent = frame

local function createLayout()
    local layout = Instance.new("UIListLayout")
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 4)
    layout.Parent = output
    return layout
end
local layout = createLayout()

-- Função para adicionar texto no output
local function addText(text)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -10, 0, 20)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(200, 200, 200)
    label.Font = Enum.Font.Gotham
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Text = text
    label.Parent = output
end

-- Limpar output e começar scan
output:ClearAllChildren()
layout = createLayout()
addText("Iniciando scan do jogo...")

-- Scan jogadores e times
addText("")
addText("== Jogadores e Times ==")
for _, player in ipairs(Players:GetPlayers()) do
    addText("Jogador: " .. player.Name)
    if player.Team then
        addText(" Time: " .. player.Team.Name)
    else
        addText(" Sem time")
    end
end

-- Scan ferramentas dos jogadores
addText("")
addText("== Ferramentas na mochila ==")
for _, player in ipairs(Players:GetPlayers()) do
    local backpack = player:FindFirstChild("Backpack")
    if backpack then
        local tools = backpack:GetChildren()
        if #tools == 0 then
            addText("Player: " .. player.Name .. " não tem ferramentas.")
        else
            addText("Player: " .. player.Name)
            for _, tool in ipairs(tools) do
                addText(" Item: " .. tool.Name .. " | Tipo: " .. tool.ClassName)
            end
        end
    end
end

-- Scan objetos no workspace
addText("")
addText("== Objetos no Workspace ==")
for _, obj in ipairs(Workspace:GetChildren()) do
    addText("Objeto: " .. obj.Name .. " | Tipo: " .. obj.ClassName)
end

-- Scan posições dos jogadores (distância local)
addText("")
addText("== Posições dos Jogadores ==")
local lpPos = nil
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
    lpPos = LocalPlayer.Character.HumanoidRootPart.Position
end

for _, player in ipairs(Players:GetPlayers()) do
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and lpPos then
        local dist = (player.Character.HumanoidRootPart.Position - lpPos).Magnitude
        addText(player.Name .. " está a " .. math.floor(dist) .. " metros")
    end
end

-- Ajustar canvas dinamicamente
RunService.RenderStepped:Connect(function()
    local totalHeight = 0
    for _, child in ipairs(output:GetChildren()) do
        if child:IsA("TextLabel") then
            totalHeight = totalHeight + child.Size.Y.Offset + layout.Padding.Offset
        end
    end
    output.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
end)
