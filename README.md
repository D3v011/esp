local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criação da GUI principal
local gui = Instance.new("ScreenGui")
gui.Name = "PainelModMenuArrastavel"
gui.Parent = LocalPlayer.PlayerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 120)
frame.Position = UDim2.new(0.5, -150, 0.5, -60)
frame.BackgroundColor3 = Color3.fromRGB(40,40,40)
frame.Active = true
frame.Draggable = true -- Torna o painel arrastável
frame.Parent = gui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = frame

local title = Instance.new("TextLabel")
title.Parent = frame
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundTransparency = 1
title.Text = "🔥 MOD MENU 🔥"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16

-- Botão fechar
local closeButton = Instance.new("TextButton")
closeButton.Parent = frame
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.Text = "✕"
closeButton.TextColor3 = Color3.fromRGB(255, 0, 0)
closeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 18
closeButton.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Botão de velocidade
local speedBtn = Instance.new("TextButton")
speedBtn.Parent = frame
speedBtn.Position = UDim2.new(0, 20, 0, 60)
speedBtn.Size = UDim2.new(0, 260, 0, 40)
speedBtn.Text = "Ativar velocidade"
speedBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
speedBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
speedBtn.Font = Enum.Font.Gotham
speedBtn.TextSize = 16

local running = false

speedBtn.MouseButton1Click:Connect(function()
    running = not running
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = running and 50 or 16
    end
    speedBtn.Text = running and "Desativar velocidade" or "Ativar velocidade"
end)
