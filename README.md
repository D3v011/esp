local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local LocalPlayer = Players.LocalPlayer

local ESP_On = false
local TeamCheck = true
local ESP_Boxes = {}

-- Cria GUI na tela
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "ESP_UI"

local frame = Instance.new("Frame", ScreenGui)
frame.Position = UDim2.new(0, 10, 0, 100)
frame.Size = UDim2.new(0, 200, 0, 120)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0.2

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "ESP Painel"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local toggleESP = Instance.new("TextButton", frame)
toggleESP.Position = UDim2.new(0, 10, 0, 40)
toggleESP.Size = UDim2.new(1, -20, 0, 30)
toggleESP.Text = "Ativar ESP"
toggleESP.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toggleESP.TextColor3 = Color3.fromRGB(255,255,255)
toggleESP.Font = Enum.Font.Gotham
toggleESP.TextSize = 14

local toggleTeam = Instance.new("TextButton", frame)
toggleTeam.Position = UDim2.new(0, 10, 0, 80)
toggleTeam.Size = UDim2.new(1, -20, 0, 30)
toggleTeam.Text = "Team Check: ON"
toggleTeam.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toggleTeam.TextColor3 = Color3.fromRGB(255,255,255)
toggleTeam.Font = Enum.Font.Gotham
toggleTeam.TextSize = 14

-- Função para criar ESP
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

-- Remover ESP
function removerESP(player)
    if ESP_Boxes[player] then
        ESP_Boxes[player]:Remove()
        ESP_Boxes[player] = nil
    end
end

-- Atualiza ESP
RunService.RenderStepped:Connect(function()
    if not ESP_On then
        for _, box in pairs(ESP_Boxes) do
            box.Visible = false
        end
        return
    end

    for _, player in pairs(Players:GetPlayers()) do
        local char = player.Character
        local esp = ESP_Boxes[player]

        if char and esp and char:FindFirstChild("HumanoidRootPart") then
            if TeamCheck and player.Team == LocalPlayer.Team then
                esp.Visible = false
            else
                local pos, vis = workspace.CurrentCamera:WorldToViewportPoint(char.HumanoidRootPart.Position)
                esp.Position = Vector2.new(pos.X, pos.Y)
                esp.Text = player.Name
                esp.Visible = vis
            end
        elseif esp then
            esp.Visible = false
        end
    end
end)

-- Botões da GUI
toggleESP.MouseButton1Click:Connect(function()
    ESP_On = not ESP_On
    toggleESP.Text = ESP_On and "Desativar ESP" or "Ativar ESP"
end)

toggleTeam.MouseButton1Click:Connect(function()
    TeamCheck = not TeamCheck
    toggleTeam.Text = TeamCheck and "Team Check: ON" or "Team Check: OFF"
end)

-- Inicializa ESP
for _, player in pairs(Players:GetPlayers()) do
    criarESP(player)
end

Players.PlayerAdded:Connect(criarESP)
Players.PlayerRemoving:Connect(removerESP)

StarterGui:SetCore("SendNotification", {
    Title = "ESP GUI",
    Text = "Interface ativada!",
    Duration = 5
})
