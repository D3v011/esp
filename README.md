-- Painel com botão fechar e três variações de botões
local player = game.Players.LocalPlayer

-- Cria gui principal
local gui = Instance.new("ScreenGui")
gui.Name = "AutoGariFarmPainel"
gui.Parent = player.PlayerGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 260, 0, 220)
frame.Position = UDim2.new(0.5, -130, 0.5, -110)
frame.BackgroundColor3 = Color3.fromRGB(40,40,40)
frame.BorderSizePixel = 2
frame.Parent = gui

-- Botão fechar
local closeButton = Instance.new("TextButton")
closeButton.Text = "✕"
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(160,40,40)
closeButton.TextColor3 = Color3.new(1,1,1)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 22
closeButton.Parent = frame
closeButton.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- Botão 1: Clássico
local classicButton = Instance.new("TextButton")
classicButton.Text = "Iniciar/Parar (Clássico)"
classicButton.Size = UDim2.new(0, 220, 0, 40)
classicButton.Position = UDim2.new(0, 20, 0, 45)
classicButton.BackgroundColor3 = Color3.fromRGB(0,170,0)
classicButton.TextColor3 = Color3.new(1,1,1)
classicButton.Font = Enum.Font.SourceSansBold
classicButton.TextSize = 18
classicButton.Parent = frame

-- Botão 2: Com borda
local borderButton = Instance.new("TextButton")
borderButton.Text = "Iniciar/Parar (Borda)"
borderButton.Size = UDim2.new(0, 220, 0, 40)
borderButton.Position = UDim2.new(0, 20, 0, 95)
borderButton.BackgroundColor3 = Color3.fromRGB(40,40,40)
borderButton.BorderColor3 = Color3.fromRGB(0, 170, 255)
borderButton.TextColor3 = Color3.fromRGB(0, 170, 255)
borderButton.Font = Enum.Font.GothamBold
borderButton.TextSize = 18
borderButton.Parent = frame
local borderUICorner = Instance.new("UICorner", borderButton)
local borderShadow = Instance.new("UIStroke", borderButton)
borderShadow.Thickness = 2
borderShadow.Color = Color3.fromRGB(0,170,255)

-- Botão 3: Redondo
local roundButton = Instance.new("TextButton")
roundButton.Text = "Iniciar/Parar (Redondo)"
roundButton.Size = UDim2.new(0, 220, 0, 40)
roundButton.Position = UDim2.new(0, 20, 0, 145)
roundButton.BackgroundColor3 = Color3.fromRGB(255,170,0)
roundButton.TextColor3 = Color3.fromRGB(50,30,0)
roundButton.Font = Enum.Font.FredokaOne
roundButton.TextSize = 18
roundButton.Parent = frame
local roundUICorner = Instance.new("UICorner", roundButton)
roundUICorner.CornerRadius = UDim.new(0, 18)

-- Status
local farmStatus = Instance.new("TextLabel")
farmStatus.Text = "Status: Parado"
farmStatus.Size = UDim2.new(0, 220, 0, 20)
farmStatus.Position = UDim2.new(0, 20, 0, 190)
farmStatus.BackgroundTransparency = 1
farmStatus.TextColor3 = Color3.fromRGB(200,200,200)
farmStatus.Font = Enum.Font.Gotham
farmStatus.TextSize = 14
farmStatus.TextXAlignment = Enum.TextXAlignment.Left
farmStatus.Parent = frame

-- Controles do farm
local running = false
local farmThread = nil

local function startFarm()
    if farmThread then return end
    running = true
    farmStatus.Text = "Status: Ativo"
    farmStatus.TextColor3 = Color3.fromRGB(0, 255, 0)
    -- Exemplo de farm: printa no output, troque pelo seu farm real!
    farmThread = task.spawn(function()
        while running do
            print("Farm rodando...") -- Aqui coloque sua lógica
            task.wait(1)
        end
        farmThread = nil
    end)
end

local function stopFarm()
    running = false
    farmStatus.Text = "Status: Parado"
    farmStatus.TextColor3 = Color3.fromRGB(200,200,200)
end

local function toggleFarm()
    if running then
        stopFarm()
    else
        startFarm()
    end
end

-- Liga/desliga farm em todos os botões
classicButton.MouseButton1Click:Connect(toggleFarm)
borderButton.MouseButton1Click:Connect(toggleFarm)
roundButton.MouseButton1Click:Connect(toggleFarm)

-- DICA: troque o print("Farm rodando...") pela sua função de farm real!
