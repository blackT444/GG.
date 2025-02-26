local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar GUI principal
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)

-- Criar botão "P"
local PButton = Instance.new("TextButton", ScreenGui)
PButton.Size = UDim2.new(0, 50, 0, 50)
PButton.Position = UDim2.new(0.05, 0, 0.85, 0)
PButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
PButton.Text = "P"
PButton.TextScaled = true
PButton.TextColor3 = Color3.new(1, 1, 1)
PButton.BackgroundTransparency = 0.3
PButton.BorderSizePixel = 0
PButton.Font = Enum.Font.SourceSansBold
PButton.AnchorPoint = Vector2.new(0.5, 0.5)
PButton.ClipsDescendants = true
PButton.ZIndex = 2

-- Criar UICorner para tornar o botão circular
local UICorner = Instance.new("UICorner", PButton)
UICorner.CornerRadius = UDim.new(1, 0)

-- Criar menu de jogadores (Lista rolável)
local PlayerListFrame = Instance.new("Frame", ScreenGui)
PlayerListFrame.Size = UDim2.new(0, 200, 0, 300)
PlayerListFrame.Position = UDim2.new(0.05, 0, 0.55, 0)
PlayerListFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
PlayerListFrame.Visible = false
PlayerListFrame.BorderSizePixel = 0

-- Criar ScrollingFrame (para rolar jogadores)
local ScrollFrame = Instance.new("ScrollingFrame", PlayerListFrame)
ScrollFrame.Size = UDim2.new(1, 0, 1, -40)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 5, 0)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.ScrollBarThickness = 5
ScrollFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y

-- Criar UIListLayout para organizar os botões
local UIListLayout = Instance.new("UIListLayout", ScrollFrame)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder

-- Criar botão "Atacar"
local AttackButton = Instance.new("TextButton", PlayerListFrame)
AttackButton.Size = UDim2.new(1, 0, 0, 40)
AttackButton.Position = UDim2.new(0, 0, 1, -40)
AttackButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
AttackButton.Text = "Atacar"
AttackButton.TextScaled = true
AttackButton.TextColor3 = Color3.new(1, 1, 1)
AttackButton.BorderSizePixel = 0
AttackButton.Visible = false

-- Função para escurecer totalmente a visão do alvo
local function BlackoutPlayer(Target)
    if Target and Target.Character then
        local ScreenEffect = Instance.new("ScreenGui", Target:FindFirstChildOfClass("PlayerGui"))
        local BlackScreen = Instance.new("Frame", ScreenEffect)
        BlackScreen.Size = UDim2.new(1, 0, 1, 0)
        BlackScreen.Position = UDim2.new(0, 0, 0, 0)
        BlackScreen.BackgroundColor3 = Color3.new(0, 0, 0)
        BlackScreen.BorderSizePixel = 0
        BlackScreen.ZIndex = 10

        -- Remover efeito após 15 minutos (900 segundos)
        task.wait(900)
        ScreenEffect:Destroy()
    end
end

-- Função para abrir/fechar menu de jogadores
PButton.MouseButton1Click:Connect(function()
    PlayerListFrame.Visible = not PlayerListFrame.Visible
    AttackButton.Visible = false

    -- Limpa lista antes de atualizar
    for _, child in pairs(ScrollFrame:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end

    -- Atualiza lista de jogadores
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local PlayerButton = Instance.new("TextButton", ScrollFrame)
            PlayerButton.Size = UDim2.new(1, 0, 0, 40)
            PlayerButton.Text = player.Name
            PlayerButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            PlayerButton.TextColor3 = Color3.new(1, 1, 1)
            PlayerButton.BorderSizePixel = 0

            -- Selecionar jogador e ativar botão "Atacar"
            PlayerButton.MouseButton1Click:Connect(function()
                AttackButton.Visible = true
                AttackButton:SetAttribute("Target", player) -- Salvar o alvo
            end)
        end
    end
end)

-- Função para atacar jogador escolhido (escurecendo a tela)
AttackButton.MouseButton1Click:Connect(function()
    local Target = AttackButton:GetAttribute("Target")
    if Target then
        print("Atacando:", Target.Name) -- Mensagem no console
        BlackoutPlayer(Target) -- Aplica o efeito de tela escura no jogador por 15 minutos
    end
end)
