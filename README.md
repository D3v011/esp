local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "PainelCompletoRP"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 400)
frame.Position = UDim2.new(0.5, -150, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Name = "Painel"

-- Sombra (só aparência)
local shadow = Instance.new("ImageLabel", frame)
shadow.Size = UDim2.new(1, 20, 1, 20)
shadow.Position = UDim2.new(0, -10, 0, -10)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxassetid://9112231196" -- sombra redonda
shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
shadow.ImageTransparency = 0.8
shadow.ZIndex = 0

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "Painel de Mod RP"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.ZIndex = 2

-- Botão fechar
local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 20
closeBtn.ZIndex = 2

closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Container dos botões
local container = Instance.new("ScrollingFrame", frame)
container.Size = UDim2.new(1, -20, 1, -90)
container.Position = UDim2.new(0, 10, 0, 50)
container.BackgroundTransparency = 1
container.BorderSizePixel = 0
container.CanvasSize = UDim2.new(0, 0, 0, 0)
container.ScrollBarThickness = 6
container.ZIndex = 2

local layout = Instance.new("UIListLayout", container)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 10)

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
        local btn = Instance.new("TextButton", container)
        btn.Size = UDim2.new(1, 0, 0, 40)
        btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        btn.AutoButtonColor = true
        btn.TextColor3 = Color3.fromRGB(220, 220, 220)
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 18
        btn.Text = team.Name

        btn.MouseEnter:Connect(function()
            btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
        end)
        btn.MouseLeave:Connect(function()
            btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        end)

        btn.MouseButton1Click:Connect(function()
            mudarTime(team)
        end)
    end
end

-- Botão noclip
local noclipBtn = Instance.new("TextButton", frame)
noclipBtn.Size = UDim2.new(0, 120, 0, 40)
noclipBtn.Position = UDim2.new(0.5, -60, 1, -35)
noclipBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
noclipBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
noclipBtn.Font = Enum.Font.GothamBold
noclipBtn.TextSize = 18
noclipBtn.Text = "Noclip: OFF"
noclipBtn.ZIndex = 2

local noclipAtivo = false
local function setNoclip(active)
    noclipAtivo = active
    noclipBtn.Text = "Noclip: " .. (active and "ON" or "OFF")
end

setNoclip(false)

local function noclipLoop()
    while noclipAtivo do
        RunService.Stepped:Wait()
        local char = LocalPlayer.Character
        if char then
            for _, part in pairs(char:GetChildren()) do
                if part:IsA("BasePart") and part.CanCollide == true then
                    part.CanCollide = false
                end
            end
        end
    end
    -- Restaurar colisão quando desligar noclip
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
    setNoclip(not noclipAtivo)
    if noclipAtivo then
        coroutine.wrap(noclipLoop)()
    end
end)

print("Painel completo rodando! Use para trocar times e ativar noclip.")
