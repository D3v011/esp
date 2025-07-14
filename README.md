local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "TimeSwitcherGUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 40)
frame.Position = UDim2.new(0.5, -125, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.BorderSizePixel = 0
frame.ClipsDescendants = true

local layout = Instance.new("UIListLayout", frame)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 5)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "Trocar de Time"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.LayoutOrder = 0

-- Função para mudar time
local function mudarTime(time)
    if time then
        LocalPlayer.Team = time
        print("Você mudou para o time: " .. time.Name)
    else
        warn("Time inválido")
    end
end

-- Criar botão para cada time
for _, team in pairs(Teams:GetChildren()) do
    if team:IsA("Team") then
        local btn = Instance.new("TextButton", frame)
        btn.Size = UDim2.new(1, -10, 0, 30)
        btn.Position = UDim2.new(0, 5, 0, 0)
        btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        btn.TextColor3 = Color3.fromRGB(200, 200, 200)
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 16
        btn.Text = team.Name
        btn.LayoutOrder = 1

        btn.MouseButton1Click:Connect(function()
            mudarTime(team)
        end)
    end
end

-- Ajustar a altura do frame conforme a quantidade de times
local totalButtons = #Teams:GetChildren()
frame.Size = UDim2.new(0, 250, 0, 30 + (totalButtons * 35))
frame.Position = UDim2.new(0.5, -125, 0.4, 0)
