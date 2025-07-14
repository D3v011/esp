local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "TimeSwitcherGUI"
gui.ResetOnSpawn = false

-- Painel principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 250, 0, 300)
frame.Position = UDim2.new(0.5, -125, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = gui

-- Cantos arredondados
local UICorner = Instance.new("UICorner", frame)
UICorner.CornerRadius = UDim.new(0, 10)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, -40, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "📌 Trocar de Time"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextXAlignment = Enum.TextXAlignment.Left
title.Padding = UDim.new(0, 10)

-- Botão de fechar
local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 0, 0)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 16
closeBtn.AutoButtonColor = true
closeBtn.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

-- Área com scroll
local scroll = Instance.new("ScrollingFrame", frame)
scroll.Size = UDim2.new(1, 0, 1, -40)
scroll.Position = UDim2.new(0, 0, 0, 40)
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.ScrollBarThickness = 6
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0

local layout = Instance.new("UIListLayout", scroll)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 5)

scroll.Parent = frame

-- Função para mudar de time
local function mudarTime(time)
	if time then
		LocalPlayer.Team = time
		print("Você mudou para o time: " .. time.Name)
	else
		warn("Time inválido")
	end
end

-- Criar botões para cada time
for _, team in ipairs(Teams:GetChildren()) do
	if team:IsA("Team") then
		local btn = Instance.new("TextButton", scroll)
		btn.Size = UDim2.new(1, -10, 0, 30)
		btn.Position = UDim2.new(0, 5, 0, 0)
		btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		btn.TextColor3 = Color3.fromRGB(230, 230, 230)
		btn.Font = Enum.Font.Gotham
		btn.TextSize = 14
		btn.Text = team.Name
		btn.AutoButtonColor = true

		btn.MouseEnter:Connect(function()
			btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
		end)
		btn.MouseLeave:Connect(function()
			btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		end)

		btn.MouseButton1Click:Connect(function()
			mudarTime(team)
		end)
	end
end

-- Atualiza o tamanho da rolagem
layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	scroll.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10)
end)
