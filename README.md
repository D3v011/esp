-- GUI simples
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FarmButton = Instance.new("TextButton")
local running = false

ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui
Frame.Size = UDim2.new(0, 200, 0, 100)
Frame.Position = UDim2.new(0.5, -100, 0.7, -50)
Frame.BackgroundColor3 = Color3.new(0.2,0.2,0.2)
Frame.Parent = ScreenGui

FarmButton.Text = "INICIAR AUTO GARI"
FarmButton.Size = UDim2.new(0, 180, 0, 60)
FarmButton.Position = UDim2.new(0, 10, 0, 20)
FarmButton.BackgroundColor3 = Color3.new(0,1,0)
FarmButton.TextColor3 = Color3.new(1,1,1)
FarmButton.Parent = Frame

-- Função para simular pressionar F
local function pressF()
    local VirtualInputManager = game:GetService("VirtualInputManager")
    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.F, false, game)
    wait(0.1)
    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.F, false, game)
end

-- Função para encontrar botão pelo texto
local function findButtonByText(text)
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj:FindFirstChildWhichIsA("BillboardGui") then
            for _, gui in ipairs(obj:GetChildren()) do
                if gui:IsA("BillboardGui") then
                    for _, guiObj in ipairs(gui:GetDescendants()) do
                        if guiObj:IsA("TextLabel") or guiObj:IsA("TextButton") then
                            if guiObj.Text and string.lower(guiObj.Text):find(string.lower(text)) then
                                return obj
                            end
                        end
                    end
                end
            end
        end
    end
    return nil
end

function farmGari()
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    while running do
        -- Procurar Saco de Lixo
        local lixo = findButtonByText("Saco de lixo")
        if lixo and running then
            hrp.CFrame = CFrame.new(lixo.Position + Vector3.new(0,2,0))
            wait(0.5)
            pressF()
            wait(0.7)
        end
        -- Procurar Caminhão
        local caminhao = findButtonByText("Caminhão")
        if caminhao and running then
            hrp.CFrame = CFrame.new(caminhao.Position + Vector3.new(0,2,0))
            wait(0.5)
            pressF()
            wait(1)
        end
        wait(0.5)
    end
end

FarmButton.MouseButton1Click:Connect(function()
    running = not running
    if running then
        FarmButton.Text = "PARAR AUTO GARI"
        FarmButton.BackgroundColor3 = Color3.new(1,0,0)
        farmGari()
    else
        FarmButton.Text = "INICIAR AUTO GARI"
        FarmButton.BackgroundColor3 = Color3.new(0,1,0)
    end
end)
