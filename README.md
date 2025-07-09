-- GUI básica
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FarmButton = Instance.new("TextButton")
local running = false

ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui
Frame.Size = UDim2.new(0, 200, 0, 100)
Frame.Position = UDim2.new(0.5, -100, 0.7, -50)
Frame.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
Frame.Parent = ScreenGui

FarmButton.Text = "INICIAR FARM GARI"
FarmButton.Size = UDim2.new(0, 180, 0, 60)
FarmButton.Position = UDim2.new(0, 10, 0, 20)
FarmButton.BackgroundColor3 = Color3.new(0,1,0)
FarmButton.TextColor3 = Color3.new(1,1,1)
FarmButton.Parent = Frame

-- Função para buscar objetos com nomes parecidos
local function findObjectByName(keywords)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") or obj:IsA("Model") then
            for _, word in ipairs(keywords) do
                if string.lower(obj.Name):find(word) then
                    return obj
                end
            end
        end
    end
    return nil
end

-- Função principal de farm
function farmGari()
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    -- Procurar objetos prováveis de lixo e lixeira
    local lixoKeywords = {"lixo", "garbage", "trash", "bag", "saco"}
    local lixeiraKeywords = {"lixeira", "bin", "trashcan", "coletor"}
    while running do
        local lixo = findObjectByName(lixoKeywords)
        local lixeira = findObjectByName(lixeiraKeywords)
        if lixo and lixeira then
            -- Ir até o lixo
            char:MoveTo(lixo.Position or lixo.PrimaryPart.Position)
            repeat wait(0.1) until (char.PrimaryPart.Position - (lixo.Position or lixo.PrimaryPart.Position)).magnitude < 5 or not running
            pcall(function()
                firetouchinterest(char.PrimaryPart, lixo, 0)
            end)
            wait(0.3)
            -- Ir até a lixeira
            char:MoveTo(lixeira.Position or lixeira.PrimaryPart.Position)
            repeat wait(0.1) until (char.PrimaryPart.Position - (lixeira.Position or lixeira.PrimaryPart.Position)).magnitude < 5 or not running
            pcall(function()
                firetouchinterest(char.PrimaryPart, lixeira, 0)
            end)
            wait(1)
        else
            warn("Não encontrou lixo ou lixeira!")
            wait(2)
        end
    end
end

-- Botão de ativação/desativação
FarmButton.MouseButton1Click:Connect(function()
    running = not running
    if running then
        FarmButton.Text = "PARAR FARM"
        FarmButton.BackgroundColor3 = Color3.new(1,0,0)
        farmGari()
    else
        FarmButton.Text = "INICIAR FARM GARI"
        FarmButton.BackgroundColor3 = Color3.new(0,1,0)
    end
end)
