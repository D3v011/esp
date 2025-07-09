local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

local webhookURL = "https://discord.com/api/webhooks/1392368864742080593/b9mxhC30aUABMleHw0fPkD_3Z9LXQYL6Q_wdz4ez8QNjLW-3qkFLQVKignskpNvY1wmx"

local function sendToWebhook(content)
    local data = {
        ["content"] = content
    }
    local jsonData = HttpService:JSONEncode(data)
    local success, err = pcall(function()
        HttpService:PostAsync(webhookURL, jsonData, Enum.HttpContentType.ApplicationJson)
    end)
    if not success then
        warn("Erro ao enviar webhook: ".. tostring(err))
    end
end

local function scanPlayers()
    local msg = "**Lista de jogadores no jogo:**\n"
    for _, player in pairs(Players:GetPlayers()) do
        msg = msg .. "- ".. player.Name
        if player.Team then
            msg = msg .. " (Time: ".. player.Team.Name ..")"
        else
            msg = msg .. " (Sem time)"
        end
        msg = msg .. "\n"
    end
    return msg
end

local function main()
    local content = scanPlayers()
    sendToWebhook(content)
end

main()
