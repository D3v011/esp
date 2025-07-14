local Players = game:GetService("Players")
local Teams = game:GetService("Teams")
local LocalPlayer = Players.LocalPlayer

-- Criação da aba flutuante
local abaTime = Instance.new("Frame")
abaTime.Size = UDim2.new(0, 250, 0, 300)
abaTime.Position = UDim2.new(0.5, -125, 0.5, -150)
abaTime.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
abaTime.BorderSizePixel = 0
abaTime.Visible = false
abaTime.Active = true
abaTime.Draggable = true
abaTime.Parent = game.CoreGui

local corner = Instance.new("UICorner", abaTime)
corner.CornerRadius = UDim.new(0, 10)

-- Título da aba
local titulo = Instance.new("TextLabel", abaTime)
titulo.Size = UDim2.new(1, -40, 0, 40)
titulo.Position = UDim2.new(0, 10, 0, 0)
titulo.BackgroundTransparency = 1
titulo.Text = "📌 Trocar de Time"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.Font = Enum.Font.GothamBold
titulo.TextSize = 18
titulo.TextXAlignment = Enum.TextXAlignment.Left

-- Botão de fechar aba
local fechar = Instance.new("TextButton", abaTime)
fechar.Size = UDim2.new(0, 30, 0, 30)
fechar.Position = UDim2.new(1, -35, 0, 5)
fechar.Text = "X"
fechar.TextColor3 = Color3.fromRGB(255, 0, 0)
fechar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
fechar.Font = Enum.Font.GothamBold
fechar.TextSize = 16
fechar.MouseButton1Click:Connect(function()
	abaTime.Visible = false
end)

-- Área com scroll
local scroll = Instance.new("ScrollingFrame", abaTime)
scroll.Size = UDim2.new(1, -20, 1, -50)
scroll.Position = UDim2.new(0, 10, 0, 45)
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.ScrollBarThickness = 6
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0

local layout = Instance.new("UIListLayout", scroll)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 5)

-- Função para mudar de time
local function mudarTime(time)
	if time then
		LocalPlayer.Team = time
	end
end

-- Criar botões para cada time
for _, team in ipairs(Teams:GetChildren()) do
	if team:IsA("Team") then
		local btn = Instance.new("TextButton", scroll)
		btn.Size = UDim2.new(1, 0, 0, 30)
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

layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	scroll.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 10)
end)

-- 🟢 BOTÃO no seu menu principal
-- (adicione isso ao final do seu script do MOD MENU principal)

local abrirAbaTimeBtn = Instance.new("TextButton", main) -- main = painel do mod menu
abrirAbaTimeBtn.Position = UDim2.new(0, 20, 0, 210)
abrirAbaTimeBtn.Size = UDim2.new(0, 260, 0, 35)
abrirAbaTimeBtn.Text = "👥 Trocar de Time"
abrirAbaTimeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
abrirAbaTimeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
abrirAbaTimeBtn.Font = Enum.Font.Gotham
abrirAbaTimeBtn.TextSize = 14
abrirAbaTimeBtn.MouseButton1Click:Connect(function()
	abaTime.Visible = not abaTime.Visible
end)
