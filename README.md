local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")

-- Criar GUI para mostrar ESP em cada jogador
local function criarESP(player)
    if player == LocalPlayer then return end
    local char = player.Character
    if not char or not char:FindFirstChild("Head") then return end

    if char.Head:FindFirstChild("ESPLabel") then return end

    local billboard = Instance.new("BillboardGui", char.Head)
    billboard.Name = "ESPLabel"
    billboard.Size = UDim2.new(0, 150, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 2.5, 0)
    billboard.AlwaysOnTop = true

    local label = Instance.new("TextLabel", billboard)
    label.Name = "NameLabel"
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(0, 255, 255)
    label.Font = Enum.Font.GothamBold
    label.TextSize = 14
    label.TextStrokeTransparency = 0.5
    label.Text = player.Name

    -- Mostra time/cargo junto ao nome
    if player.Team then
        label.Text = player.Name .. " [" .. player.Team.Name .. "]"
    end
end

-- Atualizar ESP para todos os jogadores
local function atualizarESP()
    for _, player in pairs(Players:GetPlayers()) do
        criarESP(player)
    end
end

-- Teleporte para hospital (exemplo)
local function teleportarHospital()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(-200, 5, 300) -- troque pelas coords do hospital no seu RP
    end
end

-- Criar menu simples para teleporte e desligar ESP
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "ModMenuRP"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 220, 0, 120)
frame.Position = UDim2.new(0.8, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0

local UICorner = Instance.new("UICorner", frame)
UICorner.CornerRadius = UDim.new(0, 8)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "Mod Menu RP"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local btnTeleport = Instance.new("TextButton", frame)
btnTeleport.Position = UDim2.new(0.1, 0, 0.35, 0)
btnTeleport.Size = UDim2.new(0.8, 0, 0, 30)
btnTeleport.Text = "Teleportar para Hospital"
btnTeleport.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
btnTeleport.TextColor3 = Color3.fromRGB(200, 200, 255)
btnTeleport.Font = Enum.Font.Gotham
btnTeleport.TextSize = 14
btnTeleport.MouseButton1Click:Connect(teleportarHospital)

local btnToggleESP = Instance.new("TextButton", frame)
btnToggleESP.Position = UDim2.new(0.1, 0, 0.7, 0)
btnToggleESP.Size = UDim2.new(0.8, 0, 0, 30)
btnToggleESP.Text = "Ativar ESP"
btnToggleESP.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
btnToggleESP.TextColor3 = Color3.fromRGB(200, 200, 255)
btnToggleESP.Font = Enum.Font.Gotham
btnToggleESP.TextSize = 14

local espAtivo = false
btnToggleESP.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    btnToggleESP.Text = espAtivo and "Desativar ESP" or "Ativar ESP"
    if not espAtivo then
        for _, player in pairs(Players:GetPlayers()) do
            local char = player.Character
            if char and char:FindFirstChild("Head") then
                local espLabel = char.Head:FindFirstChild("ESPLabel")
                if espLabel then
                    espLabel:Destroy()
                end
            end
        end
    else
        atualizarESP()
    end
end)

-- Atualiza ESP sempre que um personagem aparece
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if espAtivo then
            criarESP(player)
        end
    end)
end)

Players.PlayerRemoving:Connect(function(player)
    local char = player.Character
    if char and char:FindFirstChild("Head") then
        local espLabel = char.Head:FindFirstChild("ESPLabel")
        if espLabel then
            espLabel:Destroy()
        end
    end
end)

print("Script do Mod Menu RP rodando. Use o menu na tela para controlar.")

