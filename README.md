local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "PainelRPCompleto"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 340, 0, 520)
frame.Position = UDim2.new(0.5, -170, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 42)
title.BackgroundTransparency = 1
title.Text = "Painel RP Completo"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 24

local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -38, 0, 6)
closeBtn.BackgroundColor3 = Color3.fromRGB(170, 40, 40)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 20
closeBtn.AutoButtonColor = true

closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

local container = Instance.new("ScrollingFrame", frame)
container.Size = UDim2.new(1, -20, 1, -70)
container.Position = UDim2.new(0, 10, 0, 48)
container.BackgroundTransparency = 1
container.BorderSizePixel = 0
container.CanvasSize = UDim2.new(0, 0, 0, 0)
container.ScrollBarThickness = 8

local layout = Instance.new("UIListLayout", container)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 10)

local function criarBotao(texto, parent)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(1, 0, 0, 42)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.TextColor3 = Color3.fromRGB(230, 230, 230)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 18
    btn.Text = texto
    btn.AutoButtonColor = true
    btn.ZIndex = 2

    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(75, 75, 75)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    end)

    return btn
end

-- Trocar time --
local timeTitle = Instance.new("TextLabel", container)
timeTitle.Size = UDim2.new(1, 0, 0, 26)
timeTitle.BackgroundTransparency = 1
timeTitle.Text = "Trocar de Time:"
timeTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
timeTitle.Font = Enum.Font.GothamBold
timeTitle.TextSize = 18
timeTitle.LayoutOrder = 0

for _, team in pairs(Teams:GetChildren()) do
    if team:IsA("Team") then
        local btn = criarBotao(team.Name, container)
        btn.LayoutOrder = 1
        btn.MouseButton1Click:Connect(function()
            LocalPlayer.Team = team
            print("Mudou para o time: ".. team.Name)
        end)
    end
end

-- Noclip --
local noclipBtn = criarBotao("Noclip: OFF", container)
noclipBtn.LayoutOrder = 2

local noclipAtivo = false
local noclipConnection

local function setNoclip(status)
    noclipAtivo = status
    noclipBtn.Text = "Noclip: " .. (status and "ON" or "OFF")
end

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
    if noclipAtivo then
        noclipAtivo = false
    else
        noclipAtivo = true
        coroutine.wrap(noclipLoop)()
    end
    setNoclip(noclipAtivo)
end)

-- Teleporte --
local teleportTitle = Instance.new("TextLabel", container)
teleportTitle.Size = UDim2.new(1, 0, 0, 26)
teleportTitle.BackgroundTransparency = 1
teleportTitle.Text = "Teleporte:"
teleportTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
teleportTitle.Font = Enum.Font.GothamBold
teleportTitle.TextSize = 18
teleportTitle.LayoutOrder = 3

-- EXEMPLOS (mude para os locais do seu jogo)
local pontosTeleporte = {
    Hospital = CFrame.new(-200, 5, 300),
    Prefeitura = CFrame.new(100, 10, -150),
    Polícia = CFrame.new(50, 5, 50)
}

for nome, cframe in pairs(pontosTeleporte) do
    local btn = criarBotao("Ir para " .. nome, container)
    btn.LayoutOrder = 4
    btn.MouseButton1Click:Connect(function()
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char.HumanoidRootPart.CFrame = cframe
            print("Teleportado para " .. nome)
        end
    end)
end

-- Puxar itens da mochila (equipar) --
local puxarBtn = criarBotao("Equipar primeiro item da mochila", container)
puxarBtn.LayoutOrder = 5

puxarBtn.MouseButton1Click:Connect(function()
    local backpack = LocalPlayer:FindFirstChild("Backpack")
    if backpack then
        for _, item in pairs(backpack:GetChildren()) do
            if item:IsA("Tool") then
                item.Parent = LocalPlayer.Character
                print("Equipando item: " .. item.Name)
                break
            end
        end
    else
        print("Mochila não encontrada.")
    end
end)

-- Auto pegar dinheiro (exemplo simples) --
local autoPegarBtn = criarBotao("Auto pegar dinheiro (exemplo)", container)
autoPegarBtn.LayoutOrder = 6

local autoPegarAtivo = false
local autoPegarLoop

autoPegarBtn.MouseButton1Click:Connect(function()
    autoPegarAtivo = not autoPegarAtivo
    autoPegarBtn.Text = "Auto pegar dinheiro: " .. (autoPegarAtivo and "ON" or "OFF")
    if autoPegarAtivo then
        autoPegarLoop = coroutine.wrap(function()
            while autoPegarAtivo do
                wait(1)
                local dinheiro = Workspace:FindFirstChild("Dinheiro") -- mudar para nome correto no jogo
                local char = LocalPlayer.Character
                if dinheiro and dinheiro:IsA("BasePart") and char and char:FindFirstChild("HumanoidRootPart") then
                    dinheiro.CFrame = char.HumanoidRootPart.CFrame
                    print("Pegando dinheiro automaticamente")
                end
            end
        end)
        autoPegarLoop()
    end
end)

-- ESP --
local espTitle = Instance.new("TextLabel", container)
espTitle.Size = UDim2.new(1, 0, 0, 26)
espTitle.BackgroundTransparency = 1
espTitle.Text = "ESP:"
espTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
espTitle.Font = Enum.Font.GothamBold
espTitle.TextSize = 18
espTitle.LayoutOrder = 7

local espBtn = criarBotao("ESP: OFF", container)
espBtn.LayoutOrder = 8

local espAtivo = false
local espLabels = {}

local function criarESPLabel(player)
    if espLabels[player] then return end
    local char = player.Character
    if not char or not char:FindFirstChild("Head") then return end

    local billboard = Instance.new("BillboardGui", char.Head)
    billboard.Name = "ESPLabel"
    billboard.Size = UDim2.new(0, 150, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 2.5, 0)
    billboard.AlwaysOnTop = true

    local label = Instance.new("TextLabel", billboard)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(0, 255, 255)
    label.Font = Enum.Font.GothamBold
    label.TextSize = 14

    local text = player.Name
    if player.Team then
        text = text .. " [" .. player.Team.Name .. "]"
    end
    label.Text = text

    espLabels[player] = billboard
end

local function removerESP()
    for player, gui in pairs(espLabels) do
        if gui and gui.Parent then
            gui:Destroy()
        end
    end
    espLabels = {}
end

local function atualizarESP()
    for _, player in pairs(Players:GetPlayers()) do
        criarESPLabel(player)
    end
end

espBtn.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    espBtn.Text = "ESP: " .. (espAtivo and "ON" or "OFF")
    if espAtivo then
        atualizarESP()
    else
        removerESP()
    end
end)

Players.PlayerAdded:Connect(function(player)
    if espAtivo then
        player.CharacterAdded:Connect(function()
            criarESPLabel(player)
        end)
    end
end)
Players.PlayerRemoving:Connect(function(player)
    if espLabels[player] then
        espLabels[player]:Destroy()
        espLabels[player] = nil
    end
end)

-- Atualizar CanvasSize
layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    container.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10)
end)

print("Painel RP Completo rodando! Use o painel para controlar funções.")
