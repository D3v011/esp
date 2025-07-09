local function scanForRemoteObjects(parent, path)
    path = path or parent.Name
    for _, obj in pairs(parent:GetChildren()) do
        local currentPath = path .. "." .. obj.Name
        if obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
            print("Remote encontrado: ", currentPath, " Tipo: ", obj.ClassName)
        end
        -- Recursivamente escaneia filhos
        scanForRemoteObjects(obj, currentPath)
    end
end

-- Escanear pastas comuns onde esses remotes podem estar
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local PlayerGui = game.Players.LocalPlayer:WaitForChild("PlayerGui")
local StarterGui = game:GetService("StarterGui")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")

print("----- Scan ReplicatedStorage -----")
scanForRemoteObjects(ReplicatedStorage)

print("----- Scan PlayerGui -----")
scanForRemoteObjects(PlayerGui)

print("----- Scan StarterGui -----")
scanForRemoteObjects(StarterGui)

print("----- Scan Workspace -----")
scanForRemoteObjects(Workspace)

print("----- Scan LocalPlayer -----")
scanForRemoteObjects(Players.LocalPlayer)
