game:GetService("RunService").Stepped:Connect(function()
    local args = {
        [1] = {
            ["\3"] = {
                [1] = {
                    [1] = "Reward4"
                }
            }
        },
        [2] = {}
    }

    game:GetService("ReplicatedStorage").RedEvent:FireServer(unpack(args))
end)
