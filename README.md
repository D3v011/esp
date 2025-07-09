-- ESP Simples com Team Check para Delta
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local ESP_On = true
local TeamCheck = true
local boxes = {}

function criarBox(p)
    if p == LocalPlayer then return end
    local t = Drawing.new("Text")
    t.Size = 14
    t.Center = true
    t.Outline = true
    t.Visible = false
    t.Color = Color3.fromRGB(255,255,255)
    boxes[p] = t
end

function removerBox(p)
    if boxes[p] then boxes[p]:Remove() boxes[p] = nil end
end

for _,p in pairs(Players:GetPlayers()) do criarBox(p) end
Players.PlayerAdded:Connect(criarBox)
Players.PlayerRemoving:Connect(removerBox)

RunService.RenderStepped:Connect(function()
    if not ESP_On then return end
    for i,v in pairs(Players:GetPlayers()) do
        local c = v.Character and v.Character:FindFirstChild("HumanoidRootPart")
        local box = boxes[v]
        if c and box then
            if TeamCheck and v.Team == LocalPlayer.Team then
                box.Visible = false
            else
                local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(c.Position)
                if onScreen then
                    box.Position = Vector2.new(pos.X, pos.Y)
                    box.Text = v.Name
                    box.Visible = true
                else
                    box.Visible = false
                end
            end
        end
    end
end)

-- Hotkeys para ativar/desativar ESP e TeamCheck
game:GetService("UserInputService").InputBegan:Connect(function(i,g)
    if g then return end
    if i.KeyCode == Enum.KeyCode.F1 then
        ESP_On = not ESP_On
        print("ESP: ", ESP_On)
    elseif i.KeyCode == Enum.KeyCode.F2 then
        TeamCheck = not TeamCheck
        print("TeamCheck: ", TeamCheck)
    end
end)
