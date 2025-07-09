-- ESP simples para Roblox (exibe nome e time acima dos jogadores)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local espOn = true
local espTags = {}

-- Para ativar/desativar ESP, altere a variável espOn acima

RunService.RenderStepped:Connect(function()
    if espOn then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and p.Team then
                if not espTags[p] then
                    local guiTag = Instance.new("BillboardGui", p.Character.Head)
                    guiTag.Name = "TeamESP"
                    guiTag.Size = UDim2.new(0, 100, 0, 30)
                    guiTag.StudsOffset = Vector3.new(0, 2, 0)
                    guiTag.AlwaysOnTop = true
                    local label = Instance.new("TextLabel", guiTag)
                    label.Size = UDim2.new(1, 0, 1, 0)
                    label.BackgroundTransparency = 1
                    label.TextColor3 = Color3.fromRGB(0, 255, 255)
                    label.Font = Enum.Font.GothamBold
                    label.TextSize = 14
                    label.Text = p.Name .. " [" .. p.Team.Name .. "]"
                    espTags[p] = guiTag
                end
            elseif espTags[p] then
                -- Remove se player não está mais válido
                espTags[p]:Destroy()
                espTags[p] = nil
            end
        end
    else
        for _, tag in pairs(espTags) do
            if tag and tag.Parent then tag:Destroy() end
        end
        espTags = {}
    end
end)
