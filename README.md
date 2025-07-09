local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

print("===== SCAN DE JOGADORES E TIMES =====")
for _, player in pairs(Players:GetPlayers()) do
    print("Jogador: "..player.Name)
    if player.Team then
        print("  Time: "..player.Team.Name)
    else
        print("  Sem time")
    end
    if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
        local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
        print(string.format("  Health: %.0f / %.0f", humanoid.Health, humanoid.MaxHealth))
    end
end

print("\n===== ITENS NA MOCHILA DOS JOGADORES =====")
for _, player in pairs(Players:GetPlayers()) do
    local backpack = player:FindFirstChild("Backpack")
    if backpack then
        print("Player: "..player.Name)
        for _, tool in pairs(backpack:GetChildren()) do
            print("  - "..tool.Name.." ("..tool.ClassName..")")
        end
    else
        print("Player: "..player.Name.." não tem mochila")
    end
end

print("\n===== OBJETOS NO WORKSPACE =====")
local count = 0
for _, obj in pairs(Workspace:GetChildren()) do
    count = count + 1
    print(obj.Name.." ("..obj.ClassName..")")
    if count >= 30 then
        print("Mostrando só os primeiros 30 objetos para não lotar o console.")
        break
    end
end

print("\n===== DISTÂNCIA DOS JOGADORES =====")
local lpPos = nil
if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
    lpPos = LocalPlayer.Character.HumanoidRootPart.Position
end

if lpPos then
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (player.Character.HumanoidRootPart.Position - lpPos).Magnitude
            print(string.format("%s está a %.2f metros", player.Name, dist))
        end
    end
else
    print("Não foi possível obter posição do seu personagem.")
end

print("\n===== FIM DO SCAN =====")
