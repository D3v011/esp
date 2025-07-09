-- Este script simula um exploit tentando kikar outro jogador
-- NÃO use isso em servidores públicos — só para testes com seu anti-cheat

local Players = game:GetService("Players")
local TargetName = "JogadorAlvo" -- Nome da vítima

local Target = Players:FindFirstChild(TargetName)

if Target then
    Target:Kick("Você foi expulso por um exploit.")
end
