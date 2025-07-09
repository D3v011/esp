-- Script de exploit para testes (NÃO use publicamente)
-- Simula um hacker matando outro jogador

local Players = game:GetService("Players")
local TargetName = "JogadorAlvo" -- Nome do jogador que você quer "matar"

local target = Players:FindFirstChild(TargetName)

if target and target.Character and target.Character:FindFirstChild("Humanoid") then
    target.Character.Humanoid.Health = 0
end
